# RakuRename

A file-rename tool inspired by Perl's [File::Rename](https://metacpan.org/dist/File-Rename/view/rename.PL).

## Installation

If you have [zef](https://github.com/ugexe/zef), there are a number of options:

- just run `zef install RakuRename`;
- or clone this repo and issue `zef install <path-to-cloned-repo>`;
- as yet another variation, `cd` to it, and `zef install .`. 

Additionally, since all there is here, really, is the executable `bin/rrnm`, you can just download that and use it.

## Usage

The distribution only provides the `rrnm` binary. In general:

- Run `rrnm --help` to see information on the possible command-line switches / options.

- The first positional argument should be a substitution or transliteration operator, as documented under `Raku`'s [substitution-operators](https://docs.raku.org/language/operators#Substitution_operators) doc section.

- The positional arguments following that should be the files you want to move / copy / whatever (see the examples below).

- The regex syntax is whatever `Raku` [will recognize](https://docs.raku.org/language/regexes).

## Examples

Some usage examples follow, all of which are to be run in a terminal where you have access to `zef`-installed binaries.
```
rrnm 's/^a/b/' <DIRECTORY>/a*
```
will rename every file / directory of the form `<DIRECTORY>/a<BLAH>` to `<DIRECTORY>/b<BLAH>`.

On the other hand,
```
rrnm -c='cp' 's/^a/b/' <DIRECTORY>/a*
```
will take every file of the form `<DIRECTORY>/a<BLAH>` and *copy* it to `<DIRECTORY>/b<BLAH>`. It will complain about not being able to copy directories among those (because the [cp command](https://linux.die.net/man/1/cp) behaves this way), but you can fix that with
```
rrnm -c='cp -r' 's/^a/b/' <DIRECTORY>/a*
```
You can even do the following in a `git` repo, to effect a regex-based [git-mv](https://git-scm.com/docs/git-mv):

```
rrnm -c='git mv' 's/^(\d)/0$0/' [0-9]* 
```
or equivalently, using [lookahead assertions](https://docs.raku.org/language/regexes#Lookahead_assertions),
```
rrnm -c='git mv' 's/^<?before \d>/0/' [0-9]*
```
That will perform a `git mv` on every file whose name starts with a digit, prepending a `0` (if you wanted to do this for some reason).

The `--dry` option just *shows* you the command that would be run. So in a git repo that contains files `1.txt` and `2.txt` (and nothing else starting with a digit), 

```
rrnm -c='git mv' 's/^(\d)/0$0/' [0-9]* --dry
```
would simply show you

```
git mv 1.txt ./01.txt
git mv 2.txt ./02.txt
```
in the terminal.

I use this all the time: first run what you *think* will work with `--dry`, then recall the last command and remove the `--dry` option.
