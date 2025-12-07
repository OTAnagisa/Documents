# グリッドレイアウト（Vuetify）

## 簡単な説明

**グリッドレイアウト**は、画面を縦横に分割して要素を配置するレイアウトシステムです。Vuetifyでは、12列のグリッドシステムを使って、レスポンシブ（画面サイズに応じて変わる）なレイアウトを作成できます。画面サイズが変わっても、適切にレイアウトが調整されます。

## グリッドシステムとは

グリッドシステムは、画面を12列に分割して、要素の幅を指定するシステムです。

```
画面を12列に分割:
| 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 |
```

要素の幅を「12列のうち何列分使うか」で指定します。

- **12列**: 画面全体の幅
- **6列**: 画面の半分の幅
- **4列**: 画面の3分の1の幅
- **3列**: 画面の4分の1の幅

## Vuetifyのグリッドシステム

### 基本的な構造

Vuetifyでは、以下のコンポーネントを使います：

- **v-container**: コンテナ（最大幅を制限）
- **v-row**: 行（横方向のグループ）
- **v-col**: 列（実際の要素を配置）

```vue
<template>
  <v-container>
    <v-row>
      <v-col cols="12" md="6">
        <!-- コンテンツ1 -->
      </v-col>
      <v-col cols="12" md="6">
        <!-- コンテンツ2 -->
      </v-col>
    </v-row>
  </v-container>
</template>
```

### 基本的な例

```vue
<template>
  <v-container>
    <v-row>
      <!-- 画面全体の幅（12列） -->
      <v-col cols="12">
        <div class="box">全幅</div>
      </v-col>
    </v-row>
    
    <v-row>
      <!-- 画面の半分（6列ずつ） -->
      <v-col cols="6">
        <div class="box">左半分</div>
      </v-col>
      <v-col cols="6">
        <div class="box">右半分</div>
      </v-col>
    </v-row>
    
    <v-row>
      <!-- 画面を3等分（4列ずつ） -->
      <v-col cols="4">
        <div class="box">1/3</div>
      </v-col>
      <v-col cols="4">
        <div class="box">1/3</div>
      </v-col>
      <v-col cols="4">
        <div class="box">1/3</div>
      </v-col>
    </v-row>
  </v-container>
</template>

<style scoped>
.box {
  background: #f0f0f0;
  padding: 16px;
  text-align: center;
}
</style>
```

## レスポンシブデザイン

画面サイズに応じて、列の幅を変えることができます。

### ブレークポイント

Vuetifyには以下のブレークポイントがあります：

| ブレークポイント | 幅 | 説明 |
|----------------|-----|------|
| xs | < 600px | スマートフォン |
| sm | 600px ~ 960px | タブレット（縦） |
| md | 960px ~ 1264px | タブレット（横）・小さいPC |
| lg | 1264px ~ 1904px | PC |
| xl | ≥ 1904px | 大きいPC |

### レスポンシブな指定

```vue
<template>
  <v-container>
    <v-row>
      <!-- 
        xs: スマートフォンでは12列（全幅）
        md: タブレット以上では6列（半分）
        lg: PCでは4列（3分の1）
      -->
      <v-col cols="12" md="6" lg="4">
        <div class="box">カード1</div>
      </v-col>
      <v-col cols="12" md="6" lg="4">
        <div class="box">カード2</div>
      </v-col>
      <v-col cols="12" md="6" lg="4">
        <div class="box">カード3</div>
      </v-col>
    </v-row>
  </v-container>
</template>
```

この例では：
- **スマートフォン**: 各カードが縦に並ぶ（12列 = 全幅）
- **タブレット**: 各カードが横に2つ並ぶ（6列 = 半分）
- **PC**: 各カードが横に3つ並ぶ（4列 = 3分の1）

## よく使うレイアウトパターン

### 1. 2カラムレイアウト

```vue
<template>
  <v-container>
    <v-row>
      <!-- サイドバー -->
      <v-col cols="12" md="3">
        <div class="sidebar">サイドバー</div>
      </v-col>
      
      <!-- メインコンテンツ -->
      <v-col cols="12" md="9">
        <div class="main">メインコンテンツ</div>
      </v-col>
    </v-row>
  </v-container>
</template>
```

### 2. 3カラムレイアウト

```vue
<template>
  <v-container>
    <v-row>
      <v-col cols="12" md="4">
        <div class="column">カラム1</div>
      </v-col>
      <v-col cols="12" md="4">
        <div class="column">カラム2</div>
      </v-col>
      <v-col cols="12" md="4">
        <div class="column">カラム3</div>
      </v-col>
    </v-row>
  </v-container>
</template>
```

### 3. カードグリッド

```vue
<template>
  <v-container>
    <v-row>
      <v-col
        v-for="item in items"
        :key="item.id"
        cols="12"
        sm="6"
        md="4"
        lg="3"
      >
        <v-card>
          <v-card-title>{{ item.title }}</v-card-title>
          <v-card-text>{{ item.description }}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const items = ref([
  { id: 1, title: 'カード1', description: '説明1' },
  { id: 2, title: 'カード2', description: '説明2' },
  { id: 3, title: 'カード3', description: '説明3' },
  { id: 4, title: 'カード4', description: '説明4' }
])
</script>
```

## 間隔の調整

### 列の間隔（gutter）

`v-row`の`gutter`プロパティで、列の間隔を調整できます。

```vue
<template>
  <!-- 間隔なし -->
  <v-row no-gutters>
    <v-col cols="6">...</v-col>
    <v-col cols="6">...</v-col>
  </v-row>
  
  <!-- 間隔あり（デフォルト） -->
  <v-row>
    <v-col cols="6">...</v-col>
    <v-col cols="6">...</v-col>
  </v-row>
</template>
```

### 列の配置

`v-row`の`align`や`justify`プロパティで、列の配置を調整できます。

```vue
<template>
  <!-- 中央揃え -->
  <v-row justify="center">
    <v-col cols="6">...</v-col>
  </v-row>
  
  <!-- 右揃え -->
  <v-row justify="end">
    <v-col cols="6">...</v-col>
  </v-row>
  
  <!-- 均等配置 -->
  <v-row justify="space-between">
    <v-col cols="4">...</v-col>
    <v-col cols="4">...</v-col>
  </v-row>
</template>
```

## オフセット

列の前に空白を追加できます。

```vue
<template>
  <v-container>
    <v-row>
      <!-- 3列分の空白の後、6列のコンテンツ -->
      <v-col cols="6" offset-md="3">
        <div class="box">中央に配置</div>
      </v-col>
    </v-row>
  </v-container>
</template>
```

## 実践的な例

### 商品一覧ページのレイアウト

```vue
<template>
  <v-container>
    <!-- ヘッダー -->
    <v-row>
      <v-col cols="12">
        <h1>商品一覧</h1>
      </v-col>
    </v-row>
    
    <!-- フィルターと商品一覧 -->
    <v-row>
      <!-- フィルター（サイドバー） -->
      <v-col cols="12" md="3">
        <v-card>
          <v-card-title>フィルター</v-card-title>
          <v-card-text>
            <!-- フィルターの内容 -->
          </v-card-text>
        </v-card>
      </v-col>
      
      <!-- 商品一覧 -->
      <v-col cols="12" md="9">
        <v-row>
          <v-col
            v-for="product in products"
            :key="product.id"
            cols="12"
            sm="6"
            lg="4"
          >
            <v-card>
              <v-img :src="product.image" height="200"></v-img>
              <v-card-title>{{ product.name }}</v-card-title>
              <v-card-text>
                <p>{{ product.description }}</p>
                <p class="text-h6">{{ product.price }}円</p>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>
```

### ダッシュボードのレイアウト

```vue
<template>
  <v-container fluid>
    <v-row>
      <!-- 統計カード -->
      <v-col cols="12" sm="6" md="3">
        <v-card>
          <v-card-title>総売上</v-card-title>
          <v-card-text class="text-h4">¥1,000,000</v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" sm="6" md="3">
        <v-card>
          <v-card-title>注文数</v-card-title>
          <v-card-text class="text-h4">150</v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" sm="6" md="3">
        <v-card>
          <v-card-title>ユーザー数</v-card-title>
          <v-card-text class="text-h4">1,234</v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" sm="6" md="3">
        <v-card>
          <v-card-title>商品数</v-card-title>
          <v-card-text class="text-h4">567</v-card-text>
        </v-card>
      </v-col>
    </v-row>
    
    <!-- グラフとテーブル -->
    <v-row>
      <v-col cols="12" md="8">
        <v-card>
          <v-card-title>売上グラフ</v-card-title>
          <v-card-text>
            <!-- グラフの内容 -->
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" md="4">
        <v-card>
          <v-card-title>最近の注文</v-card-title>
          <v-card-text>
            <!-- テーブルの内容 -->
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
```

## まとめ

- **グリッドシステム**: 画面を12列に分割して要素を配置
- **v-container**: コンテナ（最大幅を制限）
- **v-row**: 行（横方向のグループ）
- **v-col**: 列（実際の要素を配置）
- **レスポンシブ**: 画面サイズに応じて列の幅を変更
- **ブレークポイント**: xs, sm, md, lg, xl

## 参考資料

- [Vuetify - グリッドシステム](https://vuetifyjs.com/ja/components/grids/#section-4f7f304465b9)
- [Vuetify - ブレークポイント](https://vuetifyjs.com/ja/features/breakpoints/)

