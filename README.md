# dotfiles

## Tools to prevent the accidental commit of data to github

This repo contains:

* A template global [.gitignore](.gitignore_global) file which will prevent git from seeing the most commonly used data formats.
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

### global .gitignore

To set [.gitignore_global](.gitignore_global) as default for all new repositories:

```
cp .gitignore_global ~/
git config --global core.excludesfile ~/.gitignore_global
```

Doing so will condition git to ignore the following files in all new repos:

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

More information about ignoring files can be found here: https://help.github.com/articles/ignoring-files/

**Note: In default state using this as a default .gitignore is likely to be very annoying! You are advised to use the default then customise it in each directory as required.**

### pre-commit and pre-push hooks

These hooks will check, at the point of each commit and push, that the the code does not contain any of the following:

* AWS keys (determined by regex)
* Private SSH keys (determined by regex)
* .pem files

#### Install in all new repositories

** Note that this will overwrite any prior pre-commit and pre-push hooks you may have already installed**

```bash
cp -R git_template ~/.git_template

git config --global init.templatedir '~/.git_template'

```
#### Install into any pre-existing respositories

Having done the previous step, run the following to sync git hooks with the defaults in `~/.git_template`:

```bash
$(git config --path --get init.templatedir)/update.sh
```

#### Install intall all pre-existing respoisitories in a directory

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
