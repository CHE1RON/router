---
id: useLoaderDataHook
title: `useLoaderData` hook
---


The `useLoaderData` hook returns the loader data from the closest `RouteMatch` in the component tree.

### Options

#### `opts.from`

- Type: `string`
- The route id of the closest parent match
- Optional, but recommended for full type safety.
- If `opts.strict` is `true`, TypeScript will warn for this option if it is not provided.
- If `opts.strict` is `false`, TypeScript will provided loosened types for the returned loader data.

#### `opts.strict`

- Type: `boolean`
- Optional - `default: true`
- If `false`, the `opts.from` option will be ignored and types will be loosened to to reflect the shared types of all possible loader data.

#### `opts.select`

- Optional
- `(loaderData: TLoaderData) => TSelected`
- If supplied, this function will be called with the loader data and the return value will be returned from `useLoaderData`. This value will also be used to determine if the hook should re-render its parent component using shallow equality checks.

### Returns

- If a `select` function is provided, the return value of the `select` function.
- If no `select` function is provided, the loader data or a loosened version of the loader data if `opts.strict` is `false`.
