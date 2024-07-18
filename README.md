# Usage
1. Copy folder `quterofi` inside your userscripts dir. 
2. Make `quterofi/open`, `quterofi/switch_engine` and `quterofi/read_engines` executable if needed. 
3. Declare your engines in `engines.toml` inside your config dir (See chapter below).
4. Update your config.py (See chapter below).

## engines.toml 
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

## config.py
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
