# Git LFS Test

This repository was used while testing [this blog post][blog-post] and [Git LFS][git-lfs]. Specifically, we wanted to make sure we understood what would happen if a client _without LFS installed_ cloned an LFS-enabled repository.

As you might expect, the client with LFS installed downloaded the LFS-tracked binaries during the `git clone` process, with no additional work on the part of the user. On the client _without_ LFS installed, only the pointer file was downloaded, with no other errors or warnings.

The specific steps taken during testing can be found below. The environments used in testing were:
- This repo was originally created in OSX.
- The LFS-enabled environment was a Docker container from the `jansauer/docker-lfs` image.
- The non-LFS environment was a Docker container from the `samueldebruyn/debian-git` image.

More discussion on the topic can be found in the [original post][blog-post].

[blog-post]: http://blog.testdouble.com/posts/2017-01-25-how-and-why-to-use-git-lfs.html
[git-lfs]: https://git-lfs.github.com/

## Steps to create this repository

1. Created and cloned this GitHub repo normally.
1. Installed Git LFS to track all JPG files: `git lfs track '*.jpg'`
1. Added and committed the new `.gitattributes` file.
1. Added a binary for LFS to track: `git add binary.jpg`
1. Committed the new image.
1. Pushed to GitHub.

```
Git LFS: (1 of 1 files) 606.22 KB / 606.22 KB
Counting objects: 6, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 704 bytes | 0 bytes/s, done.
Total 6 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), done.
To github.com:Schoonology/git-lfs-test.git
   cdc9fb6..5a24879  HEAD -> master
```

## Test with LFS installed

```
docker run -it jansauer/docker-lfs /bin/sh
...
/ # git clone https://github.com/Schoonology/git-lfs-test.git
Cloning into 'git-lfs-test'...
remote: Counting objects: 9, done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 9 (delta 1), reused 6 (delta 1), pack-reused 0
Unpacking objects: 100% (9/9), done.
Checking connectivity... done.
Downloading binary.jpg (606.22 KB)
/ # cd git-lfs-test/
/git-lfs-test # ls -l
total 612
-rw-r--r--    1 root     root            14 Jan 13 18:24 README.md
-rw-r--r--    1 root     root        620773 Jan 13 18:24 binary.jpg
```

## Test without LFS installed

```
docker run -it samueldebruyn/debian-git /bin/sh
...
# git clone https://github.com/Schoonology/git-lfs-test.git
Cloning into 'git-lfs-test'...
remote: Counting objects: 9, done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 9 (delta 1), reused 6 (delta 1), pack-reused 0
Unpacking objects: 100% (9/9), done.
Checking connectivity... done.
# cd git-lfs-test
# ls -l
total 8
-rw-r--r-- 1 root root  14 Jan 13 18:29 README.md
-rw-r--r-- 1 root root 131 Jan 13 18:29 binary.jpg
# cat binary.jpg
version https://git-lfs.github.com/spec/v1
oid sha256:6fe2e48ef494b7855e6d8f93c59cf38d38350274e304a9ac35102d5c365ee002
size 620773
```
