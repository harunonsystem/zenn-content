---
title: "Nuxt.jsで使うコンポーネントのあれこれ"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxtjs", "javascript", "vue"]
published: false
---

Nuxt.jsを仕事で使っています。
`pages/` にベタ書きしてたら色々しんどくなった、けどコンポーネントの使い方（何をどこまでやったらいいのかわからん）になったので備忘録程度にまとめていきます。
`props` がなにかもわからず、コピペして動いてるけどまともに使えなくて泣いているところからスタートしました。

1. まずコンポーネントを作って親で読み込んでみる

```javascript
<template>
    <h1>title</h1>
</template>
```
だから何だと言われそうですがまぁこういうのがあったとして。
普通にアプリを作っていれば、タイトルは幾つかのページで使われ、その都度内容が変更されると思います。（HomeとかProfileとかBlogとか）
ただの`ｈ1`で終わればいいですが、「タイトルのCSSは全部いっしょにしちゃってー」とか言われます。

なので
```
pages/home.vue
pages/profile.vue
pages/blog.vue
```
で使われる `title.vue` というコンポーネントを作ってみます。

Parent ex)pages.home.vue
```javascript
<template>
    <Title title="ホーム" />
</template>

<script>
import Title from '@components/title'
export default {
    components: {
        Title,
    }
}
</script>
```

Child
```javascript
<template>
    <h1 class="default">{{ title }}</h1>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            default: '',
        }
    }
}
</script>

<style>
.default {
    font-size: 12px;
    color:red;
}
</style>
```

これで同じstyleを指定しつつ、親でタイトルを変更するだけで使い回せるコンポーネントが作れました🎉

2. メソッドを実行する
やっぱボタン作って実行したいじゃんね（唐突）

アプリ作ってれば保存、キャンセル、追加などボタンを複数作る場面があると思います。
作ってみましょう。

```vue
//child component
<template>
    <button @click="save(value)">{{ BtnText }}</button>
</template>

<script>
export default {
    props: {
        BtnText: {
            type: String,
            default: ''
        },
    },
    methods: {
        save(value) {
            this.$emit('save', this.value)
        }
    }
}
</script>
```
さっきと同様、`BtnText`で親コンポーネントから名前を引き継げるようにしてます。

```vue
// parent component
<template>
    <SaveBtn title="保存" @save="save()" />
</template>

<script>
export default {
    components: {
        SaveBtn,
    },
    methods: {
        save() {
            // わからんが何か保存するねん
        }
    }
}
</script>
```

`$emit`することで子コンポーネントのデータを親に引き継いでいます。

Methodを親に持たせるべきか、子に持たせるべきかケースバイケースなのだろうがわからんので誰か教えて欲しい…
pages 直下のmethodsに処理を書いてて、子コンポーネントではemitして値を受け渡していくのがベターなのかな。。

3. `emit`で渡す値が複数ある場合
参考） https://qiita.com/kambe0331/items/f17a54027cdfad99740c
```vue
//child
<template>
<button @click="save()">{{ BtnText }}</button>
</template>

<script>
export default {
    props: {
        BtnText: {
            type: String,
            default: '',
        },
    },
    data() {
        return {
            data1: 'message1',
            data2: 'message2',
        },
    },
    methods: {
        save() {
            this.$emit('save', data1, data2)
        }
    }
}
</script>
```


```vue
//parent
<template>
    <SaveBtn title="保存" @click="save()" />
</template>

<script>
export default {
    components: {
        SaveBtn,
    },
    methods: {
        save(...value) {
            console.log(value)
            // [data1, data2]
        }
    }
}
</script>
```
value はArrayで取得できます。
各自適当に使っていけます。