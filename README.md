jsbuf
=====

Provides a simple JavaScript library for encoding and decoding messages
using [Google's Protocol Buffers][protobuf].

## Decoding

Incoming messages are expected to be binary strings. In terms of JavaScript
strings I mean that each character accessible by `charCodeAt` is a single byte
of binary data. Given a binary string, the decoding process will return a
JavaScript array with each element representing a field in the message. Each
element will contain the field number, type, and value. The array of field data
is *not* guaranteed to be sorted.

    // Assume a message with two fields, both varint types.
    var msg = Protobuf.decode(bstr);
    msg[0] == [1, 0, 150];
    msg[1] == [2, 0, 96];

The format for each field element is `[FieldNum, WireType, Value]`. So in the
example the resulting message data happens to have the first element by the
first field, with a varint wire type, and a value of 150. The second element is
the second field, also with a varint wire type, and a value of 96.

Using the `jsbuf` generator you can create a helper script that will decode
message data to JavaScript objects where fields can be accessed by their name or
field number.

## Encoding

Encode an array of field data to a binary string that can be sent over the wire
using as a protocol buffer message. Field data must look like `[FieldNum,
WireType, Value]`.

    var fields = [ [1, 0, 150], [2, 0, 96] ];
    Protobuf.encode(fields);

The field data does *not* need to be sorted by field number prior to encoding.
The protocol buffer format does not require it, since part of each field's
"header" data you must include the field number.

Using the `jsbuf` generator you can create a helper script that will encode
JavaScript objects to binary strings based on your `*.proto` files. This means
you can use named attributes to set data and the underlying helper class will
make sure each field has the appropriate number and wire type set when
generating the field data array to encode.

## Generator

The sweet spot of this project is the helper class generator. Using your
`*.proto` files you can create a helper class for each message type defined.
These classes ensure only attributes defined in the proto file can be set, as
well as giving the developer accessed to named fields. Finally, these helper
classes will have two other convenience functions: easy encoding and decoding.

[protobuf]: https://developers.google.com/protocol-buffers/
