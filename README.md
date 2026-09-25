# scruple-provider-cache

Wraps a Scruple `DecisionProvider` so an identical request is answered from disk instead of the wrapped provider.

## Install

```json
{
  "dependencies": {
    "scruple-provider-cache": "git+https://github.com/jaypeeZero/scruple-provider-cache.git#v0.1.0"
  }
}
```

```ts
import { defineConfig } from '@scruple/core'
import { jevProvider } from '@scruple/provider-jev'
import { cachedProvider } from 'scruple-provider-cache'

export default defineConfig({
  provider: cachedProvider(jevProvider({ apiKey }), {
    enabled: process.env['SCRUPLE_CACHE'] !== 'off'
  }),
  // parser, plugins, rules ...
})
```

## Behaviour

- `cachedProvider(inner, options?)` returns a `DecisionProvider` with the same `id`, `concurrency`, and `close` as `inner`.
- The cache key is the SHA-256 hash of `{ providerId: inner.id, request }`.
- Each key is one file, `<cacheDir>/<key>.json`, holding the `DecisionResponse` as written.
- `options.cacheDir` defaults to `node_modules/.cache/scruple`.
- `options.enabled` defaults to `true`. `false` makes `evaluate` a pass-through to `inner`.
- A missing, unreadable, or malformed cache file is a miss.
- A rejection from `inner.evaluate` propagates unchanged.
- A failure while writing the cache file does not fail the request.

## Development

```
npm install
npm test
npm run typecheck
npm run lint
```
