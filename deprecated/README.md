# Rofi-open
This is a basic bash-only (no extra deps) version of quterofi/open. 

## Usage

``` bash
rofi_open [--string <arg>] [--(no-)newtab] [-h|--help]
```


## Toml syntax
It works with the following toml syntax, exclusively.

``` toml
[[engines]]
alias = "gl"
url = "https://www.google.com/search?q={}"

[[github_repos]]
alias="lnx"
user="torvalds"
repo="linux"
```

## Use without engines.toml

Alternatively, replace the last code block with this one below, to skip looking up for engines declared in engines.toml and asume instead that the last word in the search string is a valid search engine declared as usual in `config.py`, when selecting an engine with `-kb-accept-entry` (any of `"Ctrl+j`,`Ctrl+m`,`Return` or `KP_Enter`)

``` bash
while true; do
    [[ "$it" -lt "6" ]] && it=$((it+1)) || { echo "message-info 'Breaking at: $it'" >> "$QUTE_FIFO"; break; } # use to prevent infinite loops when rofi fails to start (dev/debug only)
    [[ "$ret" == "0" ]] && { echo "$command $(echo "$string" | sed 's/ *$//' | awk 'NF>1{print $NF}') $(echo "$string" | sed 's/ *$//' | sed 's/\ [^\ ]*$//')" >> "$QUTE_FIFO"; break; }
    [[ "$ret" == "1" ]] && exit 0;
    [[ "$ret" == "10" ]] && { echo "$command $string" >> "$QUTE_FIFO"; break; }
    [[ "$ret" == "11" && "$command" == "open" ]] && { command="open -t"; string="$(call_rofi "$string")"; ret="$?"; }
    [[ "$ret" == "11" && "$command" == "open -t" ]] && { command="open"; string="$(call_rofi "$string")"; ret="$?"; }
    [[ "$ret" == "12" ]] && { string="$(call_rofi "$QUTE_URL")"; ret="$?"; }
    [[ "$ret" == "13" ]] && { echo "cmd-set-text -s :$command $string" >> "$QUTE_FIFO" ; break ; }
done
```
