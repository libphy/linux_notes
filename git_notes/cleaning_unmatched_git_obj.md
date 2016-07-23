I found my remote git repo was too slow to download and its size was big even though I had just a few codes.
I found my .git folder's objects folder has a huge object.  
In the local git repo run a bash command  

```bash
du -hs .git
```

The result was 70+MB although I had only a few codes in my repo.
The reason is that I committed/pushed a big ipynb file then manually moved/deleted them,
but the packing object .git still has the history.
Following bash command ([source: stackoverflow](https://stackoverflow.com/questions/1029969/why-is-my-git-repository-so-big/14329983#14329983?newreg=8fabebe39b5a40e6929ff4a48f7a2421)) will print out which files are associated with git objects.

```bash
git rev-list --all --objects | \
    sed -n $(git rev-list --objects --all | \
    cut -f1 -d' ' | \
    git cat-file --batch-check | \
    grep blob | \
    sort -n -k 3 | \
    tail -n40 | \
    while read hash type size; do
         echo -n "-e s/$hash/$size/p ";
    done) | \
    sort -n -k1
```

I've got a result like
```
68003 Test.ipynb
87616 code/peaks.ipynb
341643 Test.ipynb
449281 Test.ipynb
956382 data/cat/1650_Katze_Maunzen_short.wav
1686750 data/dog/1649_Haushund_Bellen_short.wav
1822724 data/cat/Felis_silvestris_f_domestica_V1543_23_short.wav
1947140 data/cat/Felis_silvestris_f_domestica_S0049_01_short.wav
1984004 data/cat/Felis_silvestris_f_domestica_S0001_01_short.wav
2258247 code/EDA.ipynb
11236185 code/EDA.ipynb
103013455 code/inspect_cat.ipynb
```
Then I ran following (removing code/inspect_cat.ipynb alone pushed my .git size down to 14M)

```bash
git filter-branch -f  --index-filter \
    'git rm --force --cached --ignore-unmatch (replace with the filepath)' \
     -- --all
rm -Rf .git/refs/original && \
    git reflog expire --expire=now --all && \
    git gc --aggressive && \
    git prune
```
