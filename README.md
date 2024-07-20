# Installation
1. Clone and copy (or symlink) this repo inside your userscripts dir (See [Dir structure](#dir-structure) below). 
2. Make `quterofi/open`, `quterofi/switch_engine` and `quterofi/read_engines` executable if needed. 
3. Declare your engines in `engines.toml` (See [Engines](#engines) section below).
4. Update your `config.py` file (See [Config](#config) section below).

#### Dir structure

```
.
├── config.py
├── engines.toml
└── userscripts
    └── quterofi 
        ├── open
        ├── read_engines
        └── switch_engine
```

# Usage
Quterofi provides the `open` and `switch_engine` userscripts (`read_engines` is just an utility used to parse engines declared in engines.toml)

## Usage of the `open` userscript
`quterofi/open` is a replacement for the `:open` menu

``` bash
quterofi/open  [--extended] [--newtab] [--string <arg>] [--invert] [--history] [--quickmarks]
```

- Call with `--extended` if you declare your `engines.toml` using the extended format. See [Engines](#engines) for more information on this topic.

- Call with `--newtab` to use `:open -t` by default (It's also possible to switch later between `open` and `open -t` using **-kb-custom-2**)

- Call with `--string <arg>` to start writing with string ARG already set

- Call with `--invert` to treat the last word as a search engine alias. If your search string is `hello world ddg`, the underlying command will be `:open ddg hello world`, if and only if there is a search engine with alias `ddg` declared in your [engines.toml](#engines) file. This behavior is for **-kb-accept-entry**, you can also accept your entry with **-kb-custom-2** for your string to be interpreted verbatim (`:open hello world ddg`). Note: When using `--invert`, Qb could still interpret the first word in the underlying `:open hello world ddg` as a search engine, this behavior could be deactivated, see: #6 on the issue tracker.

- Call with `--history` to open the history menu directly (You can also enter this menu using **-kb-custom-6** in the main menu)

- Call with `--quickmarks` to open the quickmarks menu directly (You can also enter this menu using **-kb-custom-7** in the main menu)

### Available keys when using the `open` menu
1. **-kb-accept-entry** (Any of `Ctrl+j`,`Ctrl+m`,`Return`,`KP_Enter`): 
   - If you call `quterofi/open` with `--invert`, the last word in the search string will be used as a search engine (if and only if there is one with such alias). For example, if your search string is `hello world ddg`, the underlying open command will be `:open ddg hello world`. Alternatively, you can accept your entry with **-kb-custom-1** for your string to be interpreted verbatim (`:open ddg hello world`)
   - If you call `quterofi/open` without `--invert`, **-kb-accept-entry** will `:open` your search string, verbatim.

2. **-kb-cancel** (Any of `Escape`,`Control+g`,`Control+bracketleft`): 
   - Exit menu

3. **-kb-custom-1** (Any of `Alt+j`,`Alt+m`): 
   - Accept entry as normal, without inversion. `ddg hello` -> `:open ddg hello`

4. **-kb-custom-2** (`Ctrl+o`): 
   - Switch between `:open` and `:open -t` without closing the menu or clearing user input.

5. **-kb-custom-3** (`Alt+o`): 
   - Set search string to be the current url. This is the same as calling `spawn -u quterofi/open --string {url:pretty}`

6. **-kb-custom-4** (`Alt+u`): 
   - Close menu, open the regular `:open` menu and set search string: `:open <search_string>` (search string is set verbatim, without inversion)

7. **-kb-custom-5** (`Alt+e`): 
   - Open submenu listing all search engines, pick one to be set in your search string. The choosen alias will be set at the end or beginning of your search string, depending on whether you are using `--invert` or not, and indepently of cursor position.

8. **-kb-custom-6** (`Alt+h`): 
   - Open submenu listing history items.

9. **-kb-custom-7** (`Alt+q`): 
   - Open submenu listing quickmarks.

## Usage of the `switch_engine` userscript
Call `quterofi/switch_engine` to open a menu asking for the alias of a new search engine to open the search string present in your current url, if there is a matching engine for your current url. See example below.

``` bash
quterofi/switch_engine [--extended] [--newtab]
```

- Call with `--extended` if you declare your `engines.toml` using the extended format. See [Engines](#engines) for more information on this topic.

- Call with `--newtab` to use `:open -t` by default (It's also possible to switch later between `open` and `open -t` using **-kb-custom-2**)

### Example
- Current url: `https://www.google.com/search?q=hello`
- New engine selected by calling `quterofi/switch_engine`: `ghi.fzf`
- Automagically redirects to: `https://github.com/junegunn/fzf/issues?q=hello`

### Available keys when using the `switch_engine` menu
1. **-kb-accept-entry** (Any of `Ctrl+j`,`Ctrl+m`,`Return`,`KP_Enter`)
2. **-kb-cancel'** (Any of `Escape`,`Control+g`,`Control+bracketleft`)

## Engines
Declare `engines.toml` (See [Dir structure](#dir-structure)) using either the basic or extended format. Any equivalent toml syntax declaring the same underlying structure should work (not tested).

### Basic format
Using this format the user can't define new rules instructing quterofi how to create search engines. It allows to declare engines with `[[engines]]` and `[[github_repos]]` exclusively. See [Extended format](#extended-format) for an extendable alternative.

``` toml
[[engines]]
alias = "gl"
url = "https://www.google.com/search?q={}"

[[engines]]
alias = "ddg"
url = "https://duckduckgo.com/?q={}&ia=web"

[[github_repos]]
alias = "fzf"
user = "junegunn"
repo = "fzf"
```

This will generate the following search engines:

``` json
{"gl": "https://www.google.com/search?q={}"}
{"ddg": "https://duckduckgo.com/?q={}&ia=web"}
{"gh.fzf": "https://github.com/search?q=repo%3Ajunegunn%2Ffzf+{}&type=issues"}
{"ghi.fzf": "https://github.com/junegunn/fzf/issues?q={}"}
```

### Extended format
The extended format allows users to create custom "templates" instructing quterofi how to generate search engines based on templates.

For quterofi to understand the extra details declared using this format, you have to call `quterofi/open` and `quterofi/switch_engine` with the `--extended` flag. 

Note that it's also possible to use this particular example without the `--extended` flag, in that case quterofi will just ignore everything except `[[engines]]` and `[[github_repos]]`. In other words, it wont be able to read `[[wikipedia_languages]]` since it doesn't knows how.

``` toml
## Quterofi rules
####################

[[engine_rules]]
er_template = "engines"
er_alias = "{alias}"
er_url = "{url}"

[[engine_rules]]
er_template = "github_repos"
er_alias = "gh.{alias}"
er_url = "https://github.com/search?q=repo%3A{user}%2F{repo}+{}&type=issues"

[[engine_rules]]
er_template = "github_repos"
er_alias ="ghi.{alias}"
er_url = "https://github.com/{user}/{repo}/issues?q={}"


[[engine_rules]]
er_template = "wikipedia_languages"
er_alias ="wp.{alias}"
er_url = "https://{lang}.wikipedia.org/wiki/{}"

## Engines
####################

[[engines]]
alias = "ddg"
url = "https://duckduckgo.com/?q={}&ia=web"

[[engines]]
alias = "gl"
url = "https://www.google.com/search?q={}"

## Github
####################

[[github_repos]]
alias = "qr"
user = "cortsf"
repo = "quterofi"


## Wikipedia
####################

[[wikipedia_languages]]
alias = "eng"
lang = "en"

[[wikipedia_languages]]
alias = "esp"
lang = "es"
```

## Config
In your config.py include code below. Be sure to:

- Set `<username>`.
- Use `all_engines = parse_engines_extended(qbdir + "/engines.toml")` if you choose the "extended" toml format.
- Use instead `all_engines = parse_engines_simple(qbdir + "/engines.toml")` if you choose the "simple" toml format.
- Use `--extended` in all your quterofi bindings if you use the "extended" toml format.


``` python
qbdir = "/home/<username>/.config/qutebrowser"

exec(open(qbdir + '/userscripts/quterofi/read_engines').read())

all_engines = parse_engines_extended(qbdir + "/engines.toml")

for alias, url in all_engines.items():
    c.url.searchengines[alias] = url
```

### Quterofi bindings
This is what I use. You can remove the `--extended` flag if you use the "simple" instead of the "extended" toml format (See [Engines](#engines)).

```
config.bind('o', 'spawn -u quterofi/open --extended --invert')
config.bind('<Shift-o>', 'spawn -u quterofi/open --extended --invert --newtab')
config.bind('<Alt-o>', 'spawn -u quterofi/open --extended --invert --string {url}')
config.bind('<Alt-Shift-o>', 'spawn -u quterofi/open --extended --invert --newtab --string {url}')
config.bind('<Ctrl-o>', 'spawn -u quterofi/open --extended --invert --newtab')
config.bind('<Alt-h>', 'spawn -u quterofi/open --extended --invert --history')
config.bind('<Alt-q>', 'spawn -u quterofi/open --extended --invert --quickmarks')
config.bind(',o', 'spawn -u -m quterofi/switch_engine --extended')
config.bind(',O', 'spawn -u -m quterofi/switch_engine --extended --newtab')
```

### Extra bindings 
These are standard non-quterofi bindings that play well with the above keybindings.

``` python
config.bind("e", "cmd-set-text -s :open ")
config.bind("E", "cmd-set-text -s :open -t ")
config.bind("<Alt-e>", "cmd-set-text -s :open {url:pretty}")
config.bind("<Alt-Shift-e>", "cmd-set-text -s :open -t {url:pretty}")
```
