# Template Functions

Output format templating is a powerful feature that allows you to control the look and feel (and, to some degree, content) of any information sent to users. Gort templates are based on [Go templates](https://pkg.go.dev/text/template), and support all of their features, including variables, logic, and flow control.

There are three main types of elements used to construct elements:

* **Tags:** Tags (or "actions") represents visual elements or directives, such as `{{ text }}` or `{{ image }}`. All text and directives must be enclosed within (or between) tags.

* **Functions:** Functions are used to modify the contents or behavior of tags. They're called using pipes (`|`) within a tag. For example, `{{ image "foo.jpg" | thumbnail true }}` turns an image into a thumbnail image. Some functions can only be used with specific tags. [Sprig](https://masterminds.github.io/sprig/) functions are also supported.

* **Fields:** A call into a data value, typically the [the Response Envelope](templates-response-envelope.md).

The supported elements are detailed below.

## Tags (Actions)

### `{{alt}}`

Provides alternative text to be shown if other elements in a message cannot be rendered. Only the first instance of `{{alt}}` will be shown.

Example: `{{ alt "This is alternative text." }}`

### `{{divider}}`

Emits a simple divider, used to break up blocks. 

### `{{ header }}`

Can be used to decorate or modify the behavior of the entire template. If a `{{ header }}` is used, it must be the first line of the template.

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `color`     | Adds a colored sidebar to the output block. Must be a hexadecimal string with the format `#RRGGBB`. | `{{ header \| color "#FF0000" }}` |
| `title`     | Adds a title to the output block. | `{{ header \| title "Error?" }}` |


### `{{text}}` and `{{endtext}}`

Used to describe a text block or element. They may be used inside of a  `{{section}}`/`{{endsection}}` pair.

Example: `{{ text }}This is a plain text block.{{ endtext }}`

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `inline`    | Makes the text "inline" (in the Discord sense). If true, a `title` is also expected. | `{{ text \| inline true \| title "Favorite Food" }}Pizza{{ endtext }}` |
| `monospace` | All text in the block is monospaced. | `{{ text \| monospace true }}THIS IS CODE.{{ endtext }}` |
| `title`     | Adds a title to the text block.  | `{{ text \| title "Favorite Food" }}Pizza{{ endtext }}` |


### `{{section}}` and `{{endsection}}`

Sections can be used to group elements together. These are only supported in Slack; they are ignored in Discord.

These are often used with a `range` loop of some kind over a collection:

```
{{ text }}Here are your results:{{ endtext }}
{{ range $index, $loc := .Payload.Locations }}
{{ section }}
{{ text | title $loc.Title | inline true }}$loc.Name{{ endtext }}
{{ image $loc.Image | thumbnail true }}
{{ endsection }}
{{ end }}
```


### `{{image}}`

Outputs an image whose URL is specified in the argument. They may be used inside of a  `{{section}}`/`{{endsection}}` pair.

Example: `{{ image "https://example.com/img/image1.png" }}`

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `thumbnail` | Causes the image to be presented as a thumbnail (usually for a block or section). | `{{ image .Payload.Image \| thumbnail true }}` |

## Additional Functions

In addition to the above, all of the [Sprig library](https://masterminds.github.io/sprig/) functions are supported as well.