# koa-http-proxy
Koa HTTP proxy middleware powered by [http-party/node-http-proxy](https://github.com/http-party/node-http-proxy).

## Installation

```shell
npm install --save @erwinv/koa-http-proxy
```

## Usage

```typescript
import {
  KoaHttpProxy,
  StateWithProxyOpts,
  BufferResponseBody,
} from '@erwinv/koa-http-proxy'

import { default as Koa, Middleware } from 'koa'

new Koa()
  .use((async (ctx, next) => {
    const authMissing = (ctx.get('Authorization')?.trim() ?? '') === ''

    if (authMissing) {
      ctx.state.proxyOpts = {
        headers: { Authorization: `Basic ${process.env.PROXY_AUTH}`},
      }
    }

    return next()
  }) as Middleware<StateWithProxyOpts>)
  .use(KoaHttpProxy({
    target: process.env.PROXY_TARGET,
    xfwd: true,
    proxyTimeout: 60 * 1000,
  }, true))
  .listen(process.env.PORT)
```
