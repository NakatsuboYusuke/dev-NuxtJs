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
- <a href="">ホットリロード</a>
- <a href="">動的なルーティング</a>
- <a href="">コンテンツの出し分け</a>
- <a href=""></a>

## プロジェクトを作成

```
$ yarn create nuxt-app my-first-nuxt-app
```

### 構成

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

## コンテンツの出し分け
Nuxt.jsは_(アンダースコア)以降の名前をプロパティとして認識、paramsへ自動的に代入する。

```
# pages/users/_id.vue
<template>
  <div>
    <p>
      <!-- /users/_id.vue -->
      User ID: {{ userId }}
    </p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      userId: this.$route.params.id
    }
  }
}
</script>

# http://localhost:3000/users/_id
// => User ID: _id
```

## 外部リソースの取得

### axious-module をインストール
moduleにパッケージ名の配列を渡すと、自動的に読み込まれる。

```
$ yarn add @nuxtjs/axios
```

```
# nuxt.config.js
export default {
  :<snip>
  /*
  ** Nuxt.js modules
  */
  modules: [
    '@nuxtjs/axios'
  ],
  axios: {

  },
  :<snip>
}
```

### axios で API へアクセス

```
<template>
  <div class="container">
    <!-- -->
  </div>
</template>

<script>
export default {
  async mounted() {
    console.log(
      JSON.stringify(await
        this.$axios.$get('https://qiita.com/api/v2/items?query=tag:nuxt.js', true, ' ')
      )
    )
  }
}
</script>

// => コンソールに結果が出力される

// => すべてのコンポーネントで $axios に axios が格納される
// => $axios に対して、$get/$post などのリクエストが送信可能になる
```

### API のトークンを発行

```
# Qiita の場合
https://qiita.com/settings/tokens/new

// => 発行されたトークンを保存しておく
```

### axios で 認証付きで API にアクセス

```
# plugins/axios.js
export default function ({ $axios }) {
  $axios.onRequest((config) => {
    if (process.env.QIITA_TOKEN) {
      // リクエストヘッダーに認証情報を追加
      config.headers.common['Authorization'] = `Bearer ${process.env.QIITA_TOKEN}`
    }
    return config
  })
}

# nuxt.config.js
export default {
  :<snip>
  plugins: [
    '~/plugins/axios.js'
  ],
  // 渡したい環境変数がある場合、環境変数を明記する
  env: {
    QIITA_TOKEN: process.env.QIITA_TOKEN
  },
  /*
  ** Nuxt.js dev-modules
  */
  buildModules: [
  ],
  /*
  ** Nuxt.js modules
  */
  modules: [
    '@nuxtjs/axios'
  ],
  axios: {

  },
  :<snip>
}

// => direnv をインストール
$ brew install direnv
$ export QIITA_TOKEN=取得したトークン > .envrc
$ echo QIITA_TOKEN
// => 取得したトークン
$ yarn dev

※ Bearer 認証にしないとエラーがでた
参考記事 => https://bit.ly/2G4F6LE
```
