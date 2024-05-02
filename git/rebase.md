# Rebase

[Source](https://git-scm.com/docs/git-rebase)

## Simple one branch rebase

Before the rebase:

```
          A---B---C topic
         /
    D---E---F---G master
```

To rebase:

```
git rebase master
git rebase master topic
```

After:

```
                  A'--B'--C' topic
                 /
    D---E---F---G master
```

Remember to push --force or even better --force-with-lease

## Rebase depending branches

Before the rebase:

```
                    H---I---J topicB
                   /
          E---F---G  topicA
         /
    A---B---C---D  master
```

To rebase:

```
git rebase --update-refs master topicA
```

After:

```
                            H---I---J topicB
                           /
                  E---F---G  topicA
                 /
    A---B---C---D  master
```

## Preserve Merges

TODO --preserve-merges

