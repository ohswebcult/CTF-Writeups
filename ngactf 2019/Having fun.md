The file is uuencoded 
```shell
cat message.coded | tr 'A-Za-z' 'N-ZA-Mn-za-m' | uudecode
```

The output looks like a simple substitution cipher.
```
Ilobj fmprj alilo pfq xjbq, zlkpbzqbqro xafmfpzfkd bifq. Fkqbdbo fxzrifp, lozf rq xrzqlo yixkafq, bolp qromfp mixzboxq cbifp, bq zlkdrb ibzqrp xrdrb sfqxb jxdkx. Jxbzbkxp fmprj mrorp, mbiibkqbpnrb fk arf kbz, sbkbkxqfp xzzrjpxk xkqb. Jxrofp riqofzfbp riqofzfbp ilobj nrfp zlkpbzqbqro. Xii qefkdp ybfkd bnrxi, cfkafkd xii qebpb cixdp, lkb zxk qoriv pxv qbjmrp crdfq. Rq arf kbnrb, srimrqxqb rq plaxibp klk, orqorj fa cbifp. Arfp xz ixzfkfx pxmfbk. Rq qfkzfarkq, mrorp x mexobqox irzqrp, bkfj xrdrb mrisfkxo bolp, x pzbibofpnrb ifdrix arf fk ibl. Pba klk cbojbkqrj krkz, sfqxb sbefzrix lafl. Bqfxj mlprbob qloqlo sfqxb bibjbkqrj zlkpbnrxq. Alkbz bibfcbka pfq xjbq boxq x ilyloqfp.
```

Decode using this tool: https://quipqiup.com/

After decoding, one line will read `All things being equal, finding all these flags, one can truly say tempus fugit`\
The flag is **tempus fugit**
