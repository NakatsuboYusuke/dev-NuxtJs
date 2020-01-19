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

- <a href="https://github.com/NakatsuboYusuke/dev-NuxtJs#%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E4%BD%9C%E6%88%90">プロジェクトを作成</a>
- <a href="https://github.com/NakatsuboYusuke/dev-NuxtJs#%E3%83%9B%E3%83%83%E3%83%88%E3%83%AA%E3%83%AD%E3%83%BC%E3%83%89hot-module-replacementhmr">ホットリロード</a>
- <a href="https://github.com/NakatsuboYusuke/dev-NuxtJs#%E5%8B%95%E7%9A%84%E3%81%AA%E3%83%AB%E3%83%BC%E3%83%86%E3%82%A3%E3%83%B3%E3%82%B0">動的なルーティング</a>
- <a href="https://github.com/NakatsuboYusuke/dev-NuxtJs#%E3%82%B3%E3%83%B3%E3%83%86%E3%83%B3%E3%83%84%E3%81%AE%E5%87%BA%E3%81%97%E5%88%86%E3%81%91">コンテンツの出し分け</a>
- <a href="https://github.com/NakatsuboYusuke/dev-NuxtJs#%E5%A4%96%E9%83%A8%E3%83%AA%E3%82%BD%E3%83%BC%E3%82%B9%E3%81%AE%E5%8F%96%E5%BE%97">外部リソースの取得</a>
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

### axios で 認証付き API にアクセス

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

## サーバーサイドレンダリング
Nuxt.js 独自の asyncData 関数を実行し、サーバーサイドレンダリングを処理する。<br>
asyncData 関数は、コンポーネントの初期化前に実行される(= この時点では、thisにアクセスできない)

```
# pages/index.vue
<template>
  <section class="container">
    <div>
      <h3>Nuxt.js のタグが付けられた投稿の一覧</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
          <h4>
            <span>{{item.title}} </span>
          </h4>
          <div>{{item.body.slice(0, 130)}}……</div>
          <p><a target="_blank" :href="item.url">{{item.url}}</a></p>
        </li>
      </ul>
    </div>
  </section>
</template>

<script>
import Logo from '~/components/Logo.vue'

export default {
  // async mounted() {
  //   console.log(
  //     JSON.stringify(await
  //       this.$axios.$get('https://qiita.com/api/v2/items?query=tag:nuxt.js', true, ' ')
  //     )
  //   )
  // }
  // asyncData()関数を使用する
  async asyncData({ app }) {
    const items = await app.$axios.$get('https://qiita.com/api/v2/items?query=tag:nuxt.js')
    return {
      items
    }
  }
}
</script>

<style>
.container {
  min-height: 100vh;
  padding: 16px;
}

h3 {
  margin: 16px 0;
  padding: 8px 0;
  border-bottom: 1px solid #e5e5e5;
}

li + li {
  margin: 16px 0;
}

p {
  margin: 8px 0;
}
</style>
```

### サーバーサイドレンダリングとルーティング
Nuxt.js では、独自の asyncData 関数を中心としてページコンポーネントを構築する<br>
asyncData に入る情報は、総称としてcontextと呼ばれる。

```
# pages/index.vue
<template>
  :<snip>
  <small>
    <span>by </span>
    <nuxt-link :to="`/users/${item.user.id}`">
      {{item.user.id}}
    </nuxt-link>
  </small>
  :<snip>
</template>

# pages/users/_id.vue
<template>
  <section class="container">
    <div>
      <h3>{{ user.id }}</h3>
      <img :src="user.profile_image_url" width="120" alt="">
      <p>{{ user.description || 'No description' }}</p>
      <p>
        <nuxt-link to="/">
          <small><b>トップへ戻る</b></small>
        </nuxt-link>
      </p>
      <h3>{{ user.id }}さんの投稿一覧</h3>
      <ul>
        <li v-for="item in items" :key="item.id">
          <h4>
            <span>{{ item.title }}</span>
          </h4>
          <div>{{ item.body.slice(0, 130) }}……</div>
          <p><a target="_blank" :href="item.url">{{ item.url }}</a></p>
        </li>
      </ul>
    </div>
  </section>
</template>

<script>
export default {
  // data() {
  //   return {
  //     userId: this.$route.params.id
  //   }
  // }
  async asyncData({ route, app }) {
    const user = await app.$axios.$get(`https://qiita.com/api/v2/users/${route.params.id}`)
    const items = await app.$axios.$get(`https://qiita.com/api/v2/items?query=users:${route.params.id}`)
    return {
      user, items
    }
  }
}
</script>

<style scoped>
.container {
  min-height: 100vh;
  padding: 16px;
}
h3 {
  margin: 16px 0;
  padding: 8px 0;
  border-bottom: solid 1px #e5e5e5;
}
li + li {
  margin: 16px 0;
}
p {
  margin: 8px 0;
}
</style>
```
