# Signed Commit

How to do signed commits with [GPG](./../it-sec/cryptographics/gpg.md)

## Git Config

### Enable GPG signing

To configure your Git client to sign commits by default for a local repository, in Git versions 2.0.0 and above, run
```
    git config commit.gpgsign true
```
To sign all commits by default in any local repository on your computer, run
```
    git config --global commit.gpgsign true
```

### Add Signing key

```
git config --global user.signingkey ABCD1234
```

### Set GPG Program

```
git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"
```


## Sign commit

When committing changes in your local branch, add the -S flag to the git commit command:

```
git commit -S -m "YOUR_COMMIT_MESSAGE"
```

If you're using GPG, after you create your commit, provide the passphrase you set up when you generated your GPG key.

When you've finished creating commits locally, push them to your remote repository on GitHub:

```
git push
```