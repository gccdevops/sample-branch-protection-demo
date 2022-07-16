# sample-branch-protection-demo

## sign commit

- try a sign commit 
```text
cqiao2@LN1GZD5Z0B467C:~/desktop/repos/sample-branch-protection-demo$ git commit -S -m 'update'
error: gpg failed to sign the data
fatal: failed to write commit object
```  

- create a gpg key
```shell
cqiao2@LN1GZD5Z0B467C:~$ gpg --gen-key
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: chaoqiao
Email address: chao.m.qiao@foxmail.com
You selected this USER-ID:
    "chaoqiao <chao.m.qiao@foxmail.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? O
```

- list gpg keys
```shell
cqiao2@LN1GZD5Z0B467C:~$ gpg --list-keys
/home/cqiao2/.gnupg/pubring.kbx
-------------------------------
pub   rsa3072 2022-07-15 [SC] [expires: 2024-07-14]
      6EE08F0DF058A89B6526BF30F7981FB9AEAD30A7
uid           [ultimate] chaoqiao <chao.m.qiao@foxmail.com>
sub   rsa3072 2022-07-15 [E] [expires: 2024-07-14]

cqiao2@LN1GZD5Z0B467C:~$
```

- export gpg public key
```
cqiao2@LN1GZD5Z0B467C:~$ gpg --export --armor chaoqiao > chaoqiao.gpg.pem
cqiao2@LN1GZD5Z0B467C:~$ cat chaoqiao.gpg.pem
-----BEGIN PGP PUBLIC KEY BLOCK-----

mQGNBGLRM3cBDACoV1D1gYlrO3tN1TZGMCZmbh8ehX8rkyCuv1giVUc/1DiZS9ch
...
MeM1JrZnWNZjylLjdi55M7Feapr3fPqyH2/zyOlRnDPPEgHjwHXCC1m5Agt7S6jl
s4Ie5Ta
-----END PGP PUBLIC KEY BLOCK-----
cqiao2@LN1GZD5Z0B467C:~$
```

- import gpg public key to github gpg

- setup git with gpg key
```shell
git config user.signingkey 6EE08F0DF058A89B6526BF30F7981FB9AEAD30A7
git config commit.gpgsign true
```

- fix issue 1
```text
gpg: signing failed: Inappropriate ioctl for device
gpg: [stdin]: clear-sign failed: Inappropriate ioctl for device
```
```shell
export GPG_TTY=$(tty)
ubuntu@ip-172-31-30-60:~$ echo "test" | gpg --clearsign
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

test
-----BEGIN PGP SIGNATURE-----

iQGzBAEBCgAdFiEEbuCPDfBYqJtlJr8w95gfua6tMKcFAmLRcGcACgkQ95gfua6t
MKeiAgwAiXecjOnabH2rHjzCjTHumOVJmocgbGERYDlqBe0twEFj0FPDCSKKTZ3n
YNCxm1cA/s1TlUGWTS7zbi/fjDFiGi51rlKnDqG3X6oHu84PgfYdCVfETDlEeCQR
xUNY1k/vU3si/t3giDn2ck6mxF0hOCBRwZ5fw+TMig4W+sD9PNJs9yCVO4YLXOsV
bokh5f0QcxpQZ9z4Wtqy6zOW07egOXjCGPBzW3sgLnyhATJ53nX5MQmWDDG6G4PB
2HT7KfpDB1HLNbGdA6BMkiJx4wIQVFhtMAwNYOg/frgW0pbClW6bJIhPsS2nkj0L
EjV2FuD1SyhnAxka4JEhQS8+W3BSnUFROINfPu7w+x2CEBt/y6dGNo5+hdUK/0+Q
izG82xgg3kFx4oyl+hJk1CYVGxEJNJNAqIhnppdFaya6bW8mNeKu4DQVQwlaBzhC
JFoMD5MdSTkhwla7+rg3xIeLV5/KW1HT7M0Ip13N+6xm/zwbVVDx6OiuQhRh6DO4
4p0D9JPO
=rTXX
-----END PGP SIGNATURE-----
ubuntu@ip-172-31-30-60:~$
```

- verify sign commit
```text
cqiao2@LN1GZD5Z0B467C:~/repos/sample-branch-protection-demo$ git log --show-signature -1
commit cbfaea4c0c9bcee31b60f6b6d39c86651158249e (HEAD -> fix-002, origin/fix-002)
gpg: Signature made Sat Jul 16 10:10:32 2022 CST
gpg:                using RSA key 6EE08F0DF058A89B6526BF30F7981FB9AEAD30A7
gpg: Good signature from "chaoqiao <chao.m.qiao@foxmail.com>" [ultimate]
Author: chaoqiao <chao.m.qiao@foxmail.com>
Date:   Sat Jul 16 10:10:32 2022 +0800

    fix codeowner issue
```

## pull request review from code owner
- add codeowners file 
```text
file should be add in .github/CODEOWNERS
People with admin or owner permissions can set up a CODEOWNERS file in a repository.

The people you choose as code owners must have read permissions for the repository. 
When the code owner is a team, that team must be visible and it must have write permissions, 
even if all the individual members of the team already have write permissions directly, through organization membership, or through another team membership.
```

## Dismiss stale pull request approvals when new commits are pushed
```text
Optionally, to dismiss a pull request approval review when a code-modifying commit is pushed to the branch, select Dismiss stale pull request approvals when new commits are pushed.
```

## Restrict who can dismiss pull request reviews
```text
If your repository requires reviews, you can dismiss pull request reviews that are no longer valid or are unable to be approved by the reviewer.
```

## Allow specified actors to bypass required pull requests
```text
Optionally, to allow specific actors to push code to the branch without creating pull requests when they're required, 
select Allow specified actors to bypass required pull requests. Then, search for and select the actors who should be allowed to skip creating a pull request.
```
