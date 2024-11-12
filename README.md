# Installation
1. Clone and copy (or symlink) this repo inside your userscripts dir (See [Dir structure](#dir-structure) below). 
2. Make `quterofi/open`, `quterofi/quteromarks`, `quterofi/switch_engine` and `quterofi/common` executable if needed. 
3. Declare your engines and quteromarks in `quterofi.toml` (See [Quterofi.toml](#quterofitoml-engines--quteromarks) section below).
4. Update your `config.py` file (See [Config](#config) section below).

See the [Dequterofi](#dequterofi) section for a script you can use to translate `quterofi.toml` files into regular python search engines. This is useful if you decide to switch back after trying quterofi for a while. It can also be used to declare search engines generated from a `quterofi.toml` file, without having to clone/download/install/use quterofi at all.

#### Dir structure

```
.
├── config.py
├── quickmarks
├── quterofi.toml
└── userscripts
    └── quterofi 
        ├── common
        ├── open
        ├── quteromarks
        └── switch_engine
```

# Usage
Quterofi provides users with the [open](#usage-of-the-open-userscript), [quteromarks](#usage-of-the-quteromarks-userscript) and [switch_engine](#usage-of-the-switch_engine-userscript) userscripts (`common` is a module providing utility functions to the other scripts)

## Usage of the `open` userscript
`quterofi/open` is a replacement for the `:open` menu

``` bash
quterofi/open [--newtab] [--string <arg>] [--invert] [--engines] [--history] [--quteromarks] [--autoaccept]
```

- Call with `--newtab` to use `:open -t` by default (It's also possible to switch later between `open` and `open -t` using **-kb-custom-2**)

- Call with `--string <arg>` to start writing with string ARG already set

- Call with `--invert` to treat the last word as a search engine alias. If your search string is `hello world ddg`, the underlying command will be `:open ddg hello world`, if and only if there is a search engine with alias `ddg` declared in your [quterofi.toml](#quterofitoml-engines--quteromarks) file. This behavior is for **-kb-accept-entry**, you can also accept your entry with **-kb-custom-2** for your string to be interpreted verbatim (`:open hello world ddg`).

- Call with `--engines` to open the engines menu directly (You can also enter this menu using **-kb-custom-5** in the main menu)

- Call with `--history` to open the history menu directly (You can also enter this menu using **-kb-custom-6** in the main menu)

- Call with `--quteromarks` to open the quteromarks menu directly (You can also enter this menu using **-kb-custom-7** in the main menu).

- Call with `--autoaccept` for the search engine menu to automatically submit the `:open` comand instead of setting the search engine alias in the search string of the main menu.

### Available keys when using the `open` menu
1. **-kb-accept-entry** (Any of `Ctrl+j`,`Ctrl+m`,`Return`,`KP_Enter`): 
   - If you call `quterofi/open` with `--invert`, the last word in the search string will be used as a search engine (if and only if there is one with such alias). For example, if your search string is `hello world ddg`, the underlying open command will be `:open ddg hello world`. Alternatively, you can accept your entry with **-kb-custom-1** for your string to be interpreted verbatim (`:open hello world ddg`)
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
   - Open submenu listing all search engines, pick one to be set in your search string. The choosen alias will be set at the end or beginning of your search string, depending on whether you are using `--invert` or not, and indepently of cursor position. If there is already a valid search engine set, quterofi will replace it instead of stacking a new one.

8. **-kb-custom-6** (`Alt+h`): 
   - Open submenu listing history items.

9. **-kb-custom-7** (`Alt+q`): 
   - Open submenu listing quteromarks.

## Usage of the `switch_engine` userscript
Call `quterofi/switch_engine` to open a menu asking for the alias of a new search engine to open the search string present in your current url, if there is a matching engine for your current url. See examples below.

``` bash
quterofi/switch_engine [--newtab] [--edit] [--main_invert] [--main_autoaccept]
```

- Call with `--newtab` to use `:open -t` instead of `:open`.
- Call with `--edit` to edit the search string and engine alias in the main menue.
- Call with `--main_invert` if you want `--edit` to call the main menu with `--invert`.
- Call with `--main_autoaccept` if you want `--edit` to call the main menu with `--autoaccept`.

#### Example 1 (without `--edit`)
- Current url: `https://www.google.com/search?q=hello world`
- New engine selected by calling `quterofi/switch_engine`: `ghi.qr`
- Automagically redirects to: `https://github.com/cortsf/quterofi/issues?q=hello world`

#### Example 2 (with `--edit`)
- Current url: `https://www.google.com/search?q=hello world`
- Calling `quterofi/switch_engine` with `--edit` will call `quterofi/open` with the string `gl hello world` or `hello world gl` if you use `--main_invert` in addition to `--edit`. This allows you to edit both the search engine and the search string.

### Available keys when using the `switch_engine` menu
1. **-kb-accept-entry** (Any of `Ctrl+j`,`Ctrl+m`,`Return`,`KP_Enter`)
2. **-kb-cancel'** (Any of `Escape`,`Control+g`,`Control+bracketleft`)

## Quterofi.toml (Engines & quteromarks)
Declare your search engines and quteromarks in `quterofi.toml` (See [Dir structure](#dir-structure)) using this format. Any equivalent toml syntax declaring the same underlying structure/s should work (not tested).

This format allows users to create custom "templates" (quite an abstract concept in this context..) instructing quterofi how to generate search engines and quteromarks. Note that you can declare/define any variable to construct urls, except for `alias` which is reserved to be used exclusively to construct aliases, and it's in fact, mandatory to declare for every engine/quteromark declaration, and it's also the only variable available to construct aliases (This may change in the future allowing any variable name and number to be used both for urls and aliases).

### Example
Brief example skipping many useful rules like `ghic`, `ghpc` or `ghd`, for github closed issues, closed pulls and discussions, respectively. For brevity. See more github rules on this [gist](https://gist.github.com/cortsf/d6273111f48991e17b3d279a8e90cb83)

``` toml
## Quterofi rules
####################

[[engine_rules]]
er_template = "search_engines"
er_alias = "{alias}"
er_url = "{url}"

[[engine_rules]]
er_template = "wikipedia_languages"
er_alias ="wp.{alias}"
er_url = "https://{lang}.wikipedia.org/wiki/{}"

[[engine_rules]]
er_template = "github_repos"
er_alias = "gh.{alias}"
er_url = "https://github.com/search?q=repo%3A{user}%2F{repo}+{}&type=issues"

[[engine_rules]]
er_template = "github_repos"
er_alias ="ghi.{alias}"
er_url = "https://github.com/{user}/{repo}/issues?q={}"

[[engine_rules]] # Use this one to open individual issues, directly, by number.
er_template = "github_repos"
er_alias ="ghin.{alias}"
er_url = "https://github.com/{user}/{repo}/issues/{}"

[[engine_rules]]
er_template = "github_repos"
er_alias ="ghp.{alias}"
er_url = "https://github.com/{user}/{repo}/pulls?q={}"

[[engine_rules]] # Use this one to open individual pr's, directly, by number.
er_template = "github_repos"
er_alias ="ghpn.{alias}"
er_url = "https://github.com/{user}/{repo}/pull/{}"

[[quteromark_rules]]
qr_template = "github_repos"
qr_alias ="gh.{alias}"
qr_url = "https://github.com/{user}/{repo}"

[[quteromark_rules]]
qr_template = "github_repos"
qr_alias ="ghi.{alias}"
qr_url = "https://github.com/{user}/{repo}/issues"

[[quteromark_rules]]
qr_template = "github_repos"
qr_alias ="ghp.{alias}"
qr_url = "https://github.com/{user}/{repo}/pulls"

## Search engines
####################

[[search_engines]]
alias = "ddg"
url = "https://duckduckgo.com/?q={}&ia=web"

[[search_engines]]
alias = "gl"
url = "https://www.google.com/search?q={}"

## Github repos
####################

[[github_repos]]
alias = "qr"
user = "cortsf"
repo = "quterofi"

[[github_repos]]
alias = "lnx"
user = "torvalds"
repo = "linux"

## Wikipedia
####################

[[wikipedia_languages]]
alias = "en"
lang = "en"

[[wikipedia_languages]]
alias = "es"
lang = "es"
```

### Resulting engines for the above quterofi.toml
``` json
{"ddg": "https://duckduckgo.com/?q={}&ia=web"}
{"gl": "https://www.google.com/search?q={}"}
{"wp.en": "https://en.wikipedia.org/wiki/{}"}
{"wp.es": "https://es.wikipedia.org/wiki/{}"}
{"gh.qr": "https://github.com/search?q=repo%3Acortsf%2Fquterofi+{}&type=issues"}
{"gh.lnx": "https://github.com/search?q=repo%3Atorvalds%2Flinux+{}&type=issues"}
{"ghi.qr": "https://github.com/cortsf/quterofi/issues?q={}"}
{"ghi.lnx": "https://github.com/torvalds/linux/issues?q={}"}
{"ghin.qr": "https://github.com/cortsf/quterofi/issues/{}"}
{"ghin.lnx": "https://github.com/torvalds/linux/issues/{}"}
{"ghp.qr": "https://github.com/cortsf/quterofi/pulls?q={}"}
{"ghp.lnx": "https://github.com/torvalds/linux/pulls?q={}"}
{"ghpn.qr": "https://github.com/cortsf/quterofi/pulls/{}"}
{"ghpn.lnx": "https://github.com/torvalds/linux/pulls/{}"}
```

### Resulting quteromarks for the above quterofi.toml
``` json
{"gh.qr": "https://github.com/cortsf/quterofi"}
{"ghi.qr": "https://github.com/cortsf/quterofi/issues"}
{"ghp.qr": "https://github.com/cortsf/quterofi/pulls"}
{"gh.lnx": "https://github.com/torvalds/linux"}
{"ghi.lnx": "https://github.com/torvalds/linux/issues"}
{"ghp.lnx": "https://github.com/torvalds/linux/pulls"}
```

In this particular example, the more `[[github_repos]]` you declare, the more you benefit since you'll have for free all of those rules using `er_template = "github_repos"` generating many (uniformly prefixed) engines and quteromarks for you.

## Config
In your config.py include code below. Be sure to set `<username>`.


``` python
qbdir = "/home/<username>/.config/qutebrowser"

exec(open(qbdir + '/userscripts/quterofi/common').read())

all_engines = parse_engines(qbdir + "/quterofi.toml")

for alias, url in all_engines.items():
    c.url.searchengines[alias] = url
```

### Quterofi bindings

``` python
config.bind('o', 'spawn -u quterofi/open --invert --autoaccept')
config.bind('<Ctrl-o>', 'spawn -u quterofi/open --invert --newtab --autoaccept')
config.bind('<Shift-o>', 'spawn -u quterofi/open --invert --newtab --autoaccept')
config.bind('<Alt-o>', 'spawn -u quterofi/open --invert --string {url:pretty}')
config.bind('<Alt-Shift-o>', 'spawn -u quterofi/open --invert --newtab --string {url:pretty}')
config.bind('<Alt-h>', 'spawn -u quterofi/open --history')
config.bind('<Alt-Shift-h>', 'spawn -u quterofi/open --history --newtab')
config.bind('<Alt-q>', 'spawn -u quterofi/open --quteromarks')
config.bind('<Alt-Shift-q>', 'spawn -u quterofi/open --quteromarks --newtab')
config.bind(',o', 'spawn -u -m quterofi/switch_engine')
config.bind(',O', 'spawn -u -m quterofi/switch_engine --newtab')
config.bind(',e', 'spawn -u -m quterofi/switch_engine --edit --main_autoaccept')
config.bind(',E', 'spawn -u -m quterofi/switch_engine --edit --newtab --main_autoaccept')
config.bind(',y', 'mode-enter caret;;yank selection;;cmd-later 40 spawn -u -m quterofi/open --engines --autoaccept --string {clipboard}') # Use when searching text
config.bind(',y', 'yank selection;;cmd-later 40 spawn -u -m quterofi/open --engines --autoaccept --string {clipboard}', mode="caret")

```

### Extra bindings 
These are standard non-quterofi bindings that play well with the above keybindings.

``` python
config.bind("e", "cmd-set-text -s :open ")
config.bind("E", "cmd-set-text -s :open -t ")
config.bind("<Alt-e>", "cmd-set-text -s :open {url:pretty}")
config.bind("<Alt-Shift-e>", "cmd-set-text -s :open -t {url:pretty}")
```

# External launcher

``` bash
#!/usr/bin/env bash

export QUTE_CONFIG_DIR="$HOME/.config/qutebrowser"
export QUTE_FIFO="/tmp/quterofi_launcher_fifo"
> "$QUTE_FIFO"
"$QUTE_CONFIG_DIR"/userscripts/quterofi/open "$@"
[[ -n "$(cat "$QUTE_FIFO")" ]] && qutebrowser "$(cat "$QUTE_FIFO")"
rm "$QUTE_FIFO"
```

You may want to add some (system-dependent..) code to switch window focus automatically.

## Emacs 
This function opens the `quterofi/open` script showing the list of search engines. As soon as you pick one, it will use this engine to search the text inside the emacs region, or the `word-at-point`, if there is no active region.

This function needs the [External launcher](#external-launcher).

``` elisp
(defun quterofi () (interactive)
       (shell-command (format "/path/to/quterofi_launcher.sh --newtab --engines --autoaccept --string %s" 
			      (if (use-region-p)
				  (buffer-substring (mark) (point))
				  (word-at-point)
				)
			      )
		      )
       )
```


# Dequterofi
Use this script to translate quterofi.toml files into python code you can source or paste in your `config.py`. Needs `toml2json` and `jq` available on `$PATH` to work.

``` bash
#!/usr/bin/env bash
# USAGE: decuterofi.sh /path/to/quterofi.toml > engines.py
# DEPS: toml2json, jq

json="$(toml2json "$1")"

echo "$json" | jq -r '.engine_rules.[] | .er_template + " " + .er_alias + " " + .er_url' | while IFS=' ' read -r er_template er_alias er_url; do
    url_variables="$(echo "$er_url" | grep -o '{[a-zA-Z0-9]*}' | grep -v "{}" | tr -d "{}")"
    while read -r engine_item; do
	if [ -n "$engine_item" ]; then 
	    new_url="$er_url"
	    new_alias="$(echo "${er_alias}" | sed "s/{alias}/$(echo "$engine_item" | jq -r .alias)/")"
	    while read -r url_var; do
		new_url="$(echo "${new_url}" | sed "s|{${url_var}}|$(echo "$engine_item" | jq -r ."${url_var}" | sed 's/&/\\\&/g')|")"
	    done  <<<"$url_variables"
	    echo "c.url.searchengines[\"$new_alias\"] = \"$new_url\""
	fi
    done <<<"$(echo "$json" | jq -c ".${er_template}[]?")"
done
```

For the above [Example](#example), this program produces the following engines:

``` python
c.url.searchengines["ddg"] = "https://duckduckgo.com/?q={}&ia=web"
c.url.searchengines["gl"] = "https://www.google.com/search?q={}"
c.url.searchengines["wp.en"] = "https://en.wikipedia.org/wiki/{}"
c.url.searchengines["wp.es"] = "https://es.wikipedia.org/wiki/{}"
c.url.searchengines["gh.qr"] = "https://github.com/search?q=repo%3Acortsf%2Fquterofi+{}&type=issues"
c.url.searchengines["gh.lnx"] = "https://github.com/search?q=repo%3Atorvalds%2Flinux+{}&type=issues"
c.url.searchengines["ghi.qr"] = "https://github.com/cortsf/quterofi/issues?q={}"
c.url.searchengines["ghi.lnx"] = "https://github.com/torvalds/linux/issues?q={}"
c.url.searchengines["ghin.qr"] = "https://github.com/cortsf/quterofi/issues/{}"
c.url.searchengines["ghin.lnx"] = "https://github.com/torvalds/linux/issues/{}"
c.url.searchengines["ghp.qr"] = "https://github.com/cortsf/quterofi/pulls?q={}"
c.url.searchengines["ghp.lnx"] = "https://github.com/torvalds/linux/pulls?q={}"
c.url.searchengines["ghpn.qr"] = "https://github.com/cortsf/quterofi/pull/{}"
c.url.searchengines["ghpn.lnx"] = "https://github.com/torvalds/linux/pull/{}"
```

Source from `engines.py` with:

``` python
config.source('./engines.py')
```

Or just copy the contents inside your `config.py`


# Translate standard qb quickmarks file into quteromarks

``` bash
#!/usr/bin/env bash

cat "$1" |  while read -r line; do
    echo -e "\n[[quteromarks]]" >> quteromarks.toml
    echo -e "alias=\"$(echo "$line" | sed 's/ [^ ]*//')\"" >> quteromarks.toml
    echo -e "url=\"$(echo "$line" | awk '{print $NF}')\"" >> quteromarks.toml
done
```
