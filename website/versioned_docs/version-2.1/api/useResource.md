---
id: version-2.1-useresource
title: useResource()
original_id: useresource
---

<!--DOCUSAURUS_CODE_TABS-->
<!--Type-->

```typescript
function useResource(fetchShape: ReadShape, params: Params | null):
  SchemaOf<typeof fetchShape.schema>;

function useResource(...[fetchShape: ReadShape, params: Params | null]):
  SchemaOf<typeof fetchShape.schema>[];
```

<!--With Generics-->

```typescript
function useResource<
  Params extends Readonly<object>,
  Body extends Readonly<object | string> | void,
  S extends Schema
>(fetchShape: ReadShape<S, Params, Body>, params: Params | null): SchemaOf<S>;

function useResource<
  Params extends Readonly<object>,
  Body extends Readonly<object | string> | void,
  S extends Schema
>(...[fetchShape: ReadShape<S, Params, Body>, params: Params | null]): SchemaOf<S>[];
```

<!--END_DOCUSAURUS_CODE_TABS-->

Excellent for retrieving the data you need.

- Triggers fetch:
  - On first-render and when parameters change
  - and When not in cache or result is considered stale
  - and When no identical requests are in flight
  - and when params are not null
- [On Error (404, 500, etc)](https://www.restapitutorial.com/httpstatuscodes.html):
  - Throws error to be [caught](../guides/network-errors.md) by [Error Boundaries](https://reactjs.org/docs/error-boundaries.html)
- While Loading:
  - Returns previously cached if exists (even if stale)
  - [Suspend rendering](../guides/loading-state.md) otherwise

## Single

```tsx
function Post({ id }: { id: number }) {
  const post = useResource(PostResource.detailShape(), { id });
  // post as PostResource
}
```

## List

```tsx
function Posts() {
  const posts = useResource(PostResource.listShape(), {});
  // posts as PostResource[]
}
```

## Parallel

```tsx
function Posts() {
  const [user, posts] = useResource(
    [UserResource.detailShape(), { id: userId }],
    [PostResource.listShape(), { userId }],
  );
  // user as UserResource
  // posts as PostResource[]
}
```

## Sequential

```tsx
function PostWithAuthor() {
  const post = useResource(PostResource.detailShape(), { id });
  // post as PostResource
  const author = useResource(UserResource.detailShape(), {
    id: post.userId,
  });
  // author as UserResource
}
```

## Useful `FetchShape`s to send

[Resource](./Resource.md#provided-and-overridable-methods) provides these built-in:

- detailShape()
- listShape()

Feel free to add your own [FetchShape](./FetchShape.md) as well.
