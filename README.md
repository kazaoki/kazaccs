
# カザオキ Sassコーディングルール

ver 1.0.0 - 2018.5.24

## ディレクトリ構成

```
scss/
　├─ style.scss ... ローダー
　├─ _init.scss ... タグ初期化、基本スタイル
　├─ _parts.scss ... パーツ/コンポーネントごとのスタイル
　└─ _layouts.scss ... レイアウトごとのスタイル
```

## 基本ポリシー
基本的なパーツ/コンポーネントごとにスタイルを定義し、レイアウトタグ（header,section,footerなど）でそれらを配置・調整してゆくイメージ。
他で使うかどうか分からない、使わないだろう的なものも、部品にできそうなものはなるべく全てパーツ/コンポーネントというイメージで `_parts.scss` に定義し、そのパーツを `_layouts.scss` でレイアウトタグ（`header`, `section`, `footer` など）でそれらを配置・調整してゆくイメージ。

パーツ/コンポーネントのクラス名は1文字目を大文字にする。但し、**CSSは大文字小文字を区別しないので注意**すること。見た目でわかりやすくするだけの対処。

```scss
/* --- _parts.scss --- */
.Man {
	display: block;
	background: url(man.png) no-repeat;
	width: 100px;
	height: 10px;
}

/* --- _layouts.scss --- */
header {
	.Man {
		margin-top: 10px;
	}
}
footer {
	.Man {
		margin-top: 20px;
		text-align: right;
	}
}
```

## なるべく他の箇所・ページでもそのまま使い回せるように、なるべくタグ固定にしないようにする。

```scss
h2.big-title { ...; }
↓
.big-title { ...; }

nav.menu { ...; }
↓
.menu { ...; }
```

## パーツもレイアウトも、他所でも使用しやすいようにシンプルなクラス名付を心がける。

クラス名 `.TopLogo` や `.product-list` などダメ。クラス名に位置やページ情報を入れないよう。
別所ではちょっと違いがある場合などは、サブクラスを付ける。（サブクラス名は全て小文字）

前述の場合だと、こんな感じ。
- `.TopLogo` → `.Logo` でロゴの基本スタイルを定義、 `.Logo.top` でトップ特有の定義
- `.product-list` → `.List` でリストの基本スタイルを定義、 `.List.product` でproductページ特有の定義

これで、使いまわしやすく、派生しやすくなる。
以下、サンプル。

```scss
/* --- _parts.scss --- */
.Face {
	display: block;
	background: url(face.png) no-repeat;
	width: 32px;
	height: 32px;
	&.cry {
		background: url(face-cry.png) no-repeat;
	}
}
```
```html
<div><i class="Face">いいね！</div>
<div><i class="Face cry">悲しいね...</div>
```

## .wrap や .inner を使ってもOK

インデントが深くなりソースコードが汚くなるが、必要と感じたらそんなに考え込まずに使用してOK

```html
<header class="wrap">
  ...
</header>
<section class="wrap">
  ...
</section>
<section>
  <h2 class="wrap">...</h2>
</section>
<footer class="wrap">
  ...
</footer>
```

## bodyタグにクラス書くのをやめる

よく、
```html
<body class="under news">
  <h1>下層ページタイトル</h1>
...
```
と書いてケースを分けていたが、いわゆる"状態"に近いため、全体を把握しにくい。

ページによってスタイルを変更したい場合は、ちゃんとクラス名やサブクラスで対応すること。これにより、似たようなクラスの派生や他所への流用が容易になる。

```html
<body>
  <h1 class="BigTitle news">下層ページタイトル</h1>
...
```

クラス名を沢山かかなきゃならない気がするが、それほど気にすることもない。  
面倒な場合はレイアウトスタイルの方でごにょごにょしてもOK


## メディアクエリはメインクラスの下部にまとめて書く。

↓これは何回も出てきてごちゃごちゃしててNG...

```scss
.Button {
	width: 300px;
	margin: auto;
	background: url(../images/btn-back.png);
	.disabled {
		background: url(../images/btn-back-disabled.png);
		@media screen and (max-width: 640px) {
			background-size: 15px;
		}
		&:after {
			content: '×';
			display: inline;
			@media screen and (max-width: 640px) {
				content: none;
			}
		}
	}
	@media screen and (max-width: 640px) {
		background-size: 10px;
	}
}
```

↓これならOK！


```scss
.Button {
	width: 300px;
	margin: auto;
	background: url(../images/btn-back.png);
	.disabled {
		background: url(../images/btn-back-disabled.png);
		&:after {
			content: '×';
			display: inline;
		}
	}
	@media screen and (max-width: 640px) {
		background-size: 10px;
		.disabled {
			background-size: 15px;
			&:after {
				content: none;
			}
		}
	}
}
```

但し、致し方ない場合はやむなし。
