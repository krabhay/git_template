# A set of useful git hooks

These hooks automate some tasks with the help of git.

## Installation

    git clone git@github.com:greg0ire/git_template.git ~/.git_template

## Configuration

Set the newly cloned repo as your git template directory. This will tell git to
populate new repositories created with either `git clone` or `git init` with
the content of this directory. By default, it uses `/usr/share/git-core/templates`.

    git config --global init.templatedir '~/.git_template'

## What's inside ?

For the moment, mostly hooks related to tools from the php ecosystem.

### Exuberant Ctags hook

Updates `.git/tags` file by scanning the project with the ctags command. It is
configured for a php project. To make vim look for this file in the `.git`
directory, you can install Tim Pope's [fugitive][4] or simply add
`set tags+=.git/tags` to your .vimrc - some plugins (like [ctrlp-tjump][5])
require this to see the tags even if fugitive is installed.

Enable it :

    git config --add hooks.enabled-plugins php/ctags

Optionally, you can set the projectType configuration, like this

    git config hooks.php-ctags.project-type projectType

Supported project types :

- symfony1
- symfony2

This will make ctags ignore cache directories.

If you want improved PHP languages support, install the [patched version of ctags][6]
support.


### Composer hook

This set of scripts monitors `composer.lock` changes and updates your vendor
dependencies when appropriate. Additionally, it checks composer.json for
validity on pre-commit. It assumes Composer is [globally installed][1].

You must tell it whether you wish it to run Composer, or if you would rather
it to notify you when you need to do it:

    git config hooks.composer.onChange just_warn

If the latter case, you must configure a notifier. Available notifiers for the
moment are `echo` and `notify-send`. So to use `notify-send`, which is pretty
cool, you need to do this :

    git config hooks.notification.notifier notify-send

Enable it :

    git config --add hooks.enabled-plugins php/composer

### Sismo hook

The post-commit hook makes [Sismo][2] run each time you commit. It is a post-commit
hook because Sismo is a *local* Continuous Testing Server, which means you can
build before you push.

You must configure the path to the sismo executable, and you may do so globally,
like this:

    git config --global --add hooks.php-sismo.path /usr/share/nginx/html/sismo.php

You must also configure the slug of your project:

    git config hooks.php-sismo.slug my-slug

Enable it :

    git config --add hooks.enabled-plugins php/sismo

### Doctrine hook

This hooks runs the `doctrine:schema:validate` task of a Symfony project and
updates / migrates your database depending on the presence of a
`doctrine-migrations` folder in your vendor directory.

Enable it :

    git config --add hooks.enabled-plugins php/doctrine

### Junk checker hook

Checks for user defined phrases that you don't want to commit to your
repository, such as `var_dump()`, `console.log()` etc.

This can be overridden by doing a:

    git commit --no-verify

This hook is language-agnostic.

You must configure the `phrasesfile` option for this hook . The value is the
name of a file that contains one phrase per line. There is a sample, you can
use it like this :

    git config [--global] hooks.junkchecker.phrasesfile .git/hooks/junkchecker/junk-phrases.sample

Enable it :

    git config --add hooks.enabled-plugins junkchecker

## Usage

By default, no hook will run. You must configure the hooks you need:

    git config hooks.enabled-plugins php/composer
    git config --add hooks.enabled-plugins php/ctags
    git config --add hooks.enabled-plugins junkchecker

The `--add` flag is necessary if you don't want to wipe out previously added
plugins.

If you want to enable a plugin on every project, use the `--global` option:

    git config --global --add hooks.enabled-plugins some_plugin

## Updating

To get updates you need to update your template directory first :

    # Go to your template directory (probably ~/.git_template)
    cd $(git config --path --get init.templatedir)
    git pull

Then, you can update any repository by running this in the working tree of your
repository :

    $(git config --path --get init.templatedir)/update.sh
    # If your template directory is ~/.git_template, this is equivalent to :
    ~/.git_template/update.sh

Make sure you have rsync installed.

## Contributing

Creating your php plugin is as easy as creating a php folder under `hooks/php`.
You can then create hooks in it. For the moment, only the following are supported
(because I'm lazy)

* post-commit
* post-checkout
* post-merge
* post-rewrite

If you need to add configuration variables git configuration, you should prefix
them with the path to your hook, which is obviously unique. This will avoid
collisions.

## Running the test suite

Get this random [shunit2][7] fork in ~/src :

    git clone git@github.com:deniscostadsc/shunit2.git

and run `tests/all.sh`.

## Credits

Inspired by [Tim Pope][3]

## Build Status

[![Build Status](https://travis-ci.org/greg0ire/git_template.png)](https://travis-ci.org/greg0ire/git_template)

[1]: https://github.com/composer/composer#global-installation-of-composer-manual
[2]: http://sismo.sensiolabs.org/ "A local Continuous Testing Server"
[3]: http://tbaggery.com/2011/08/08/effortless-ctags-with-git.html
[4]: https://github.com/tpope/vim-fugitive
[5]: https://github.com/ivalkeen/vim-ctrlp-tjump
[6]: https://github.com/shawncplus/phpcomplete.vim/wiki/Patched-ctags
[7]: https://code.google.com/p/shunit2/
