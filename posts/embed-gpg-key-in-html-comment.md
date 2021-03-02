---
title: "Embedding public gpg key in HTML comment"
date: 2020-12-12T16:33:33+0100
draft: false
tags: ["gpg","HTML","public key"]
---

Cool idea from [Travis](https://travisf.net).
This will enable to import my public key simply by running `curl https://cbbg.nl | gpg --import`. Pretty cool huh? Worth the few extra bytes at each page load? Hell yeah!

In `config/_default/params.toml` 

```
key='''
<!--
-----BEGIN PGP PUBLIC KEY BLOCK-----

xsFNBFRABlQBEADpJnaPhAWHkAw7kiRq5xdvh51BC86O2aQMALKlmNesSNR/aIIk
...................... more key data ...........................
GBNnmKCglTQSqNnWFyq6lKqus01dG4c+f4BUnQaqllUwcnCqBGjxkCLx8JTXaEoa
v68=
=T92X
-----END PGP PUBLIC KEY BLOCK-----
-->
'''
```

Somehere in the footer partial of my theme I added this:
```
{{ if .IsHome }}
  {{ site.Params.key | safeHTML }}
{{ end }}
```
This will only include the raw HTML code including the key on the homepage.
