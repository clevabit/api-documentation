# CBOR

All data returned from the authenticated API calls is encoded in the space efficient
binary data format [CBOR](https://cbor.io ':target=_blank'), except stated otherwise.

CBOR is a standard for binary encoding, specified through the IETF (Internet Engineering
Task Force) and available as RFC 7049. The object format of CBOR objects mostly resembles
those of JSON objects, supporting objects, lists, dictionaries and properties. The main
reason for using CBOR is the binary representation, variable length encoding of numeric
values, as well as the easiness of encoders and decoders.

The only exception is the [Sandbox](02_Environments.md#sandbox) environment, which can be asked to return
JSON objects instead.

CBOR encoders and decoders are available for many programming languages and runtime
environments. An extensive but not complete list is available from the 
[CBOR website](https://cbor.io/impls.html ':target=_blank').
