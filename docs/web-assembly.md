# ⚙️ WebAssembly

## Bindings

WebAssembly modules are bound as follows. The bound keys will be instances of
[WebAssembly.Module](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Module):

```shell
$ miniflare --wasm MODULE1=module1.wasm --wasm MODULE2=module2.wasm
```

```toml
# wrangler.toml
[miniflare]
wasm_bindings = [
  { name = "MODULE1", path="module1.wasm" },
  { name = "MODULE2", path="module2.wasm" }
]
```

```js
const mf = new Miniflare({
  wasmBindings: {
    MODULE1: "module1.wasm",
    MODULE2: "module2.wasm",
  },
});
```

You can then use the WebAssembly modules in your workers:

```js{1,5}
const instance = new WebAssembly.Instance(MODULE1);

addEventListener("fetch", (e) => {
  // Assuming MODULE1 exports a function `add` summing 2 integer arguments
  const value = instance.exports.add(1, 2);
  e.respondWith(new Response(value.toString()));
});
```

## Modules

You can also import WebAssembly modules by adding a `CompiledWasm` module rule.
See [📚 Modules](/modules.html) for more details. For instance, with the
following `wrangler.toml` file and worker script, we can achieve the same result
as the previous example:

```toml
# wrangler.toml
[[build.upload.rules]]
type = "CompiledWasm"
globs = ["**/*.wasm"]
```

```js{1-3,7}
import module1 from "./module1.wasm";

const instance = new WebAssembly.Instance(module1);

export default {
  fetch() {
    const value = instance.exports.add(1, 2);
    return new Response(value.toString());
  },
};
```

## Rust Wrangler Builds

When using [Rust Wrangler Builds](/builds.html#rust), `wasm` is automatically
bound to your compiled WebAssembly module.
