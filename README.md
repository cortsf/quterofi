# Installation
1. Copy folder `quterofi` inside your userscripts dir. 
2. Make `quterofi/open`, `quterofi/switch_engine` and `quterofi/read_engines` executable if needed. 
3. Declare your engines in `engines.toml` inside your config dir (See [Engines](#engines) section below).
4. Update your `config.py` files (See [Config](#config) section below).

# Usage
Quterofi provides the `open` and `switch_engine` scripts/commands

## Usage of the `open` script
This command is a replacement for the `:open` menu
```
rofi_open [--string <arg>] [--newtab] [--invert]
```
- Call with `--newtab` to use `:open -t` by default (It's also possible to switch later between `open` and `open -t` using `-kb-custom-2`)

- Call with `--string <arg>` to start with string ARG 

- Call with `--invert` to threat the last word as a search engine alias, instead of the first one. If you search string is `hello ddg`, the underlying command will be `:open ddg hello`. This behavior is for `-kb-accept-entry`, you can accept your entry with `-kb-custom-2` for your string to be interpreted as normal (`:open ddg hello`)

### Available keys when using the `open` script
1. -kb-accept-entry (Any of `Ctrl+j`,`Ctrl+m`,`Return`,`KP_Enter`): 
  If you call rofi with `--invert` and your search string is `hello ddg`, the underlying open command will be `:open ddg hello`. Alternatively, you can accept your entry with `-kb-custom-1` for your string to be interpreted as normal (`:open ddg hello`)

2. -kb-cancel (Any of `Escape`,`Control+g`,`Control+bracketleft`): 
  Exit menu

3. -kb-custom-1 (Any of `Alt+j`,`Alt+m`): 
  Accept entry as normal, without inversion. `ddg hello` -> `:open ddg hello`

4. -kb-custom-2 (`Ctrl+o`): 
  Switch between `:open` and `:open -t` without closing the menu or clearing user input.

5. -kb-custom-3 (`Alt+o`): 
  Set search string to be the current url. This is the same as calling `spawn -u quterofi/open --string {url:pretty}`

6. -kb-custom-4 (`Alt+u`): 
  Close menu and open the regular `:open` menu and set search string after `:open <search_string>` (verbatim, without inversion)

7. -kb-custom-5 (`Alt+e`): 
  Open a new menu listing all search engines, to pick one.


## Usage of the `switch_engine` script

```
switch_engine '--newtab'
```

Calling `switch_engine` will open a menu asking for the alias of a new search engine to open the search string in your current url, if there is a matching engine for your current url. Example:

Current url: `https://www.google.com/search?q=hello`
New engine you selected: `ghi.lnx`
new current url: `https://github.com/torvalds/linux/pulls?q=hello`


## Engines
Declare engines as follows. Any equivalent toml syntax should work (not tested)

``` toml
[[engines]]
alias = "gl"
url = "https://www.google.com/search?q={}"

[[github_repos]]
alias = "lnx"
user = "torvalds"
repo = "linux"
```

This will generate the following search engines:
``` json
{"gl": "https://www.google.com/search?q={}"}
{"gh.lnx": "https://github.com/search?q=repo%3Atorvalds%2Flinux+hello&type=issues"}
{"ghi.lnx": "https://github.com/torvalds/linux/pulls?q=hello"}
```

## Config
Example config (make your own!)

``` python
qbdir = "/home/<username>/.config/qutebrowser"

exec(open(qbdir + '/userscripts/quterofi/read_engines').read())

all_engines = parseEngines(qbdir + "/engines.toml")

for alias, url in all_engines.items():
    c.url.searchengines[alias] = url

config.bind('o', 'spawn -u quterofi/open --invert')
config.bind('<Shift-o>', 'spawn -u quterofi/open --invert --newtab')
config.bind('<Alt-o>', 'spawn -u quterofi/open --invert --string {url}')
config.bind('<Alt-Shift-o>', 'spawn -u quterofi/open --invert --newtab --string {url}')
config.bind('<Ctrl-o>', 'spawn -u quterofi/open --invert --newtab')

config.bind(',o', 'spawn -u -m quterofi/switch_engine')
config.bind(',O', 'spawn -u -m quterofi/switch_engine --newtab')

config.bind("e", "cmd-set-text -s :open ")
config.bind("E", "cmd-set-text -s :open -t ")
config.bind("<Alt-e>", "cmd-set-text -s :open {url:pretty}")
config.bind("<Alt-Shift-e>", "cmd-set-text -s :open -t {url:pretty}")
```
