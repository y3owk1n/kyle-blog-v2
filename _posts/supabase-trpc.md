---
title: "Supabase + tRPC = ❤️"
excerpt: "Hi, I'm Kyle. I've left this blog untouched for almost a year now, but I'm re-activating it. Meanwhile I decided to move my blog into a Next.js site."
coverImage: "/assets/blog/supabase-trpc/cover.jpg"
date: "2022-07-24T16:00:00.000Z"
ogImage:
  url: "/assets/blog/supabase-trpc/cover.jpg"
---

[Next.js](https://nextjs.org/) is amazing and [Typescript](https://www.typescriptlang.org) made development even more amazing! The entire time i was finding a way to have end-to-end type safety solution until i met [tRPC](https://trpc.io), almost perfect there.

But... it wasn't yet perfect enough.

There are a lot of examples of end-to-end tRPC in the web, where majority that I followed was utilising [Prisma](https://www.prisma.io) as the ORM layer for type safety.

> At this point, I am still in love with [Supabase](https://supabase.com)

Overall, Supabase is great, and I am able to generate types from Supabase directly by following the [doc](https://supabase.com/docs/guides/api/generating-types). But here's the issue... the generated types are not suitable to be inferred directly through tRPC routers, since I am only able to grab the main types from it `definitions['table']`, but not the exact types that I am returning from the API.

And here I came across this cool tiny tool [postgrest-js-tools](https://www.npmjs.com/package/postgrest-js-tools). So how does this work? _(I won't be going through how to use tRPC here, refer to the docs 😉)_

First install the package:

```
yarn add postgrest-js-tools
```

Import the package into my tRPC router:

```
import { getShape, getFields } from "postgrest-js-tools";
```

Get the shape of a table:

```
// The shape infer the types for Supabase query and return only the field specified.

const expectedShape = getShape<definition["table"]>()({
  id: true,
  fieldThatIWant: true,
})
```

Request for data:

```
const { data, error } = await supabase
  .from<typeof expectedShape>("table")
  .select(getFields(expectedShape));

// data returned => { id: 1, fieldThatIWant: 'value' }
// typeof data => { id: number, fieldThatIWant: string }
```

Now we can pass the data down to the client through tRPC with typesafety!

Overall I am so happy that I found this tool. Just to end this, I still think Prisma should be a better fit in the stack for tRPC. I had considered to use [Prisma with Supabase](https://supabase.com/docs/guides/integrations/prisma), however it seems like a hassle and lot's of incompatibilities at this point of time. Will definitely revisit this in the future.

> Try it out if you're using tRPC with Supabase, it's amazing still ❤️