# zte config utility

The core of the decoding work is taken from a [pastebin](https://pastebin.com/GGxbngtK) dump by 'Felis-Sapien'.

Creates byte-perfect binaries for the limited number of `config.bin` that have been tested.

## Quickstart

Clone the repo and run `python3 setup.py install --user` to install the `zcu` module.
You can then use the scripts in the [resources](./resources) directory.

NOTE: This project has only been tested against **Python 3.5** and higher.

## Examples

### Decode/Encode a type-2, version 2 `config.bin` (if the key is known for the signature given/detected, you can omit it)

```sh
$ python3 examples/decode.py resources/ZXHN_H298N.bin resources/ZXHN_H298N.xml --key 'Wj'
$ python3 examples/encode.py resources/ZXHN_H298N.xml resources/ZXHN_H298N.NEW.bin --key 'Wj' --signature 'ZXHN H298N' --include-header
$ md5sum resources/ZXHN_H298N.bin resources/ZXHN_H298N.NEW.bin
8529c1e3d4e3018db508a3b5b5b574cc  resources/ZXHN_H298N.bin
8529c1e3d4e3018db508a3b5b5b574cc  resources/ZXHN_H298N.NEW.bin
```

### Decode/Encode a type-2, version 1 `config.bin`

```sh
$ python3 examples/decode.py resources/ZXHN_H108N_V2.5.bin resources/ZXHN_H108N_V2.5.xml --key 'GrWM2Hz&LTvz&f^5'
$ python3 examples/encode.py resources/ZXHN_H108N_V2.5.xml resources/ZXHN_H108N_V2.5.NEW.bin --key 'GrWM2Hz&LTvz&f^5' --signature 'ZXHN H108N V2.5' --version 1 --include-header
$ md5sum resources/ZXHN_H108N_V2.5.bin resources/ZXHN_H108N_V2.5.NEW.bin
5dbb537bb8a5bfa51f9bc9e2d48f576d  resources/ZXHN_H108N_V2.5.bin
5dbb537bb8a5bfa51f9bc9e2d48f576d  resources/ZXHN_H108N_V2.5.NEW.bin
```

### Decode/Encode a type-0 `config.bin`

```sh
$ python3 examples/decode.py resources/F600W.bin resources/F600W.xml
$ python3 examples/encode.py resources/F600W.xml resources/F600W.NEW.bin --signature F600W --payload-type 0
$ md5sum resources/F600W.bin resources/F600W.NEW.bin
a6ac0e5e04f705b54747c30f80dfd4ba  resources/F600W.bin
a6ac0e5e04f705b54747c30f80dfd4ba  resources/F600W.NEW.bin
```

### Decode/Encode a type-3 `db_default_auto_cfg.xml` from a H298Q

```sh
$ python3 examples/decode.py --model "H298Q" resources/ZXHN_H298Q_C7_db_type3.bin resources/ZXHN_H298Q_C7_db.xml
$ python3 examples/encode.py --model "H298Q" resources/ZXHN_H298Q_C7_db.xml resources/ZXHN_H298Q_C7_db_type3.NEW.bin
$ md5sum resources/ZXHN_H298Q_C7_db_type3.bin resources/ZXHN_H298Q_C7_db_type3.NEW.bin
2b76e781a8a91136539e0f2534ac030b  resources/ZXHN_H298Q_C7_db_type3.bin
2b76e781a8a91136539e0f2534ac030b  resources/ZXHN_H298Q_C7_db_type3.NEW.bin
```

### Decode/Encode `config.bin` from a DigiMobil ZXHN H298A router

You can find the serial number in the web interface of the router, in the "Management & Diagnosis" tab.

```sh
$ python3 examples/decode.py --serial ZTEXXXXXXXXXXXX config.bin config.xml
$ python3 examples/encode.py --serial ZTEXXXXXXXXXXXX --signature 'ZXHN H298A V1.0' config.xml config.bin
```

### Decode/Encode `config.bin` from a ZXHN H168N V3.5 router

Some routers (Type 4), might use the signature to create the encryption key.
When decoding, ZCU will use the signature it finds automatically (without spaces), but you can specify one by passing the `--signature` argument to the `decode.py` script.
When re-encoding, you need to specify `--use-signature-encryption` if you want signature encryption to be used.

```sh
$ python3 examples/decode.py ./config.bin ./config.xml
$ python3 examples/encode.py --signature 'ZXHN H168N V3.5' --use-signature-encryption config.xml config.bin
```

### Grab 'signature' from a `config.bin`

```sh
$ python3 examples/signature.py resources/ZXHN_H108N_V2.5.bin
ZXHN H108N V2.5
```

### Auto-decode

If your router's signature is associated with a key known to this utility, you can omit the `--key` parameter when decoding.

```sh
$ python3 examples/decode.py resources/ZXHN_H298N.bin resources/ZXHN_H298N.xml
```

You can also try all the keys known to this utility against your `config.bin` with the `--try-all-known-keys` parameter.
This might be useful if your key is known but your router's signature has not been associated with it.

```sh
$ python3 examples/decode.py resources/ZXHN_H298N.bin resources/ZXHN_H298N.xml --try-all-known-keys
```

## Limitations

The decoder has only been tested against `config.bin` files generated by the following routers:
 - `ZXHN H298A`
 - `ZXHN H298N`
 - `ZXHN H267A`
 - `ZXHN H268Q`
 - `ZXHN H298Q`
 - `ZXHN H168N V2.2`
 - `ZXHN H168N V3.5`
 - `ZXHN H108N V2.5`
 - `F600W`

And `db_default_auto_cfg.xml` files extracted from `ZXHN H268N` and `ZXHN H298Q` firmwares

It makes a number of assumptions due to this. The encoder has not been tested in the wild. Use at your own risk.

## Requirements

The AES encryption relies on [pycryptodomex](https://pypi.org/project/pycryptodomex/).
