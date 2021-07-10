---
title: "Nuxt.jsで覚える初めてのコンポーネント"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["nuxtjs", "javascript", "vue"]
published: false
---

Nuxt.jsを業務で使っています。
正直今まで`pages/` にベタ書きしてました。しかしコンポーネント作って共通化、朝令暮改で起こりまくる仕様変更に対応できるようにコンポーネントは必須でした。
`props` がなにかもわからず、コピペして動いてるけどまともに使えなくて泣いているところからスタートしました。
そういう初学者の備忘録です。🥲

# まずコンポーネントを作って親で読み込む

## 最初にシンプルなHTMLを作る
```javascript: sample.vue
<template>
    <h1>たいとる</h1>
</template>
```
まず、シンプルなHTMLを作ってみます。
ただの`h1`タグでタイトルを記述しているだけです。

`たいとる` を直接HTMLに記述せず、変数として渡すこともできます。

## たいとるを変数に
```javascript: sample.vue
<template>
    <h1 class="title">{{ title }}</h1>
</template>

<script>
export default {
    data() {
        return {
            title: 'たいとる',
        }
    }
}
</script>

<style>
    .title {
        color: #333;
        font-size: 1.5em;
    }
</style>
```
h1タグの中身を変更したい場合は、 `title` という変数を使って、 `{{ title }}` の部分を変更することができます。
そこだけ変わってもあまり意味ないように思えますが、`title`だけを編集したい場合HTMLを編集せずに済みます。

## 変数を親で読み込む

先程までに作っていたファイルをコンポーネントとして読み込みます。
```javascript
├─ components
│  └─ title.vue
├─ pages
│  └─ home.vue
│  ├─ profile.vue
│  └─ blog.vue
```
で使われる `title.vue` というコンポーネントを作ってみます。
home, profile, blog というページで使われることを想定しています。

Parent(home, profile, blog側)
```javascript: parent.vue
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

Child(title側)
```javascript: child.vue
<template>
    <h1 class="title">{{ title }}</h1>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            default: 'たいとる',
        }
    }
}
</script>

<style>
    .title {
        color: #333;
        font-size: 1.5em;
    }
</style>
```

これで同じstyleを指定しつつ、親でタイトルを変更するだけで使い回せるコンポーネントが作れました🎉

先程 
```javascript
    data() {
        return {
            title: 'たいとる',
        }
    }
```
と記述しましたが、この部分をPropsで親から読み込みます。
```javascript
    props: {
        title: {
            type: String,
            default: 'たいとる',
        }
    }
```

親コンポーネントから渡すことで、`title`変数をPageによって変更しつつ、子コンポーネントに渡すことができます。
渡した変数以外の部分は子コンポーネントで共通化できています。

親側で
```javascript
    <template>
        <Child title="たいとる" />
    </template>
```
にすれば、`title: 'たいとる',`になりますし、

```javascript
    <template>
        <Child title="ホーム" />
    </template>
```
にすれば、`title: 'ホーム',`になります。

title変数以外は、子コンポーネントで共通化しているので読み込むすべての箇所で一緒になります。

コンポーネント単位にもよりますが、タイトルを記述している箇所のHTML/CSS/JavsScriptを変更したいといった要望が来たときに子コンポーネントの`title.vue` を変更するだけでできます。
一括置換などでミスの発生を防いだり、共通化することで管理しやすくなります。

余談ですが、デザイン側でも共通化しておくことで管理しやすく、デザイナーエンジニア間でコミュニケーションが取りやすくなるかと思います。（大抵の自社開発では当たり前のようにやってそう🥺）


propsのtype(型)にはStringの他に、Number, Boolean, Array, Object, Function, Date, Symbol が使えます。

# メソッドを作成して親で読み込む

コンポーネントといえばボタンみたいなイメージがあるので作ってみます。
アプリ作ってれば保存、キャンセル、追加などボタンを複数作る場面があると思います。

```vue: child.vue
<template>
    <button @click="save(value)">{{ BtnText }}</button>
</template>

<script>
export default {
    data() {
        return {
            value: 1,
        }
    },
    props: {
        BtnText: {
            type: String,
            default: null,
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

```vue: parent.vue
<template>
    <SaveBtn BtnText="保存" @save="save()" />
</template>

<script>
export default {
    components: {
        SaveBtn,
    },
    methods: {
        save(value) {
            // childのvalueを親に渡す
            console.log(value)
        }
    }
}
</script>
```

`$emit`することで子コンポーネントのデータを親に引き継いでいます。

## `emit`で渡す値が複数ある場合
参考） https://qiita.com/kambe0331/items/f17a54027cdfad99740c

```vue: child.vue
<template>
<button @click="save()">{{ BtnText }}</button>
</template>

<script>
export default {
    props: {
        BtnText: {
            type: String,
            default: null,
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


```vue: parent.vue
<template>
    <SaveBtn BtnText="保存" @click="save()" />
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
child componentから渡される値が複数の場合、`value`は配列で渡されます。