---
title: Bloblang Functions
sidebar_label: Functions
description: A list of Bloblang functions
---

Functions can be placed anywhere and allow you to extract information from your environment, generate values, or access data from the underlying message being mapped:

```coffee
root.doc.id = uuid_v4()
root.doc.received_at = timestamp_unix()
root.doc.host = hostname()
```

### `batch_index`

Returns the index of the mapped message within a batch. This is useful for applying maps only on certain messages of a batch:

```coffee
# Only map the first message of a batch
root = match {
  batch_index() == 0 => this.apply("foo")
}
```

### `batch_size`

Returns the size of the message batch.

```coffee
root.foo = batch_size()
```

### `content`

Returns the full raw contents of the mapping target message as a byte array. When mapping to a JSON field the value should be encoded using the method [`encode`][methods.encode], or cast to a string directly using the method [`string`][methods.string], otherwise it will be base64 encoded by default.

```coffee
root.doc = content().string()

# In:  {"foo":"bar"}
# Out: {"doc":"{\"foo\":\"bar\"}"}
```

### `count`

The `count` function is a counter starting at 1 which increments after each time it is called. Count takes an argument which is an identifier for the counter, allowing you to specify multiple unique counters in your configuration.

```coffee
root.doc.id = count("documents")
```

### `deleted`

This is a special function indicating that the mapping target should be deleted. For example, it can be used to remove elements of an array within `for_each`:

```coffee
root.new_nums = this.nums.for_each(
  match this {
    this < 10 => deleted()
    _ => this - 10
  }
)

# in:  {"nums":[3,11,4,17]}
# out: {"new_nums":[1,7]}
```

### `error`

If an error has occurred during the processing of a message this function returns the reported cause of the error. For more information about error handling patterns read [here][error_handling].

```coffee
root.doc.error = error()
```

### `errored`

Returns a boolean value indicating whether an error has occurred during the processing of a message. For more information about error handling patterns read [here][error_handling].

```coffee
root.doc.status = if errored() { 400 } else { 200 }
```

### `hostname`

Resolves to the hostname of the machine running Benthos.

```coffee
root.thing.host = hostname()
```

### `json`

Returns the value of a field within a JSON message located by a [dot path][field_paths] argument. This function always targets the entire source JSON document regardless of the mapping context.

```coffee
root.mapped = json("foo.bar")

# In:  {"foo":{"bar":"hello world"}}
# Out: {"mapped":"hello world"}
```

The path argument is optional and if omitted the entire JSON payload is returned.

### `meta`

Returns the value of a metadata key from a message identified by a key. Values are extracted from the referenced input message and therefore do NOT reflect changes made from within the map.

The parameter is optional and if omitted the entire metadata contents are returned as a JSON object.

```coffee
root.topic = meta("kafka_topic")
```

### `random_int`

Generates a non-negative pseudo-random 64-bit integer. An optional integer argument can be provided in order to seed the random number generator.

```coffee
root.first = random_int()
root.second = random_int(1)
```

### `timestamp`

Prints the current time in a custom format specified by the argument. The format is defined by showing how the reference time, defined to be
`Mon Jan 2 15:04:05 -0700 MST 2006` would be displayed if it were the value.

A fractional second is represented by adding a period and zeros to the end of the seconds section of layout string, as in `15:04:05.000` to format a time stamp with millisecond precision.

```coffee
root.received_at = timestamp("15:04:05")
```

### `timestamp_unix`

Resolves to the current unix timestamp in seconds.

```coffee
root.received_at = timestamp_unix()
```

### `timestamp_unix_nano`

Resolves to the current unix timestamp in nanoseconds.

```coffee
root.received_at = timestamp_unix_nano()
```

### `timestamp_utc`

The equivalent of `timestamp` except the time is printed as UTC instead of the local timezone.

```coffee
root.received_at = timestamp_utc("15:04:05")
```

### `throw`

Returns an error similar to a regular mapping error. This is useful for abandoning a mapping entirely given certain conditions.

```coffee
root.doc.type = match {
  this.exists("header.id") => "foo"
  this.exists("body.data") => "bar"
  _ => throw("unknown type")
}
root.doc.contents = (this.body.content | this.thing.body)

# In:  {"header":{"id":"first"},"thing":{"body":"hello world"}}
# Out: {"doc":{"contents":"hello world","type":"foo"}}

# In:  {"nothing":"matches"}
# Out: Error("unknown type")
```

### `uuid_v4`

Generates a new RFC-4122 UUID each time it is invoked and prints a string representation.

```coffee
root.id = uuid_v4()
```

[error_handling]: /docs/configuration/error_handling
[field_paths]: /docs/configuration/field_paths
[meta_proc]: /docs/components/processors/metadata
[methods.encode]: /docs/guides/bloblang/methods#encode
[methods.string]: /docs/guides/bloblang/methods#string