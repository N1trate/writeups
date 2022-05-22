+++
title = "warmup flag (actual)"
description = ""
weight = 8
+++

Challenge statement:
```
Here's a flag for you. Can you flag the flag?
```

https://drive.google.com/file/d/1EJrd_iDuY9xtyMmv6L9vdjbDM8K-t5RR/view?usp=sharing

We get a base64 blob, so first step first, we can recreate the real file with
```bash
cat actualflag.txt | base64 -d > actualflag.bin
```

We can use the file command to get a guess of what we have at hand
```bash
$ file actualflag.bin 
actualflag.bin: PNG image data, 149 x 247, 8-bit/color RGBA, non-interlaced
```

Using exiftool, we could check what the metadata of that PNG file is, which may give us an hint.
```bash
$ exiftool actualflag.bin
ExifTool Version Number         : 11.88
File Name                       : actualflag.bin
Directory                       : .
File Size                       : 3.3 kB
[...]
File Permissions                : rw-rw-r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 149
Image Height                    : 247
Bit Depth                       : 8
Color Type                      : RGB with Alpha
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
SRGB Rendering                  : Perceptual
Gamma                           : 2.2
Pixels Per Unit X               : 3780
Pixels Per Unit Y               : 3780
Pixel Units                     : meters
Creation Time                   : 2022:02:22 22:52:50
Warning                         : [minor] Trailer data after PNG IEND chunk
Image Size                      : 149x247
Megapixels                      : 0.037
```

We can see one Warning toward the end, which says that we have trailing data. We can then use hexdump -C to check what that data look like.
```bash
$ hexdump -C actualflag.bin | tail
00000cc0  c4 73 93 eb ce 5a 4f b2  bc d2 a8 2c 6d 7b 56 79  |.s...ZO....,m{Vy|
00000cd0  82 be 57 72 04 57 af 96  c3 f3 75 59 1c 19 a7 64  |..Wr.W....uY...d|
00000ce0  6e 5b 42 72 9d 4d a3 c9  e4 a8 b0 83 6d eb e8 f3  |n[Br.M......m...|
00000cf0  26 cb ed ad db 97 b7 fa  58 2b f1 9b a7 72 60 9d  |&.......X+...r`.|
00000d00  c6 d7 76 cf e4 6e c3 51  2c be a3 bf 2e 59 b5 3b  |..v..n.Q,....Y.;|
00000d10  39 d2 ae 5f 85 a2 2b 54  4d 85 fe 50 a8 9a 0a fd  |9.._..+TM..P....|
00000d20  40 e4 ff 81 d2 f3 0d c2  e2 e5 de 00 00 00 00 49  |@..............I|
00000d30  45 4e 44 ae 42 60 82 42  32 32 39 2d 30 30 31 35  |END.B`.B229-0015|
00000d40  35 64 34 38 30 34 30 62  7d                       |5d48040b}|
00000d49
```

Those last bytes looks like the ending of a flag, so we need to find the start of it.\
I tried to upload the PNG file to an online metadata viewer, and one thing it gave me is actually to show me the picture itself.\
![actualflag](actualflag.bin)

I guess that was it! The full flag was FLAG-{1a91dd46-945d-11ec-B229-00155d48040b}.
