# protoc-decode-lenprefix
Wrapper around [protobuf](https://github.com/google/protobuf)'s `protoc --decode` utility.  Adds support for [length-prefixed](http://eli.thegreenplace.net/2011/08/02/length-prefix-framing-for-protocol-buffers) messages.

## Usage

```
cat PROTOBUF_MESSAGE_FILE | \
  protoc-decode-lenprefix -d mesos.internal.StatusUpdateRecord \
    -I MESOS_CODE/src -I MESOS_CODE/include \
    PROTO_FILE \
```

e.g., say you have a [`task.updates` file](https://github.com/apache/mesos/blob/0.27.0/src/slave/paths.hpp#L83) generated by [Mesos](http://github.com/apache/mesos),
and say you have downloaded the Mesos source code to the `MESOS_CODE` dir.

```
cat task.updates | \
  protoc-decode-lenprefix \
    --decode mesos.internal.StatusUpdateRecord \
    -I MESOS_CODE/src -I MESOS_CODE/include \
    MESOS_CODE/src/messages/messages.proto
```

This results in all of the StatusUpdateRecord messages in the `task.updates` input file being written to stdout.

Note how the parameters for `protoc-decode-lenprefix` are identical to those passed to `protoc`,
assuming you *don't* have the length-prefix header.
e.g., taking a [`task.info` file](https://github.com/apache/mesos/blob/0.27.0/src/slave/paths.hpp#L82), we use `dd` to strip off the 1st 4 bytes and then have `protoc`
decode the embedded message:

```
dd bs=1 skip=4 if=task.info | \
  protoc \
    --decode=mesos.internal.Task \
    -I MESOS_CODE/src -I MESOS_CODE/include \
    MESOS_CODE/src/messages/messages.proto
```
