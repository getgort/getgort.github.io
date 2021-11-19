# Output Format Templates


Output formatting templates are very powerful.

## Template Basic Format

Gort templates use Go's [template syntax](https://pkg.go.dev/text/template).

A very simple template might look something like the following:

```
{% raw %}{{ text | monospace true }}{{ .Response.Out }}{{ endtext }}{% endraw %}
```

This template emits the command's response as monospaced text.

But what's `.Response.Out`? That's part of the response envelope.

## The Response Envelope

The response envelope encapsulates all of the data and metadata around a command request, execution, and response. It's passed into the templating engine, where it can be accessed directly by templates.

The response envelope consists of four major components:

* `.Request` -- This describes the original request used to execute the command, and contains all of the original command values, and some data about the user and adapter.
* `.Response` -- Contains the textual response emitted by the command.
* `.Data` -- This object contains metadata about the command execution, including its duration and exit status.
* `.Payload` -- If the command output is valid JSON, it will be unmarshalled and placed here to be accessed by templates. Non-JSON output will also be placed here, as a plain string.

These components (and any sub-components, where relevant) are detailed below.

### .Request 

The `.Request` object represents the original command request used to execute the command. It contains the original command values as well as the user and adapter data.

| Syntax | Type | Description |
| ------ | ---- | ----------- |
| `.Request.Adapter` | string | The name of the adapter this request originated from |
| `.Request.ChannelID` | string | The provider ID of the channel that the request originated in |
| `.Request.Parameters` | []string | Tokenized command parameters |
| `.Request.RequestID` | int64  | A unique requestID |
| `.Request.Timestamp` | time.Time | The time this request was triggered |
| `.Request.UserID` | string | The provider ID of user making this request |
| `.Request.UserEmail` | string | The email address associated with the user making the request |
| `.Request.UserName` | string | The Gort username of the user making the request |

### .Request.Bundle

The `.Request.Bundle` object represents the bundle that owns the command.

| Syntax | Type | Description |
| ------ | ---- | ----------- |
| `.Request.Bundle.Name` | string | 
| `.Request.Bundle.Version` | string | 
| `.Request.Bundle.Enabled` | bool | 
| `.Request.Bundle.Author` | string | 
| `.Request.Bundle.Homepage` | string | 
| `.Request.Bundle.Description` | string | 
| `.Request.Bundle.InstalledOn` | time.Time | 
| `.Request.Bundle.InstalledBy` | string | 
| `.Request.Bundle.LongDescription` | string | 

### .Request.Command

The `.Request.Command` object represents the command definition.

| Syntax | Type | Description |
| ------ | ---- | ----------- |
| `.Request.Command.Description` | string | 
| `.Request.Command.Executable` | []string | 
| `.Request.Command.LongDescription` | string | 
| `.Request.Command.Name` | string | 
| `.Request.Command.Rules` | []string | 

### .Response

The `.Response` object contains the response text emitted by an executed command.

| Syntax | Type | Description |
| ------ | ---- | ----------- |
| `.Response.Lines` | []string | The command output (from both stdout and stderr) as a string slice, delimitted along newlines.
| `.Response.Out` | string | The command output as a single block of text, with lines joined with newlines.
| `.Response.Structured` | bool | `true` if the command output is valid JSON. If so, then it also be unmarshalled as `.Payload`; else `.Payload` will be a string (equal to `.Response.Out`).
| `.Response.Title` | string | A title. Usually only set by the relay for certain internally-detected errors. Generally a short description of the result.

### .Data

The `.Data` object contains about the command execution, including its duration and exit status. If the relay sets an an explicit internal error, it will be here as well.

| Syntax | Type | Description |
| ------ | ---- | ----------- |
| `.Data.Duration` | time.Duration | Duration is how long the command required to execute.
| `.Data.ExitCode` | int16 | ExitCode is the exit code reported by the command.
| `.Data.Error` | error | Error is set by the relay under certain internal error conditions.

### .Payload

`.Payload` includes the command output. It's a very special animal, because its contents can very according to the contents and format of the response returned by the command.

Specifically, if the command output is formatted as structured JSON, the output will be unmarshalled and made accessible via `.Payload` as if were any other object. Additionally, the value of `.Response.Structured` will be `true`.

For example, if the contents of the command response are as follows:

```json
{
   "User":"Michael Scott",
   "Company":"Dunder Mifflin",
   "Results":[
      {
         "Name":"Bond",
         "Reviews":523,
         "Description":"Bond paper is stronger and more durable than the average sheet of paper.",
         "Image":"https://dunder-mifflin/bond.jpg"
      }, {
         "Name":"Gloss coated",
         "Reviews":1234,
         "Description":"Gloss paper is typically used for flyers and brochures as it has a high shine.",
         "Image":"https://dunder-mifflin/gloss-coated.jpg"
      }
   ]
}
```

So a template containing the instructions `{% raw %}{{.Payload.User}}, {{.Payload.Company}}{% endraw %}` would resolve as `Michael Scott, Dunder Mifflin`.

If the response isn't structured, `.Response.Structured` will be `false`, and `.Payload` will be a standard string equal to `.Response.Out`. 
