# Template Functions

Output format templating is a powerful feature that allows you to control the look and feel (and, to some degree, content) of any information sent to users. Gort templates are based on [Go templates](https://pkg.go.dev/text/template), and support all of their features, including variables, logic, and flow control.

There are three main types of elements used to construct elements:

* **Tags:** Tags (or "actions") represents visual elements or directives, such as `{% raw %}{{ text }}{% endraw %}` or `{% raw %}{{ image }}{% endraw %}`. All text and directives must be enclosed within (or between) tags.

* **Functions:** Functions are used to modify the contents or behavior of tags. They're called using pipes (`|`) within a tag. For example, `{% raw %}{{ image "foo.jpg" | thumbnail true }}{% endraw %}` turns an image into a thumbnail image. Some functions can only be used with specific tags. [Sprig](https://masterminds.github.io/sprig/) functions are also supported.

* **Fields:** A call into a data value, typically the [the Response Envelope](templates-response-envelope.md).

The supported elements are detailed below.

## Tags (Actions)

### `{% raw %}{{alt}}{% endraw %}`

Provides alternative text to be shown if other elements in a message cannot be rendered. Only the first instance of `{% raw %}{{alt}}{% endraw %}` will be shown.

Example: `{% raw %}{{ alt "This is alternative text." }}{% endraw %}`

### `{% raw %}{{divider}}{% endraw %}`

Emits a simple divider, used to break up blocks. 

### `{% raw %}{{ header }}{% endraw %}`

Can be used to decorate or modify the behavior of the entire template. If a `{% raw %}{{ header }}{% endraw %}` is used, it must be the first line of the template.

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `color`     | Adds a colored sidebar to the output block. Must be a hexadecimal string with the format `#RRGGBB`. | `{% raw %}{{ header \| color "#FF0000" }}{% endraw %}` |
| `title`     | Adds a title to the output block. | `{% raw %}{{ header \| title "Error?" }}{% endraw %}` |


### `{% raw %}{{text}}{% endraw %}` and `{% raw %}{{endtext}}{% endraw %}`

Used to describe a text block or element. They may be used inside of a  `{% raw %}{{section}}{% endraw %}`/`{% raw %}{{endsection}}{% endraw %}` pair.

Example: `{% raw %}{{ text }}This is a plain text block.{{ endtext }}{% endraw %}`

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `inline`    | Makes the text "inline" (in the Discord sense). If true, a `title` is also expected. | `{% raw %}{{ text \| inline true \| title "Favorite Food" }}Pizza{{ endtext }}{% endraw %}` |
| `monospace` | All text in the block is monospaced. | `{% raw %}{{ text \| monospace true }}THIS IS CODE.{{ endtext }}{% endraw %}` |
| `title`     | Adds a title to the text block.  | `{% raw %}{{ text \| title "Favorite Food" }}Pizza{{ endtext }}{% endraw %}` |


### `{% raw %}{{section}}{% endraw %}` and `{% raw %}{{endsection}}{% endraw %}`

Sections can be used to group elements together. These are only supported in Slack; they are ignored in Discord.

These are often used with a `range` loop of some kind over a collection:

```
{% raw %}{{ text }}Here are your results:{{ endtext }}
{{ range $index, $loc := .Payload.Locations }}
{{ section }}
{{ text | title $loc.Title | inline true }}$loc.Name{{ endtext }}
{{ image $loc.Image | thumbnail true }}
{{ endsection }}
{{ end }}{% endraw %}
```


### `{% raw %}{{image}}{% endraw %}`

Outputs an image whose URL is specified in the argument. They may be used inside of a  `{% raw %}{{section}}{% endraw %}`/`{% raw %}{{endsection}}{% endraw %}` pair.

Example: `{% raw %}{{ image "https://example.com/img/image1.png" }}{% endraw %}`

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `thumbnail` | Causes the image to be presented as a thumbnail (usually for a block or section). | `{% raw %}{{ image .Payload.Image \| thumbnail true }}{% endraw %}` |

## Additional Functions

In addition to the above, all of the [Sprig library](https://masterminds.github.io/sprig/) functions are supported as well.
