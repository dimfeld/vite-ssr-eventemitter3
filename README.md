# Vite SSR eventemitter3 Transitive Dependency Reproduction

To run

1. pnpm i
2. pnpm dev
3. Open the browser

Note that you can reproduce this with `yarn` as well if you prefer.

It appears that when included as a transitive dependency `eventemitter3` is not prebundled, and since it's a CommonJS package this causes problems. In the reproduction repository case it's being included through the `@solana/wallet-adapter-base` package.

Using yarn we can add eventemitter3 to optimizeDeps.include, but with pnpm it's nested in some other directories, so that doesn't work and I get a "Failed to resolve force included dependency" error. I also tried "@solana/wallet-adapter-base > eventemitter3" but that didn't seem to do anything. 

The eventemitter3 package does some unusual stuff at the end when it exposes the module:

```
if ('undefined' !== typeof module) {
  module.exports = EventEmitter;
}
```

And then when it actually runs, since it hasn't been converted to ESM `module` is undefined and it can't do anything.

