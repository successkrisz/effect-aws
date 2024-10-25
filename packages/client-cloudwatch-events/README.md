# @effect-aws/client-cloudwatch-events

[![npm version](https://img.shields.io/npm/v/%40effect-aws%2Fclient-cloudwatch-events?color=brightgreen&label=npm%20package)](https://www.npmjs.com/package/@effect-aws/client-cloudwatch-events)
[![npm downloads](https://img.shields.io/npm/dm/%40effect-aws%2Fclient-cloudwatch-events)](https://www.npmjs.com/package/@effect-aws/client-cloudwatch-events)

## Installation

```bash
npm install --save @effect-aws/client-cloudwatch-events
```

## Usage

With default CloudWatchEventsClient instance:

```typescript
import { CloudWatchEventsService, DefaultCloudWatchEventsServiceLayer } from "@effect-aws/client-cloudwatch-events";

const program = CloudWatchEventsService.listRules(args);

const result = pipe(
  program,
  Effect.provide(DefaultCloudWatchEventsServiceLayer),
  Effect.runPromise,
);
```

With custom CloudWatchEventsClient instance:

```typescript
import {
  CloudWatchEventsService,
  BaseCloudWatchEventsServiceLayer,
  CloudWatchEventsClientInstance,
} from "@effect-aws/client-cloudwatch-events";

const program = CloudWatchEventsService.listRules(args);

const CloudWatchEventsClientInstanceLayer = Layer.succeed(
  CloudWatchEventsClientInstance,
  new CloudWatchEventsClient({ region: "eu-central-1" }),
);

const result = await pipe(
  program,
  Effect.provide(BaseCloudWatchEventsServiceLayer),
  Effect.provide(CloudWatchEventsClientInstanceLayer),
  Effect.runPromise,
);
```

With custom CloudWatchEventsClient configuration:

```typescript
import {
  CloudWatchEventsService,
  BaseCloudWatchEventsServiceLayer,
  DefaultCloudWatchEventsClientConfigLayer,
  CloudWatchEventsClientInstance,
  CloudWatchEventsClientInstanceConfig,
} from "@effect-aws/client-cloudwatch-events";

const program = CloudWatchEventsService.listRules(args);

const CloudWatchEventsClientInstanceLayer = Layer.provide(
  Layer.effect(
    CloudWatchEventsClientInstance,
    CloudWatchEventsClientInstanceConfig.pipe(
      Effect.map(
        (config) => new CloudWatchEventsClient({ ...config, region: "eu-central-1" }),
      ),
    ),
  ),
  DefaultCloudWatchEventsClientConfigLayer,
);

const result = await pipe(
  program,
  Effect.provide(BaseCloudWatchEventsServiceLayer),
  Effect.provide(CloudWatchEventsClientInstanceLayer),
  Effect.runPromiseExit,
);
```

or map over `DefaultCloudWatchEventsClientConfigLayer` layer context and update the configuration...