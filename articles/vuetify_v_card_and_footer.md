---
title: "Vuetifyのv-cardでFooterを固定させてスクロールする方法"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['nuxtjs', 'vuetify']
published: true
---

VuetifyのDialogを使ってv-cardを表示させるときにFooterを画面内に固定させる方法を備忘録程度に。

## 完成形
せっかちめな人はこちらを参考に。
@[codepen](https://codepen.io/harunonsystem/pen/dyWmYWR)

# Buttonを用意してダイアログを作成

```html: sample.vue
   <v-dialog
      v-model="dialog"
      scrollable
      max-width="300px"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          color="primary"
          dark
          v-bind="attrs"
          v-on="on"
        >
          Open Dialog
        </v-btn>
      </template>
      <!-- ここにダイアログ開いたあとのコンテンツを書いていく -->
    </v-dialog>
```
ボタンをタップするとダイアログが出てくるようにできました。
`v-slot:activator`のついた`<template>`以下にダイアログの中に表示したい`v-card`を書いていきます。

# ダイアログの中にv-cardを書く

`</template>` 直下にv-cardで表示する中身を書いていきます。
```html: sample.vue
   <v-dialog
      v-model="dialog"
      scrollable
      max-width="300px"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          color="primary"
          dark
          v-bind="attrs"
          v-on="on"
        >
          Open Dialog
        </v-btn>
      </template>
        // add ↓↓↓
      <v-card>
        <v-card-title>Select Country</v-card-title>
        <v-divider></v-divider>
        // スクロールする範囲を設定したいのでmax-heightを指定
        <v-card-text style="height: 300px;">
          <v-radio-group
            v-model="dialogm1"
            column
          >
            <v-radio
              label="Bahamas, The"
              value="bahamas"
            ></v-radio>
            <v-radio
              label="Bahrain"
              value="bahrain"
            ></v-radio>
            <v-radio
              label="Bangladesh"
              value="bangladesh"
            ></v-radio>
                  <v-radio
              label="Brunei"
              value="brunei"
            ></v-radio>
            <v-radio
              label="Bulgaria"
              value="bulgaria"
            ></v-radio>
            <v-radio
              label="Burkina Faso"
              value="burkina"
            ></v-radio>
            <v-radio
              label="Burma"
              value="burma"
            ></v-radio>
            <v-radio
              label="Burundi"
              value="burundi"
            ></v-radio>
                  <v-radio
              label="Brunei"
              value="brunei"
            ></v-radio>
            <v-radio
              label="Bulgaria"
              value="bulgaria"
            ></v-radio>
            <v-radio
              label="Burkina Faso"
              value="burkina"
            ></v-radio>
            <v-radio
              label="Burma"
              value="burma"
            ></v-radio>
            <v-radio
              label="Burundi"
              value="burundi"
            ></v-radio>
            </v-radio-group>
        </v-card-text>
        
        // 固定させるFooter
        <v-divider></v-divider>
        <v-card-actions>
          <v-btn
            color="blue darken-1"
            text
            @click="dialog = false"
          >
            Close
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="dialog = false"
          >
            Save
          </v-btn>
        </v-card-actions>
    </v-dialog>
```
ざっくり図にするとこんな感じです。
```
v-card-title
--- divider ---
v-card-text
--- divider ---
v-card-actions
```
`v-card-title`が`Header`
`v-card-text`が`Body`
`v-card-actions`が`Footer`
みたいなイメージです。
Bodyをscrollableにしたいので`v-card-text` に`style="300px"`を書いています。
`v-dialog` と `v-card-text` のheightを調整してスクロール範囲やダイアログ全体のサイズを調整できます。
Header, Footerは固定になります。

## まとめ
1. `v-dialog` に `scrollable`を追加
2. `v-card-text` に `max-height`を指定
3. `v-card-actions` にFooterとして固定させるコンテンツ（だいたいButton）を書く

@[codepen](https://codepen.io/harunonsystem/pen/dyWmYWR)
