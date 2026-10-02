<h1 align="center">kanverter.js</h1>

<p align="center"><b>A source-derived <code>kanagawa.nvim</code> Lua-to-Vim9 colorscheme converter, written in Node.js</b></p>

I use Vim and Neovim side by side (sometimes literally) and I really like the `rebelot/kanagawa.nvim` colorscheme, although it isn't available in its full glory in the Vim ecosystem.\
So, I decided to fix that, and here it is — a JavaScript converter that takes the original Lua code from the latest commit and translates it into a Vim9 colorscheme.

## :triangular_ruler: Design / Architecture

The main goal of `kanverter.js` is to keep the generated Vim colorscheme as close to the original `kanagawa.nvim` as possible. To achieve this, the converter works directly with the Lua definitions rather than maintaining a separate implementation.

The conversion pipeline looks like this:

1. Fetch and extract the source.

    - The palette, theme bindings, and highlight group definitions are fetched from the upstream repository.\
    The raw source is then sliced down to the parts needed for the conversion.

2. Convert the palette to JSON.

    - The extracted Lua palette table is converted to JSON using regular expressions. The resulting palette object provides the color values used by the following stages.

3. Resolve the theme colors.

    - The appropriate theme table is converted in a similar way. Its color names are replaced with the corresponding hexadecimal values from the parsed palette.\
    The resulting theme is then converted to JSON as well.

4. Generate the Vim colorscheme.

    - The highlight group bindings from the relevant source files are combined and converted into Vim9 colorscheme syntax. Their color and style values are replaced with the resolved values from the previous stage and adapted accoring to the user's configuration.\
    LSP-related and Neovim-specific groups are excluded, as they are not applicable to standard Vim.

The regular expressions used for the conversion are designed to tolerate changes in the upstream source.\
When the source changes, simply running the converter again produces an updated Vim version.

## :building_construction: Installation

- Clone the repository:

```bash
git clone https://github.com/distant-chervony/kanverter.js
```

## :wrench: Configuration

The tool reads a `config.json` file, located at the root of the repository.\
The location and name of the file could be changed using the `--config|-c` flag and passing the path to the JSON-formatted file as an argument.

The entries in `config.json`, their default values, and their behavior mimic the configuration of `kanagawa.nvim`. At least, I've implemeted the options I've considered important for the colorscheme in standard Vim.

### Defaults

The default configuration file supplied with the tool looks like this:

```json
{
  "undercurl": true,
  "commentStyle": "italic",
  "functionStyle": "",
  "keywordStyle": "italic",
  "statementStyle": "bold",
  "typeStyle": "",
  "transparent": false,
  "dimInactive": false,
  "terminalColors": true
}
```

> [!NOTE]
> The string values should be either `bold`, `italic`, or empty (which is treated as `NONE` in the Vim colorscheme).

> [!NOTE]
> If you want something to appear *both* bold and italic, you need to write both values separated by a comma (e.g., `bold,italic`).

## :joystick: Using `kanverter.js`

### Quick Start

The tool has the following flags:

> [!TIP]
> You can always pass the `--help|-h` flag to `kanverter.js` to see the help menu, e.g. `./kanverter -h`.

- `--theme|-t`
    - Accepts a string value, representing one of the available themes from the original colorscheme.\
    The default is `wave`, so this option can be omitted when fetching the default variant.
- `--config|-c`  *Optional*
    - Accepts a string value, representing the path to your custom JSON configuration file.
- `--save|-s`  *Optional*
    - If passed without an argument, saves the converted colorscheme in the project's root directory.\
    By default, `kanverter.js` outputs it to stdout, allowing you to process it however you'd like.

> [!NOTE]
> By default, the saved file will be named `kanagawa_<variant>.vim`.

### Examples

Here are a couple of usage examples:

* Convert the `kanagawa_wave` colorscheme to a Vim colorscheme and print it to stdout (using the default configuration):

```bash
./kanverter
```

* The same, but with `kanagawa_dragon` and a custom configuration path/name:

> [!WARNING]
> Just remember to format the config itself as valid JSON.

```bash
./kanverter --theme dragon --config ~/.config/kan_conf
```

or using the short flags:

```bash
./kanverter -t dragon -c ~/.config/kan_conf
```

* Convert the `lotus` variant and save it to a file:

```bash
./kanverter -t lotus -s
```

or the same, but with a path specifying where to save the file:

```bash
./kanverter --theme lotus --save ~/.convig/vim/colors/
```

## Acknowledgements
- [kanagawa.nvim](https://github.com/rebelot/kanagawa.nvim): The original colorscheme for Neovim.

## License
[MIT](./LICENSE).
