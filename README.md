# Issue with bitski-provider

```bash
pnpm i
pnpm dev
# then navigate to localhost:5173
```

```
app.js:16 TypeError: Class extends value undefined is not a constructor or null
    at node_modules/.pnpm/@metamask+safe-event-emitter@2.0.0/node_modules/@metamask/safe-event-emitter/index.js (index.ts:28:47)
    at __require (chunk-WXXH56N5.js?v=0e76569a:12:50)
    at bitski-provider.js:1:30
```
