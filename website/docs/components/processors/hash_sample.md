---
title: hash_sample
type: processor
deprecated: true
---

<!--
     THIS FILE IS AUTOGENERATED!

     To make changes please edit the contents of:
     lib/processor/hash_sample.go
-->

DEPRECATED: This component is deprecated and will be removed in the next major
version release. Please consider moving onto [alternative components](#alternatives).


import Tabs from '@theme/Tabs';

<Tabs defaultValue="common" values={[
  { label: 'Common', value: 'common', },
  { label: 'Advanced', value: 'advanced', },
]}>

import TabItem from '@theme/TabItem';

<TabItem value="common">

```yaml
# Common config fields, showing default values
hash_sample:
  retain_min: 0
  retain_max: 10
```

</TabItem>
<TabItem value="advanced">

```yaml
# All config fields, showing default values
hash_sample:
  retain_min: 0
  retain_max: 10
  parts:
    - 0
```

</TabItem>
</Tabs>

## Fields

### `retain_min`

The lower percentage of the sample range.


Type: `number`  
Default: `0`  

### `retain_max`

The upper percentage of the sample range.


Type: `number`  
Default: `10`  

### `parts`

An array of message indexes within the batch to sample based on. If left empty all messages included. This field is only applicable when batching messages [at the input level](/docs/configuration/batching).


Type: `array`  
Default: `[0]`  

## Alternatives

All functionality of this processor has been superseded by the
[bloblang](/docs/components/processors/bloblang) processor.

