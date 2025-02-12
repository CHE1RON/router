---
id: RouteOptionsType
title: `RouteOptions` type
---


The `RouteOptions` type is used to describe the options that can be used when creating a route.

### Properties

#### `getParentRoute`

- Type: `() => TParentRoute`
- Required
- A function that returns the parent route of the route being created. This is required to provide full type safety to child route configurations and to ensure that the route tree is built correctly.

#### `path`

- Type: `string`
- Required, unless an `id` is provided to configure the route as a layout route
- The path segment that will be used to match the route.

#### `id`

- Type: `string`
- Optional, but required if a `path` is not provided
- The unique identifier for the route if it is to be configured as a layout route. If provided, the, the route will not match against the location pathname and its routes will be flattened into its parent route for matching.

#### `validateSearch`

- Type: `(rawSearchParams: unknown) => TSearchSchema`
- Optional
- A function that will be called when this route is matched and passed the raw search params from the current location and return valid parsed search params. If this function throws, the route will be put into an error state and the error will be thrown during render. If this function does not throw, its return value will be used as the route's search params and the return type will be inferred into the rest of the router.

#### `parseParams`

- Type: `(rawParams: Record<string, string>) => TParams`
- Optional
- A function that will be called when this route is matched and passed the raw params from the current location and return valid parsed params. If this function throws, the route will be put into an error state and the error will be thrown during render. If this function does not throw, its return value will be used as the route's params and the return type will be inferred into the rest of the router.

#### `stringifyParams`

- Type: `(params: TParams) => Record<string, string>`
- Required if `parseParams` is provided
- A function that will be called when this routes parsed params are being used to build a location. This function should return a valid object of `Record<string, string>` mapping.

#### `beforeLoad`

```tsx
type beforeLoad = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TParentContext
    location: ParsedLocation
    navigate: NavigateFn<AnyRoute>
    buildLocation: BuildLocationFn<AnyRoute>
    cause: 'enter' | 'stay'
  },
) => Promise<TRouteContext> | TRouteContext | void
```

- Optional
- This async function is called before a route is loaded. If an error is thrown here, the route's loader will not be called and the route will not render. If thrown during a navigation, the navigation will be cancelled and the error will be passed to the `onError` function. If thrown during a preload event, the error will be logged to the console and the preload will fail.
- If this function returns a promise, the route will be put into a pending state and cause rendering to suspend until the promise resolves. If this routes pendingMs threshold is reached, the `pendingComponent` will be shown until it resolved. If the promise rejects, the route will be put into an error state and the error will be thrown during render.
- If this function returns a `TRouteContext` object, that object will be merged into the route's context and be made available in the `loader` and other related route components/methods.
- It's common to use this function to check if a user is authenticated and redirect them to a login page if they are not. To do this, you can either return or throw a `redirect` object from this function.

#### `loader`

```tsx
type loader = (
  opts: RouteMatch & {
    search: TFullSearchSchema
    abortController: AbortController
    preload: boolean
    params: TAllParams
    context: TAllContext
    location: ParsedLocation
    navigate: NavigateFn<AnyRoute>
    buildLocation: BuildLocationFn<AnyRoute>
    cause: 'enter' | 'stay'
  },
) => Promise<TLoaderData> | TLoaderData | void
```

- Optional
- This async function is called when a route is matched and passed the route's match object. If an error is thrown here, the route will be put into an error state and the error will be thrown during render. If thrown during a navigation, the navigation will be cancelled and the error will be passed to the `onError` function. If thrown during a preload event, the error will be logged to the console and the preload will fail.
- If this function returns a promise, the route will be put into a pending state and cause rendering to suspend until the promise resolves. If this routes pendingMs threshold is reached, the `pendingComponent` will be shown until it resolved. If the promise rejects, the route will be put into an error state and the error will be thrown during render.
- If this function returns a `TLoaderData` object, that object will be stored on the route match until the route match is no longer active. It can be accessed using the `useLoaderData` hook in any component that is a child of the route match before another `<Outlet />` is rendered.

#### `loaderDeps`

- Type: `(opts: { search: TFullSearchSchema; location: ParsedLocation, context: TAllContext }) => Record<string, any>`
- Optional
- A function that will be called before this route is matched to provide additional unique identification to the route match and serve as a dependency tracker for when the match should be reloaded. It should return any serializable value that can uniquely identify the route match from navigation to navigation.
- By default, path params are already used to uniquely identify a route match, so it's unnecessary to return these here.
- If your route match relies on search params or context values for unique identification, it's required that you return them here so they can be made available in the `loader`'s `deps` argument.

#### `staleTime`

- Type: `number`
- Optional
- Defaults to `routerOptions.defaultStaleTime`, which defaults to `0`
- The amount of time in milliseconds that a route match's loader data will be considered fresh. If a route match is matched again within this time frame, its loader data will not be reloaded.

#### `preloadStaleTime`

- Type: `number`
- Optional
- Defaults to `routerOptions.defaultPreloadStaleTime`, which defaults to `30_000` ms (30 seconds)
- The amount of time in milliseconds that a route match's loader data will be considered fresh when preloading. If a route match is preloaded again within this time frame, its loader data will not be reloaded. If a route match is loaded (for navigation) within this time frame, the normal `staleTime` is used instead.

#### `gcTime`

- Type: `number`
- Optional
- Defaults to `routerOptions.defaultGcTime`, which defaults to 30 minutes.
- The amount of time in milliseconds that a route match's loader data will be kept in memory after a preload or it is no longer in use.

#### `shouldReload`

- Type: `boolean | ((args: LoaderArgs) => boolean)`
- Optional
- If `false` or returns `false`, the route match's loader data will not be reloaded on subsequent matches.
- If `true` or returns `true`, the route match's loader data will be reloaded on subsequent matches.
- If `undefined` or returns `undefined`, the route match's loader data will adhere to the default stale-while-revalidate behavior.

#### `caseSensitive`

- Type: `boolean`
- Optional
- If `true`, this route will be matched as case-sensitive

#### `wrapInSuspense`

- Type: `boolean`
- Optional
- If `true`, this route will be forcefully wrapped in a suspense boundary, regardless if a reason is found to do so from inspecting its provided components.

#### `pendingMs`

- Type: `number`
- Optional
- Defaults to `routerOptions.defaultPendingMs`, which defaults to `1000`
- The threshold in milliseconds that a route must be pending before its `pendingComponent` is shown.

#### `pendingMinMs`

- Type: `number`
- Optional
- Defaults to `routerOptions.defaultPendingMinMs` which defaults to `500`
- The minimum amount of time in milliseconds that the pending component will be shown for if it is shown. This is useful to prevent the pending component from flashing on the screen for a split second.

#### `preloadMaxAge`

- Type: `number`
- Optional
- Defaults to `30_000` ms (30 seconds)
- The maximum amount of time in milliseconds that a route's preloaded route data will be cached for. If a route is not matched within this time frame, its loader data will be discarded.

#### `preSearchFilters`

- Type: `((search: TFullSearchSchema) => TFullSearchSchema)[]`
- Optional
- An array of functions that will be called when generating any new links to this route or its grandchildren.
- Each function will be called with the current search params and should return a new search params object that will be used to generate the link.
- It has a `pre` prefix because it is called before the user-provided function that is passed to `navigate`/`Link` etc has a chance to modify the search params.

#### `postSearchFilters`

- Type: `((search: TFullSearchSchema) => TFullSearchSchema)[]`
- Optional
- An array of functions that will be called when generating any new links to this route or its grandchildren.
- Each function will be called with the current search params and should return a new search params object that will be used to generate the link.
- It has a `post` prefix because it is called after the user-provided function that is passed to `navigate`/`Link` etc has modified the search params.

#### `onError`

- Type: `(error: unknown) => void`
- Optional
- A function that will be called when an error is thrown during a navigation or preload event.
- If this function returns a `redirect` object, the redirect will be applied immediately.

#### `onEnter`

- Type: `(match: RouteMatch) => void`
- Optional
- A function that will be called when a route is matched and loaded after not being matched in the previous location.

#### `onStay`

- Type: `(match: RouteMatch) => void`
- Optional
- A function that will be called when a route is matched and loaded after being matched in the previous location.

#### `onLeave`

- Type: `(match: RouteMatch) => void`
- Optional
- A function that will be called when a route is no longer matched after being matched in the previous location.

#### `component`

- Type: `RouteComponent` or `LazyRouteComponent`
- Optional - Defaults to `<Outlet />`
- The content to be rendered when the route is matched.

#### `errorComponent`

- Type: `RouteComponent` or `LazyRouteComponent`
- Optional - Defaults to `routerOptions.defaultErrorComponent`
- The content to be rendered when the route encounters an error.

#### `pendingComponent`

- Type: `RouteComponent` or `LazyRouteComponent`
- Optional - Defaults to `routerOptions.defaultPendingComponent`
- The content to be rendered if and when the route is pending and has reached its pendingMs threshold.
