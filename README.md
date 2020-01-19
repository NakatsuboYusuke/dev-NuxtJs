# my-first-nuxt-app

> my-first-nuxt-app

## Build Setup

``` bash
# install dependencies
$ yarn install

# serve with hot reload at localhost:3000
$ yarn dev

# build for production and launch server
$ yarn build
$ yarn start

# generate static project
$ yarn generate
```

For detailed explanation on how things work, check out [Nuxt.js docs](https://nuxtjs.org).

## Index

- <a href="">プロジェクトを作成</a>
- <a href=""></a>

## プロジェクトを作成

```
$ yarn create nuxt-app my-first-nuxt-app
```

## 構成

```
$ brew install tree
$ tree -L 1

// =>
```

```
.
├── README.md
├── assets
├── components
├── layouts
├── middleware
├── node_modules
├── nuxt.config.js
├── package.json
├── pages
├── plugins
├── static
├── store
└── yarn.lock
```

## ホットリロード(Hot Module Replacement(HMR))
http://localhost:3000/ にアクセスすると、自動的にコンテンツが切り替わる。

```
# pages/index.vue
:<snip>
<h1 class="title">
  <!-- my-first-nuxt-app -->
  Hello, world
</h1>
:<snip>

// => Hello, world
```

## 動的なルーティング

```
# pages/users/index.vue
<template>
  <div>
    <p>
      /users/index.vue
    </p>
  </div>
</template>

# http://localhost:3000/users/potato4d
// => /users/index.vue

# pages/users/_id.vue
<template>
  <div>
    <p>
      /users/_id.vue
    </p>
  </div>
</template>

# http://localhost:3000/users/potato4d
// => /users/_id.vue

# pages/users/register.vue
<template>
  <div>
    <p>
      /users/register.vue
    </p>
  </div>
</template>

# http://localhost:3000/users/register
// => /users/register.vue
// => _id.vue より優先して表示される
```
