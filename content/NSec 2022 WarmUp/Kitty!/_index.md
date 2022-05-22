+++
title = "Kitty!"
description = ""
weight = 1
+++

Challenge statement:
```
Examine the kitty.jpeg file to find the flag.
🔗https://drive.google.com/file/d/1XWD2aurnHEVzwcd3hub8vOW4AlbZKJDy/view?usp=sharing
```

The [kitty.jpeg](kitty.jpeg) looks like a very valid picture.

Since it is the first challenge of the warmup, we can start with a simple approach.

```bash
$ strings kitty.jpeg | grep -i nsec
nsec2022{kitcam2022}
```

Well... here's our flag! \
That went faster than expected. \
I was going to try with other words like ctf and flag, but the competition name is a great start.

Another way to find it was to do the same using exiftool:

```bash
$ exiftool kitty.jpeg  | grep -i nsec
Camera Model Name               : nsec2022{kitcam2022}
```