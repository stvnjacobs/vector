---
delivery_guarantee: "best_effort"
event_types: ["log"]
issues_url: https://github.com/timberio/vector/issues?q=is%3Aopen+is%3Aissue+label%3A%22sink%3A+vector%22
operating_systems: ["linux","macos","windows"]
sidebar_label: "vector|[\"log\"]"
source_url: https://github.com/timberio/vector/tree/master/src/sinks/vector.rs
status: "prod-ready"
title: "vector sink"
unsupported_operating_systems: []
---

The `vector` sink [streams](#streaming) [`log`][docs.data-model#log] events to another downstream [`vector` source][docs.sources.vector].

## Configuration

import Tabs from '@theme/Tabs';

<Tabs
  block={true}
  defaultValue="common"
  values={[
    { label: 'Common', value: 'common', },
    { label: 'Advanced', value: 'advanced', },
  ]
}>

import TabItem from '@theme/TabItem';

<TabItem value="common">

import CodeHeader from '@site/src/components/CodeHeader';

<CodeHeader fileName="vector.toml" learnMoreUrl="/docs/setup/configuration"/ >

```toml
[sinks.my_sink_id]
  type = "vector" # example, must be: "vector"
  inputs = ["my-source-id"] # example
  address = "92.12.333.224:5000" # example
```

</TabItem>
<TabItem value="advanced">

<CodeHeader fileName="vector.toml" learnMoreUrl="/docs/setup/configuration" />

```toml
[sinks.my_sink_id]
  # REQUIRED - General
  type = "vector" # example, must be: "vector"
  inputs = ["my-source-id"] # example
  address = "92.12.333.224:5000" # example
  
  # OPTIONAL - General
  healthcheck = true # default
  
  # OPTIONAL - Buffer
  [sinks.my_sink_id.buffer]
    type = "memory" # default, enum
    max_size = 104900000 # example, no default, bytes, relevant when type = "disk"
    num_items = 500 # default, events, relevant when type = "memory"
    when_full = "block" # default, enum
```

</TabItem>

</Tabs>

## Options

import Fields from '@site/src/components/Fields';

import Field from '@site/src/components/Field';

<Fields filters={true}>


<Field
  common={true}
  defaultValue={null}
  enumValues={null}
  examples={["92.12.333.224:5000"]}
  name={"address"}
  nullable={false}
  path={null}
  relevantWhen={null}
  required={true}
  templateable={false}
  type={"string"}
  unit={null}
  >

### address

The downstream Vector address.


</Field>


<Field
  common={false}
  defaultValue={null}
  enumValues={null}
  examples={[]}
  name={"buffer"}
  nullable={true}
  path={null}
  relevantWhen={null}
  required={false}
  templateable={false}
  type={"table"}
  unit={null}
  >

### buffer

Configures the sink specific buffer.

<Fields filters={false}>


<Field
  common={false}
  defaultValue={"memory"}
  enumValues={{"memory":"Stores the sink's buffer in memory. This is more performant (~3x), but less durable. Data will be lost if Vector is restarted abruptly.","disk":"Stores the sink's buffer on disk. This is less performance (~3x),  but durable. Data will not be lost between restarts."}}
  examples={["memory","disk"]}
  name={"type"}
  nullable={false}
  path={"buffer"}
  relevantWhen={null}
  required={false}
  templateable={false}
  type={"string"}
  unit={null}
  >

#### type

The buffer's type / location. `disk` buffers are persistent and will be retained between restarts.


</Field>


<Field
  common={false}
  defaultValue={"block"}
  enumValues={{"block":"Applies back pressure when the buffer is full. This prevents data loss, but will cause data to pile up on the edge.","drop_newest":"Drops new data as it's received. This data is lost. This should be used when performance is the highest priority."}}
  examples={["block","drop_newest"]}
  name={"when_full"}
  nullable={false}
  path={"buffer"}
  relevantWhen={null}
  required={false}
  templateable={false}
  type={"string"}
  unit={null}
  >

#### when_full

The behavior when the buffer becomes full.


</Field>


<Field
  common={false}
  defaultValue={null}
  enumValues={null}
  examples={[104900000]}
  name={"max_size"}
  nullable={true}
  path={"buffer"}
  relevantWhen={{"type":"disk"}}
  required={false}
  templateable={false}
  type={"int"}
  unit={"bytes"}
  >

#### max_size

The maximum size of the buffer on the disk.


</Field>


<Field
  common={false}
  defaultValue={500}
  enumValues={null}
  examples={[500]}
  name={"num_items"}
  nullable={true}
  path={"buffer"}
  relevantWhen={{"type":"memory"}}
  required={false}
  templateable={false}
  type={"int"}
  unit={"events"}
  >

#### num_items

The maximum number of [events][docs.data-model#event] allowed in the buffer.


</Field>


</Fields>

</Field>


<Field
  common={false}
  defaultValue={true}
  enumValues={null}
  examples={[true,false]}
  name={"healthcheck"}
  nullable={false}
  path={null}
  relevantWhen={null}
  required={false}
  templateable={false}
  type={"bool"}
  unit={null}
  >

### healthcheck

Enables/disables the sink healthcheck upon start. See [Health Checks](#health-checks) for more info.


</Field>


</Fields>

## Output

The `vector` sink [streams](#streaming) [`log`][docs.data-model#log] events to another downstream [`vector` source][docs.sources.vector].

## How It Works

### Environment Variables

Environment variables are supported through all of Vector's configuration.
Simply add `${MY_ENV_VAR}` in your Vector configuration file and the variable
will be replaced before being evaluated.

You can learn more in the [Environment Variables][docs.configuration#environment-variables]
section.

### Health Checks

Health checks ensure that the downstream service is accessible and ready to
accept data. This check is performed upon sink initialization.
If the health check fails an error will be logged and Vector will proceed to
start.

#### Require Health Checks

If you'd like to exit immediately upon a health check failure, you can
pass the `--require-healthy` flag:

```bash
vector --config /etc/vector/vector.toml --require-healthy
```

#### Disable Health Checks

If you'd like to disable health checks for this sink you can set the[`healthcheck`](#healthcheck) option to `false`.

### Streaming

The `vector` sink streams data on a real-time
event-by-event basis. It does not batch data.


[docs.configuration#environment-variables]: /docs/setup/configuration#environment-variables
[docs.data-model#event]: /docs/about/data-model#event
[docs.data-model#log]: /docs/about/data-model#log
[docs.sources.vector]: /docs/reference/sources/vector