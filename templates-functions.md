# Template Functions

Output format templating is a powerful feature that allows you to control the look and feel (and, to some degree, content) of any information sent to users. Gort templates are based on [Go templates](https://pkg.go.dev/text/template), and support all of their features, including variables, logic, and flow control.

There are three main types of elements used to construct elements:

* **Tags:** Tags (or "actions") represents visual elements or directives, such as `{{ text }}` or `{{ image }}`. All text and directives must be enclosed within (or between) tags.

* **Functions:** Functions are used to modify the contents or behavior of tags. They're called using pipes (`|`) within a tag. For example, `{{ image "foo.jpg" | thumbnail true }}` turns an image into a thumbnail image. Some functions can only be used with specific tags. [Sprig](https://masterminds.github.io/sprig/) functions are also supported.

* **Fields:** A call into a data value, typically the [the Response Envelope](templates-response-envelope.md).

The supported elements are detailed below.

## Tags (Actions)

### `{{ header }}`

Can be used to decorate or modify the behavior of the entire template. If a `{{ header }}` is used, it must be the first line of the template.

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `color`     | Adds a colored sidebar to the output block. Must be a hexadecimal string with the format `#RRGGBB`. | `{{ header \| color "#FF0000" }}` |
| `title`     | Adds a title to the output block. | `{{ header \| title "Error?" }}` |


### `{{text}}` and `{{endtext}}`

Used to describe a text block or element. They may be used inside of a  `{{section}}`/`{{endsection}}` pair.

Example: `{{ text }}This is a standard text block.{{ endtext }}`

| Function    | Description | Example |
| ----------- | ----------- | ------- |
| `inline`    | Makes the text "inline" (in the Discord sense). If true, a `title` is also expected. | `{{ text \| inline true \| title "Favorite Food" }}Pizza{{ endtext }}` |
| `monospace` | All text in the block is monospaced. | `{{ text \| monospace true }}THIS IS CODE.{{ endtext }}` |
| `title`     | Adds a title to the text block.  | `{{ text \| title "Favorite Food" }}Pizza{{ endtext }}` |

### `{{section}}` and `{{endsection}}`

The `{{section}}` and `{{endsection}}` tag

#### Section Functions



### `{{divider}}`

The `{{divider}}` tag



### `{{image}}`

The `{{image}}` tag



### `{{alt}}`

The `{{alt}}` tag

## Functions

## Fields
