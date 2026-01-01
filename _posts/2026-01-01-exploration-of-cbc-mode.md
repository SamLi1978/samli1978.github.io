---
layout: post
title: Exploration of CBC Mode
date: 2026-01-01 12:48 -0500
categories: [Cryptography]
tags: [Symmetric encryption]
---


As is well known, the AES encryption algorithm has a block size of 128 bits, with key lengths of 128, 192, or 256 bits. Next, we will use OpenSSL to randomly generate an AES encryption key (256-bit) and an initialization vector (128-bit).

`openssl rand -hex 32 > aes.key`
`openssl rand -hex 16 > aes.iv`

The generated content of aes.key is as follows:

`9d4c522427edc26b31d881fc0f2ea177af7989301c1b741ddb74993a7998a24b`

The generated content of aes.iv is as follows:

`c4d559d78c5d36b7c188f953997fe8ec`

Assume the content of text.txt is as follows, with a length of 1810 bytes.
```
Trichogenes claviger

From Wikipedia, the free encyclopedia

Trichogenes claviger, the Caetés catfish, is a critically endangered species of freshwater ray-finned fish belonging to the family Trichomycteridae, the pencil and parasitic catfishes. This species is endemic to streams in the Atlantic Forest of Brazil. It was discovered early in 2010 and scientifically described later that year. One of three species within the genus Trichogenes, it is restricted to an area of 16 km2 (6.2 sq mi) in the Caetés forest, a mountainous area in the Brazilian state of Espírito Santo. When discovered, the rainforest in which it occurs was unprotected and threatened by deforestation. A private nature reserve has since been established, allowing visitors to see the fish in its habitat.

A small fish, T. claviger is up to 50.8 mm (2.00 in) in length. A series of black dots runs along the side of the body, distinguishing it from related species. Males have a bony protrusion from the gill area (the opercular process) that is elongated and club-like, a feature that inspired the name of the species (claviger – 'club-bearing'). The opercular process in T. claviger is the only known secondary sex characteristic in pencil catfishes, and might have evolved for sexual signaling; it is also used by the fish to climb up net walls when caught. The mouth is terminal (faces forwards rather than being upturned or downturned); this feature is also found in its closest relative, T. beagle, but absent in all other members of their family. It is known to gulp air from the water surface; when carrying air, the body tilts downward. The species lives in small, shaded, and slow-moving streams in the rainforest, and mostly feeds on insects that have fallen on the water surface. It is the only fish in its habitat. 
```

We will use OpenSSL aes-256-cbc to encrypt this text.txt. The key and IV are aes.key and aes.iv, respectively.

`openssl aes-256-cbc -e -in text.txt -out text.enc -K $(cat aes.key) -iv $(cat aes.iv)
`

The ciphertext generated in text.enc is as follows, with a length of 1824 bytes.
```
00000000: d796 71e0 3c31 0c72 0d04 5b1d d54b 61e5  ..q.<1.r..[..Ka.
00000010: 1f4c 7444 d5ae c28d e09d 54d1 a211 9207  .LtD......T.....
00000020: 0a19 438c 1970 6c2b 85b5 e1e3 ebc9 ad9b  ..C..pl+........
00000030: ef42 bf4a 8ccd 935f be9b 68a4 f18a 0a50  .B.J..._..h....P
00000040: fab5 adfc 374c e05e 3cc7 dabf 5b58 2389  ....7L.^<...[X#.
00000050: 2e1a 56b7 74a2 be4a 7fcb a70a 2280 d8b2  ..V.t..J...."...
00000060: 77c5 4c90 20f6 c4e6 4176 0d4f a2eb 1f58  w.L. ...Av.O...X
00000070: bb9a e371 70b2 4b76 84e2 4c64 7c39 3127  ...qp.Kv..Ld|91'
00000080: 56ad affe 3ba1 c08b 4c2e d99f de2c 2520  V...;...L....,% 
00000090: 1eb8 417a f776 e013 fcf6 db42 b22e 142f  ..Az.v.....B.../
000000a0: 13a1 8718 bbcb 63e3 3236 5ff9 6959 0110  ......c.26_.iY..
000000b0: 1558 ece1 a3c7 94fa 77d3 97a8 22ed 9995  .X......w..."...
000000c0: 0445 f4e3 c84b 16a2 aa3f 2480 0f51 4a41  .E...K...?$..QJA
000000d0: 699c a30c 43f0 393d 5d9f 3bbe dcd9 6c0f  i...C.9=].;...l.
000000e0: ae28 db49 877d 440f 2eb5 5bae 917c 1fe5  .(.I.}D...[..|..
000000f0: 107c 4f8e 11d7 1b6f b366 739c 1632 baa0  .|O....o.fs..2..
00000100: 0ed9 6c21 d2f5 f3d5 91ea 01dc 1582 1a98  ..l!............
00000110: 4993 33fa 960d 3803 a516 12b5 4198 694e  I.3...8.....A.iN
00000120: e697 b408 1317 d2a2 0d2f 6990 05e3 111b  ........./i.....
00000130: 9fe1 463c a127 50d4 bdf6 82be 118f d84d  ..F<.'P........M
00000140: 1aaf 474e cf9e 5b63 6013 bcda 5eb7 178a  ..GN..[c`...^...
00000150: 3e84 3784 e587 4d95 6138 d47a ad02 a448  >.7...M.a8.z...H
00000160: ad71 e408 ee79 ce0a 350a 6607 3152 b331  .q...y..5.f.1R.1
00000170: 782d 0376 f039 2dd1 20fa 7f8d 2824 ec55  x-.v.9-. ...($.U
00000180: 47d8 0a4a 6f9a 83a9 07d0 e7eb 481e d9d7  G..Jo.......H...
00000190: da0b 1258 23f6 3f5b 924d 02e3 1cca b73b  ...X#.?[.M.....;
000001a0: 58eb 3fe4 b9b0 9419 2af9 c6a0 3a34 08b5  X.?.....*...:4..
000001b0: fbf2 e3b8 829c 2587 dae8 2aa8 0542 66e7  ......%...*..Bf.
000001c0: e18f 74d0 5074 3b81 b71e 7a22 3a4c 08a7  ..t.Pt;...z":L..
000001d0: 357f 95bb 470a 4d39 f62d 0443 93e4 cb79  5...G.M9.-.C...y
000001e0: 18d3 0116 fffe fe88 729e 5ac8 6709 286b  ........r.Z.g.(k
000001f0: b8d1 0a9f 6d9e 47a5 fef1 aa5d 5dd7 6a54  ....m.G....]].jT
00000200: f37c e73d 7c2f 6ce7 3881 8f0e 0751 266a  .|.=|/l.8....Q&j
00000210: a9ed 1a78 765a 00e7 6839 7148 c59a 9e5e  ...xvZ..h9qH...^
00000220: a1c5 3ee2 cb84 1d74 4fba 01ef c1da d224  ..>....tO......$
00000230: 89cd 1609 b372 55c8 630d 4a28 7116 b553  .....rU.c.J(q..S
00000240: 9deb 7f2a d853 aa28 24a1 93c0 f74f b879  ...*.S.($....O.y
00000250: 4f64 fab8 eaea cfb9 a273 24c8 a4fb 77eb  Od.......s$...w.
00000260: cd41 1cbe 2e19 894c 51e3 76d2 32bc 1257  .A.....LQ.v.2..W
00000270: 629b e241 6d41 1f70 0d3d d037 0a37 7c8e  b..AmA.p.=.7.7|.
00000280: b530 60fb 7e54 58c9 a0a5 b958 9c35 6a26  .0`.~TX....X.5j&
00000290: c2c7 8019 4023 8c6b e866 e98a aaba b001  ....@#.k.f......
000002a0: 23d7 352e ba56 27fb ac51 c8e7 5166 f7f6  #.5..V'..Q..Qf..
000002b0: d0c6 7d02 8b59 3e70 985f 76b2 9353 f957  ..}..Y>p._v..S.W
000002c0: 4f30 031b 0df3 1b2d 5bdd ef00 99af 0fa3  O0.....-[.......
000002d0: a11a a0f5 d9cf 9e9b a3e9 82dd 06c5 5510  ..............U.
000002e0: c718 389a f92e 0681 53b1 af68 de86 4132  ..8.....S..h..A2
000002f0: 35d9 c775 e9c0 ee59 5559 4821 faa4 0c09  5..u...YUYH!....
00000300: c87e 221f ddb5 2125 c0c1 b7c1 c6fc 9d15  .~"...!%........
00000310: 7855 92af e905 09c7 de93 e5cf 83c7 a935  xU.............5
00000320: 9842 534c baea 8dc7 d7d6 b748 f8da 4409  .BSL.......H..D.
00000330: cfee 0534 9b4e 6079 c7bc 441b 4d8a 17c8  ...4.N`y..D.M...
00000340: 3103 35e6 574d 0faa af92 a069 16b2 aa3a  1.5.WM.....i...:
00000350: c241 2eed 973a 0b0a d437 57e9 4d89 401b  .A...:...7W.M.@.
00000360: ef1b b69b 4e9c 6551 afb1 88d3 1976 8f4a  ....N.eQ.....v.J
00000370: 3b85 fbe9 4be9 8a97 14e3 fb1c 0eef f1c4  ;...K...........
00000380: 32ef 08b2 424c c0d2 c425 854e 758e 407a  2...BL...%.Nu.@z
00000390: cd95 4826 0a8b 8ffd bd8e 6625 94a2 4553  ..H&......f%..ES
000003a0: ce31 17a2 6deb f162 b67e 85b2 1048 eda5  .1..m..b.~...H..
000003b0: cc91 9580 a0fe 50c0 a5c1 e272 ef03 d57a  ......P....r...z
000003c0: 02b9 f055 ec80 3e04 57bf 30a6 2117 47b0  ...U..>.W.0.!.G.
000003d0: 4744 980c 2b3f b53f 1043 a197 f216 0f84  GD..+?.?.C......
000003e0: 1b44 76d9 7ba0 a17d 6510 4aa7 f2b7 e679  .Dv.{..}e.J....y
000003f0: 6646 4e22 19dc 9604 5bb4 7611 5553 feba  fFN"....[.v.US..
00000400: f2dd 823f 146f cb26 5330 86bd 01cc 9bd6  ...?.o.&S0......
00000410: b488 9862 3b8c b222 c266 a1bc 2133 1717  ...b;..".f..!3..
00000420: c8a4 d6b4 072c fcae fef8 5a48 1253 457e  .....,....ZH.SE~
00000430: 6603 8e93 3edf da7e 709a 8d36 a073 0cbb  f...>..~p..6.s..
00000440: 1ab2 c323 ad70 ad77 3feb 4e27 201a 9607  ...#.p.w?.N' ...
00000450: 0e2b ec70 4822 1d72 8ddf 2ba7 ae67 6154  .+.pH".r..+..gaT
00000460: 249c 2718 00d5 fef6 2f11 b44f 6813 1881  $.'...../..Oh...
00000470: 56b2 6325 59fe f327 28e7 cbfc 24bb 2fa1  V.c%Y..'(...$./.
00000480: d088 3d71 a50c c7a2 6478 f0a2 993f d2ce  ..=q....dx...?..
00000490: 05e4 7615 6540 9426 b9ee 5d88 ce19 ffaf  ..v.e@.&..].....
000004a0: 7d14 936c 74ff f4dd 8737 103b f843 b446  }..lt....7.;.C.F
000004b0: c523 c953 67a8 2193 e53b 1f2c 63d9 3b65  .#.Sg.!..;.,c.;e
000004c0: af2e 45f6 a99b d7bd 36f7 c9cc e690 89ba  ..E.....6.......
000004d0: 1fcf db0d 8724 a91a e0f8 a277 4079 9373  .....$.....w@y.s
000004e0: 569b d483 0500 e1a7 d625 ef4a 404c 8acc  V........%.J@L..
000004f0: 8105 b1f0 020b 708e df37 aa1e 3ba2 d7ae  ......p..7..;...
00000500: fa1d b30e 7a09 36ae 9c41 62c3 46eb 6c73  ....z.6..Ab.F.ls
00000510: be52 7211 6ed5 a949 0cff b959 9f66 f0ba  .Rr.n..I...Y.f..
00000520: 547a f76c 61b7 a84c 7f83 2703 ab9a d6f4  Tz.la..L..'.....
00000530: 7b85 3e61 5906 39de d482 4b88 7b32 cf62  {.>aY.9...K.{2.b
00000540: caa4 f5a2 3bad f0b1 d8ce d6c5 9b17 3914  ....;.........9.
00000550: 38c3 1d12 7e3a 18ee fc26 3d4b 3062 cb13  8...~:...&=K0b..
00000560: d6f9 8f3a 343e 8603 611c d352 6644 e0fd  ...:4>..a..RfD..
00000570: a6e9 a844 de82 9198 5f47 f733 daac 9890  ...D...._G.3....
00000580: 10df ed83 6f8b 0e1c 9111 b790 b39d ea3b  ....o..........;
00000590: f57a 4d49 66a6 81af 82c5 3325 3acb 05d7  .zMIf.....3%:...
000005a0: 7c0b adea bedc edb8 fa3e 5762 36e6 a191  |........>Wb6...
000005b0: ed47 5dae fcff 7fef 9682 d7ed 59d1 7f6c  .G].........Y..l
000005c0: 7127 9687 7311 fcfa 213c 43f2 5035 6569  q'..s...!<C.P5ei
000005d0: c826 e7b5 be91 b932 0f00 d06c 9afa 7e06  .&.....2...l..~.
000005e0: 2748 86bc 474c 98b4 3efd ae1b 3980 5737  'H..GL..>...9.W7
000005f0: a253 0acf 1bfc c472 3936 b621 d298 04cf  .S.....r96.!....
00000600: a7c8 c79c 3e70 a8fb b5fa 517c b2e6 4980  ....>p....Q|..I.
00000610: 6398 4955 791c 0640 f730 88a4 c206 b81d  c.IUy..@.0......
00000620: d34f b81a b1a9 5d4a 4e9d 9f21 cd48 d8a8  .O....]JN..!.H..
00000630: d0cc 43a3 d51e bd9d 17a3 f9c1 b0fc cd26  ..C............&
00000640: 5f81 aeb9 657b 33dd 75d7 baa8 4bc2 e9f6  _...e{3.u...K...
00000650: 1982 0b09 d9eb abbf fd36 94e2 fdfa 21ae  .........6....!.
00000660: 4457 46a0 028f 7d80 5f0c 2518 7b06 6c55  DWF...}._.%.{.lU
00000670: 36a6 4fa0 58d3 8d7e f571 12cd d40f b493  6.O.X..~.q......
00000680: 26b6 5148 a284 de0f 7a0c c662 bf7c 7136  &.QH....z..b.|q6
00000690: 0d25 0cd1 b8a8 31c9 2303 52af 7627 2b12  .%....1.#.R.v'+.
000006a0: c2f1 0e2f 1f5e 717a 7fc6 fc7e 94f3 bdfe  .../.^qz...~....
000006b0: 818f 55a7 99f9 3064 87a7 3884 4ac8 9ca4  ..U...0d..8.J...
000006c0: 2ff5 4639 d5d3 7313 0af8 5ffa d7b6 6246  /.F9..s..._...bF
000006d0: 38cd 1dca 1e57 d52a ca23 3c30 afed 78ee  8....W.*.#<0..x.
000006e0: e03a 03c5 3aa7 6d5c cf53 7cb7 6212 3358  .:..:.m\.S|.b.3X
000006f0: b5ba 7e46 21b6 6e82 992d 7e5c 0dc2 b2b7  ..~F!.n..-~\....
00000700: 55c5 af04 b51e 2a06 e2af b342 05b0 ad25  U.....*....B...%
00000710: ee06 6060 1f0f a272 3807 e4ad 51b5 0694  ..``...r8...Q...
```

OpenSSL by default uses PKCS#7 padding to pad the plaintext. Since the original plaintext length is 1810 bytes, and 1810 divided by 16 has a remainder of 2, the last block is padded with 16 - 2 = 14 bytes, with each padded byte being 0x0E (14 in decimal).

If the ciphertext recipient only knows the encryption key but does not know the initialization vector, can they still decrypt it? The answer is basically yes, except for the first block (16 bytes).

For example, if we randomly generate an initialization vector (assuming the recipient does not know the IV used for encryption),

`openssl rand -hex 16 > aes.fakeiv`

The content is as follows:

`65f66cf07c6d826cf6f334a98e22cf67`

Then, we decrypt the ciphertext using this random initialization vector (while still using the same key).

`openssl aes-256-cbc -d -in text.enc -out text.fakeiv.txt -K $(cat aes.key) -iv $(cat aes.fakeiv)
`

We obtain the partially decrypted plaintext, with a length of 1810 bytes.
```
00000000: ab77 474e 9358 dbbc 5215 a889 373e 4bea  .wGN.X..R...7>K.
00000010: 7669 6765 720a 0a46 726f 6d20 5769 6b69  viger..From Wiki
00000020: 7065 6469 612c 2074 6865 2066 7265 6520  pedia, the free 
00000030: 656e 6379 636c 6f70 6564 6961 0a0a 5472  encyclopedia..Tr
00000040: 6963 686f 6765 6e65 7320 636c 6176 6967  ichogenes clavig
00000050: 6572 2c20 7468 6520 4361 6574 c3a9 7320  er, the Caet..s 
00000060: 6361 7466 6973 682c 2069 7320 6120 6372  catfish, is a cr
00000070: 6974 6963 616c 6c79 2065 6e64 616e 6765  itically endange
00000080: 7265 6420 7370 6563 6965 7320 6f66 2066  red species of f
00000090: 7265 7368 7761 7465 7220 7261 792d 6669  reshwater ray-fi
000000a0: 6e6e 6564 2066 6973 6820 6265 6c6f 6e67  nned fish belong
000000b0: 696e 6720 746f 2074 6865 2066 616d 696c  ing to the famil
000000c0: 7920 5472 6963 686f 6d79 6374 6572 6964  y Trichomycterid
000000d0: 6165 2c20 7468 6520 7065 6e63 696c 2061  ae, the pencil a
000000e0: 6e64 2070 6172 6173 6974 6963 2063 6174  nd parasitic cat
000000f0: 6669 7368 6573 2e20 5468 6973 2073 7065  fishes. This spe
00000100: 6369 6573 2069 7320 656e 6465 6d69 6320  cies is endemic 
00000110: 746f 2073 7472 6561 6d73 2069 6e20 7468  to streams in th
00000120: 6520 4174 6c61 6e74 6963 2046 6f72 6573  e Atlantic Fores
00000130: 7420 6f66 2042 7261 7a69 6c2e 2049 7420  t of Brazil. It 
00000140: 7761 7320 6469 7363 6f76 6572 6564 2065  was discovered e
00000150: 6172 6c79 2069 6e20 3230 3130 2061 6e64  arly in 2010 and
00000160: 2073 6369 656e 7469 6669 6361 6c6c 7920   scientifically 
00000170: 6465 7363 7269 6265 6420 6c61 7465 7220  described later 
00000180: 7468 6174 2079 6561 722e 204f 6e65 206f  that year. One o
00000190: 6620 7468 7265 6520 7370 6563 6965 7320  f three species 
000001a0: 7769 7468 696e 2074 6865 2067 656e 7573  within the genus
000001b0: 2054 7269 6368 6f67 656e 6573 2c20 6974   Trichogenes, it
000001c0: 2069 7320 7265 7374 7269 6374 6564 2074   is restricted t
000001d0: 6f20 616e 2061 7265 6120 6f66 2031 3620  o an area of 16 
000001e0: 6b6d 3220 2836 2e32 2073 7120 6d69 2920  km2 (6.2 sq mi) 
000001f0: 696e 2074 6865 2043 6165 74c3 a973 2066  in the Caet..s f
00000200: 6f72 6573 742c 2061 206d 6f75 6e74 6169  orest, a mountai
00000210: 6e6f 7573 2061 7265 6120 696e 2074 6865  nous area in the
00000220: 2042 7261 7a69 6c69 616e 2073 7461 7465   Brazilian state
00000230: 206f 6620 4573 70c3 ad72 6974 6f20 5361   of Esp..rito Sa
00000240: 6e74 6f2e 2057 6865 6e20 6469 7363 6f76  nto. When discov
00000250: 6572 6564 2c20 7468 6520 7261 696e 666f  ered, the rainfo
00000260: 7265 7374 2069 6e20 7768 6963 6820 6974  rest in which it
00000270: 206f 6363 7572 7320 7761 7320 756e 7072   occurs was unpr
00000280: 6f74 6563 7465 6420 616e 6420 7468 7265  otected and thre
00000290: 6174 656e 6564 2062 7920 6465 666f 7265  atened by defore
000002a0: 7374 6174 696f 6e2e 2041 2070 7269 7661  station. A priva
000002b0: 7465 206e 6174 7572 6520 7265 7365 7276  te nature reserv
000002c0: 6520 6861 7320 7369 6e63 6520 6265 656e  e has since been
000002d0: 2065 7374 6162 6c69 7368 6564 2c20 616c   established, al
000002e0: 6c6f 7769 6e67 2076 6973 6974 6f72 7320  lowing visitors 
000002f0: 746f 2073 6565 2074 6865 2066 6973 6820  to see the fish 
00000300: 696e 2069 7473 2068 6162 6974 6174 2e0a  in its habitat..
00000310: 0a41 2073 6d61 6c6c 2066 6973 682c 2054  .A small fish, T
00000320: 2e20 636c 6176 6967 6572 2069 7320 7570  . claviger is up
00000330: 2074 6f20 3530 2e38 206d 6d20 2832 2e30   to 50.8 mm (2.0
00000340: 3020 696e 2920 696e 206c 656e 6774 682e  0 in) in length.
00000350: 2041 2073 6572 6965 7320 6f66 2062 6c61   A series of bla
00000360: 636b 2064 6f74 7320 7275 6e73 2061 6c6f  ck dots runs alo
00000370: 6e67 2074 6865 2073 6964 6520 6f66 2074  ng the side of t
00000380: 6865 2062 6f64 792c 2064 6973 7469 6e67  he body, disting
00000390: 7569 7368 696e 6720 6974 2066 726f 6d20  uishing it from 
000003a0: 7265 6c61 7465 6420 7370 6563 6965 732e  related species.
000003b0: 204d 616c 6573 2068 6176 6520 6120 626f   Males have a bo
000003c0: 6e79 2070 726f 7472 7573 696f 6e20 6672  ny protrusion fr
000003d0: 6f6d 2074 6865 2067 696c 6c20 6172 6561  om the gill area
000003e0: 2028 7468 6520 6f70 6572 6375 6c61 7220   (the opercular 
000003f0: 7072 6f63 6573 7329 2074 6861 7420 6973  process) that is
00000400: 2065 6c6f 6e67 6174 6564 2061 6e64 2063   elongated and c
00000410: 6c75 622d 6c69 6b65 2c20 6120 6665 6174  lub-like, a feat
00000420: 7572 6520 7468 6174 2069 6e73 7069 7265  ure that inspire
00000430: 6420 7468 6520 6e61 6d65 206f 6620 7468  d the name of th
00000440: 6520 7370 6563 6965 7320 2863 6c61 7669  e species (clavi
00000450: 6765 7220 e280 9320 2763 6c75 622d 6265  ger ... 'club-be
00000460: 6172 696e 6727 292e 2054 6865 206f 7065  aring'). The ope
00000470: 7263 756c 6172 2070 726f 6365 7373 2069  rcular process i
00000480: 6e20 542e 2063 6c61 7669 6765 7220 6973  n T. claviger is
00000490: 2074 6865 206f 6e6c 7920 6b6e 6f77 6e20   the only known 
000004a0: 7365 636f 6e64 6172 7920 7365 7820 6368  secondary sex ch
000004b0: 6172 6163 7465 7269 7374 6963 2069 6e20  aracteristic in 
000004c0: 7065 6e63 696c 2063 6174 6669 7368 6573  pencil catfishes
000004d0: 2c20 616e 6420 6d69 6768 7420 6861 7665  , and might have
000004e0: 2065 766f 6c76 6564 2066 6f72 2073 6578   evolved for sex
000004f0: 7561 6c20 7369 676e 616c 696e 673b 2069  ual signaling; i
00000500: 7420 6973 2061 6c73 6f20 7573 6564 2062  t is also used b
00000510: 7920 7468 6520 6669 7368 2074 6f20 636c  y the fish to cl
00000520: 696d 6220 7570 206e 6574 2077 616c 6c73  imb up net walls
00000530: 2077 6865 6e20 6361 7567 6874 2e20 5468   when caught. Th
00000540: 6520 6d6f 7574 6820 6973 2074 6572 6d69  e mouth is termi
00000550: 6e61 6c20 2866 6163 6573 2066 6f72 7761  nal (faces forwa
00000560: 7264 7320 7261 7468 6572 2074 6861 6e20  rds rather than 
00000570: 6265 696e 6720 7570 7475 726e 6564 206f  being upturned o
00000580: 7220 646f 776e 7475 726e 6564 293b 2074  r downturned); t
00000590: 6869 7320 6665 6174 7572 6520 6973 2061  his feature is a
000005a0: 6c73 6f20 666f 756e 6420 696e 2069 7473  lso found in its
000005b0: 2063 6c6f 7365 7374 2072 656c 6174 6976   closest relativ
000005c0: 652c 2054 2e20 6265 6167 6c65 2c20 6275  e, T. beagle, bu
000005d0: 7420 6162 7365 6e74 2069 6e20 616c 6c20  t absent in all 
000005e0: 6f74 6865 7220 6d65 6d62 6572 7320 6f66  other members of
000005f0: 2074 6865 6972 2066 616d 696c 792e 2049   their family. I
00000600: 7420 6973 206b 6e6f 776e 2074 6f20 6775  t is known to gu
00000610: 6c70 2061 6972 2066 726f 6d20 7468 6520  lp air from the 
00000620: 7761 7465 7220 7375 7266 6163 653b 2077  water surface; w
00000630: 6865 6e20 6361 7272 7969 6e67 2061 6972  hen carrying air
00000640: 2c20 7468 6520 626f 6479 2074 696c 7473  , the body tilts
00000650: 2064 6f77 6e77 6172 642e 2054 6865 2073   downward. The s
00000660: 7065 6369 6573 206c 6976 6573 2069 6e20  pecies lives in 
00000670: 736d 616c 6c2c 2073 6861 6465 642c 2061  small, shaded, a
00000680: 6e64 2073 6c6f 772d 6d6f 7669 6e67 2073  nd slow-moving s
00000690: 7472 6561 6d73 2069 6e20 7468 6520 7261  treams in the ra
000006a0: 696e 666f 7265 7374 2c20 616e 6420 6d6f  inforest, and mo
000006b0: 7374 6c79 2066 6565 6473 206f 6e20 696e  stly feeds on in
000006c0: 7365 6374 7320 7468 6174 2068 6176 6520  sects that have 
000006d0: 6661 6c6c 656e 206f 6e20 7468 6520 7761  fallen on the wa
000006e0: 7465 7220 7375 7266 6163 652e 2049 7420  ter surface. It 
000006f0: 6973 2074 6865 206f 6e6c 7920 6669 7368  is the only fish
00000700: 2069 6e20 6974 7320 6861 6269 7461 742e   in its habitat.
00000710: 200a 
```

By comparing text.txt and text.fakeiv.txt using vbindiff, we find that, except for the first 16 bytes, the rest of the data is decrypted successfully.

`vbindiff text.txt text.fakeiv.txt`

```
text.txt                                                                        
00000 0000:  0A 54 72 69 63 68 6F 67  65 6E 65 73 20 63 6C 61  .Trichog enes cla
00000 0010:  76 69 67 65 72 0A 0A 46  72 6F 6D 20 57 69 6B 69  viger..F rom Wiki
text.fakeiv.txt                                                                 
00000 0000:  AB 77 47 4E 93 58 DB BC  52 15 A8 89 37 3E 4B EA  .wGN.X.. R...7>K.
00000 0010:  76 69 67 65 72 0A 0A 46  72 6F 6D 20 57 69 6B 69  viger..F rom Wiki
```

But why? If the IV is set incorrectly, shouldn't the entire decryption fail? Actually, no—this phenomenon is related to the design of CBC mode.

The principle of CBC mode is as shown below:

![CBC Mode](/assets/images/exploration-of-cbc-mode/cbc.png)

We can clearly conclude from the above diagram that, during the decryption process, the initialization vector is not used from the second block to the last block—only the first block utilizes it. Therefore, as long as the key is correct, an incorrect initialization vector will only affect the decryption of the first block.

Does the initialization vector need to be kept secret? The answer is: **No**. IV is a crucial concept in cryptography, especially when using block cipher modes of operation. It is a random or unique value used in combination with a secret key to initialize the encryption process. It ensures that the same plaintext encrypted multiple times will produce different ciphertexts, enhancing security by preventing patterns from emerging. IV is a **non-secret**, unique value used to ensure that encrypting the same plaintext with the same key results in different ciphertexts.






