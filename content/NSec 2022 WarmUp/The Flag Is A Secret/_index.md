+++
title = "The Flag Is A Secret"
description = ""
weight = 3
+++

Challenge statement:
```
Dylan got his hands on some sensitive source code. Watch his video above to find out why he needs your help...
🔗https://security.love/nsctfmc/hello.txt
```

After downloading the [hello.txt](hello.txt) file, we can see that it's a base64 blob.

We can convert it using `cat hello.txt | base64 -d > hello.file`.\
Then we can use the file utility to get an idea of what it is.

```bash
$ file hello.file 
hello.file: Zip archive data, at least v1.0 to extract
```

```bash
$ unzip hello.zip
Archive:  hello.file
   creating: repo/
   creating: repo/.git/
  inflating: repo/.git/config        
   creating: repo/.git/objects/
   creating: repo/.git/objects/pack/
  inflating: repo/.git/objects/pack/pack-eaff52f56b425e5b5ac21716b9d9fdb56e6b2c7a.pack  
   creating: repo/.git/objects/info/
 extracting: repo/.git/objects/info/packs  
 extracting: repo/.git/HEAD          
   creating: repo/.git/info/
  inflating: repo/.git/info/exclude  
  inflating: repo/.git/info/refs     
   creating: repo/.git/logs/
  inflating: repo/.git/logs/HEAD     
   creating: repo/.git/logs/refs/
   creating: repo/.git/logs/refs/heads/
  inflating: repo/.git/logs/refs/heads/master  
  inflating: repo/.git/description   
   creating: repo/.git/hooks/
  inflating: repo/.git/hooks/commit-msg.sample  
  inflating: repo/.git/hooks/pre-rebase.sample  
  inflating: repo/.git/hooks/pre-commit.sample  
  inflating: repo/.git/hooks/applypatch-msg.sample  
  inflating: repo/.git/hooks/fsmonitor-watchman.sample  
  inflating: repo/.git/hooks/pre-receive.sample  
  inflating: repo/.git/hooks/prepare-commit-msg.sample  
  inflating: repo/.git/hooks/post-update.sample  
  inflating: repo/.git/hooks/pre-applypatch.sample  
  inflating: repo/.git/hooks/pre-push.sample  
  inflating: repo/.git/hooks/update.sample  
   creating: repo/.git/refs/
   creating: repo/.git/refs/heads/
   creating: repo/.git/refs/tags/
 extracting: repo/.git/index         
  inflating: repo/.git/packed-refs   
 extracting: repo/.git/COMMIT_EDITMSG
```

We can see the packed object that is not usually in a clean repository. The repository is also not reacting normally : 
```bash
$ git log
fatal: bad object HEAD
```

```bash
$ cat objects/pack/pack-eaff52f56b425e5b5ac21716b9d9fdb56e6b2c7a.pack | git unpack-objects
Unpacking objects: 100% (11/11), 981 bytes | 981.00 KiB/s, done.
```

```
objects
├── 13
│   └── 371d7a0caf60d080d2f9196785694736cb926c
├── 29
│   └── 4b2a22f4beaaf42446c64f50d46e023e420164
├── 2b
│   └── 1ab12f55009cd98c2250e3e1dbe8777644b740
├── 2c
│   └── cd75ee92dddff522871ce4ebedd02aed08ba97
├── 3c
│   └── a4066edd31c5d9b8e6857073685e0cbd6680cd
├── 41
│   └── f7e66043fabb8280cb8fc13df101b080364ad2
├── 48
│   └── bda2b5df64b379731f0e78f4aca7c5e132b716
├── 4b
│   └── 825dc642cb6eb9a060e54bf8d69288fbee4904
├── 79
│   └── 1c0676fa6f9767ed44d0300973ab51bb9dd668
├── 8e
│   └── f2fc432808c80ae5e95cd485e2de858c69a839
└── a8
    └── fe3389a1d422e1c621b52b67b7aefabe862dd5
```

Simply using the following could give us the full file, but we got lucky since we did not have to compute diffs between commits :

```
$ cat pack/pack-eaff52f56b425e5b5ac21716b9d9fdb56e6b2c7a.pack | git cat-file --batch --batch-all-objects
[...]
2ccd75ee92dddff522871ce4ebedd02aed08ba97 blob 16
No flag here...

3ca4066edd31c5d9b8e6857073685e0cbd6680cd blob 151

[default]
aws_access_key_id = AKIAYVP4CIPPPFRDPNGX
aws_secret_access_key = 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
output = json
region = us-east-2


41f7e66043fabb8280cb8fc13df101b080364ad2 blob 46
hey, this is the flag file, where's the flag?
[...]
```

Once we called git unpack-objects, the repository became functional and allows us to checkout the different commits or do git diffs to see the different modifications. The file was not modified over time, so the git cat-file was sufficient to give us the aws configuration.


```
[default]
aws_access_key_id = AKIAYVP4CIPPPFRDPNGX
aws_secret_access_key = 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
output = json
region = us-east-2
```

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

```bash
$ AWS_CONFIG_FILE=aws.config aws sts get-caller-identity
{
    "UserId": "AIDAYVP4CIPPFFVW4CXJG",
    "Account": "595918472158",
    "Arn": "arn:aws:iam::595918472158:user/canarytokens.com@@efklxdafbz2t6lcpp66bj8c61"
}
```

```bash
$ AWS_CONFIG_FILE=aws.config aws s3 ls

An error occurred (AccessDenied) when calling the ListBuckets operation: Access Denied
```

https://www.filestash.app/s3-browser.html

After more than one hour of trying to use multiple tools to figure out which access we have using those credentials, I gave up and sent in the secret access key "qa26OSZwBYzHzeOOQZHTYZaNPOzcGT9EnZUoq44c". That was the flag.

If the challenge went ahead with the AWS credentials, instead of being a honeytoken, we could use the [Pacu](https://github.com/RhinoSecurityLabs/pacu) tool to learn more about our permissions.