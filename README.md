# dotfiles

## Tools to prevent the accidental commit of data to github

## Two things to note:

**If you are working with data that you don't want to accidentally appear on github the best way to ensure against accidental commits is to ensure that the data are not stored within repository in the first place. However using the tools described here provide a second layer of protection to ensure that accidents don't happen.**

**These are templates, and are best edited to suit your needs**

## Contents

This repo contains:

* A template [.gitignore](.gitignore) file which will prevent git from seeing the most commonly used data formats.
* A `git_template` which, if used, will install a [pre-commit](git_template/hooks/pre-commit) and [pre-push](git_template/hooks/pre-push) hook into all new repositories to prevent data in common formats from being commited or pushed by accident.

If used correctly, the templates here will:

* Prevent git from seeing common data formats and any files containing 'OFFICIAL'.
* If the user attempts to commit these files (for example by using `git add -f`, at the point of commit and push, the user will not be able to proceed without explicitly setting a `VARIABLE=1 git commit`, where VARIABLE refers to the particular check being made. Documentation for each of these is given when the error occurs, and does not need to be provided here.

This repo is based on https://github.com/MastodonC/dotfiles.

## Usage

First clone the repository and navigate to it:

```bash
git clone https://github.com/ukgovdatascience/dotfiles
cd dotfiles
```

### Template .gitignore

A template [.gitignore](.gitignore) file is included in the repository. Using this file will condition git to ignore the following file formats:

* Any file containing the word `OFFICIAL`
* Common text file formats: csv, txt
* Excel file formats: xls, xlsx, xlsm, xlst
* SPSS file formats: sav
* SAS file formats: sas7bdat
* Open office sheets files: ods
* Google sheets files: gsheet
* Some database formats: db, sqlite
* Feather format: feather
* Pickle objects: pkl, pickle
* R data files: RData, Rds

Additional file formats copied from https://gist.github.com/octocat/9257657:

* Compressed file formats
* Common system files (e.g. thumbs.db)

There are a number of ways one might use the [.gitignore](.gitignore) file:

#### Copy into the root of a repository

Copy the file into the root of a git repository and edit as approproate.

#### Set as .gitignore_global

To use for ALL git repos, the template can be set globally. Note that this is likely to be very annoying in its current form, so should be edited as appropriate, otherwise trivial files are likely to be invisible to git. Even ignored files can be added with `git add -f`, however you need to realise that they are being ignored!

```bash
cp .gitignore ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

Note that if you set this as the `.gitignore_global`, you can exclude individual repositories by running:

```bash
git config --local --unset core.excludesfile
```

The above can be used with `--global` to stop using it as the global `.gitignore`.

More information about ignoring files can be found here: https://help.github.com/articles/ignoring-files/

**Note: In default state using this as a default .gitignore is likely to be very annoying! You are advised to use the default then customise it in each directory as required.**

### pre-commit and pre-push hooks

These hooks will check, at the point of each commit and push, that the the code does not contain any of the following:

* AWS keys (determined by regex)
* Private SSH keys (determined by regex)
* .pem files
* Various data formats: 
  * xls, xlsx, xlsm, xlst, 
  * csv, txt, 
  * sav, 
  * db, sqlite, 
  * feather, pkl, pickle, 
  * ods, gsheet, 
  * Rdata, Rds 

#### Install in all new repositories

** Note that this will overwrite any prior pre-commit and pre-push hooks you may have already installed**

```bash
cp -r git_template ~/.git_template

git config --global init.templatedir '~/.git_template'

```
#### Install into any pre-existing repositories

Having done the previous step, run the following to sync git hooks with the defaults in `~/.git_template`:

```bash
$(git config --path --get init.templatedir)/update.sh
```

#### Install intall all pre-existing repositories in a directory

The following code will cycle through all directories in a folder (assuming they are all managed by git), and install the default hooks into each of them:

```bash
current=$(pwd); for i in $(ls .); do echo $i; cd $i ; $(git config --path --get init.templatedir)/update.sh; cd $current done
```

#### Fix grep for Mac OS X users

Mac OS X seems to have an old grep version by default. to fix this, the homebrew version is a solution:

```
brew install grep --with-default-names
```

this will replace grep.  Make sure your PATH has /usr/local/bin preceding /usr/bin or /bin
