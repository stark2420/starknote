+++
author = "Hugo Authors"
title = "導入手順と改良・維持の手引き"
date = "2024-12-30"
description = ""
tags = [
]
categories = [
]
draft = true
+++

<!--more-->

https://vii5ard.github.io/whitespace/

## Hugoのインストール
以下のサイトからZIPでダウンロードをして任意の場所に解凍．  
https://github.com/gohugoio/hugo/releases  
> ここでは，最新版の`hugo_extended_0.140.2_windows-amd64.zip`にした．  
場所は，`C:\Program Files (x86)\Hugo_extended_0.140.1_windows-amd64`にした．

Pathを通す．  
Windowsキーを右クリック\システム\システムの詳細設定\環境変数(N)…\システム環境変数(S)のPathを選び，編集(I)…\新規  
先ほどのファイルの場所`C:\Program Files (x86)\Hugo_extended_0.140.1_windows-amd64`を入力．

```
$ hugo -help
```
が正しければ成功．

### Hugoのインストール（20260719更新）
Windows ターミナルまたはPowerShell で以下を入力．
```
winget install Hugo.Hugo.Extended
```
インストール確認．
```
hugo version
```

ついでにNode.jsをインストール.
```
winget install OpenJS.NodeJS.LTS
```

## Hugoでsiteを作成
```
$ hugo new site example
$ cd example
$ hugo server または $ hugo server -D
```
`http://localhost:1313/` にページが真っ白なページが表示される

## Hugoテーマのインストール
[Hugo Themes](https://themes.gohugo.io/) から選ぶ．しかし，[Notepadium](https://github.com/cntrump/hugo-notepadium) のようにそこにないものもある．  
結局githubからダウンロードするので，githubにあるものであれば何でもよい．

ダウンロードしたファイル（今回は`hugo-notepadium`）を`example\themes`の中へ入れる．  
ダウンロードしたファイルの中の`exampleSite`（今回は`hugo-notepadium\exampleSite`）の中のファイルを全て`example\`にコピーする．

これでテーマの適用が完了．

参考：[公式に載っていないHugoのインストール方法](https://qiita.com/utibori1/items/46fde79958ec9202d6c4)

### 新しいバージョンで実行したらエラーになる（20260719更新）
以下のエラーが出た．
```
WARN deprecated: .Site.LanguageCode was deprecated in Hugo v0.158.0 and will be removed in a future release. Use .Site.Language.Locale instead. Built in 281 ms ERROR error building site: render: [ja v1.0.0 guest] failed to render pages: render of "C:/Users/krimm/Desktop/blog/starknote/content/post/_index.md" failed: "C:\Users\krimm\Desktop\blog\starknote\themes\hugo-notepadium\layouts\_default\rss.xml:7:50": execute of template failed: template: rss.xml:7:50: executing "rss.xml" at <.Site.Author.email>: can't evaluate field Author in type page.Site
```
`themes\hugo-notepadium\layouts\_default\rss.xml`において，以下のように修正．
```
{{ .Site.Author.email }} -> {{ .Site.Params.author.email }}
{{ $.Site.Author.name }} -> {{ $.Site.Params.author.name }}
```
また，`starknote\config.toml`に以下を追加．
```
[params.author]
  name = "Stark"
  email = "your-email@example.com"
```

## Pagefindテーマを導入し，検索機能実装

設定ファイルの [[params.nav.custom]] セクションに以下を追加．
```
[[params.nav.custom]]
title = "Search"
url = "/starknote/search/"
```

`.github/workflows/` 内のYAMLファイル`hugo.yml`に以下を追記．
```
# 👇 【追加】Pagefindのインデックスを生成するステップ
- name: Generate Pagefind Search Index
  run: npx -y pagefind --site public
```

`content/search.md` を作成．
```
---
title: "検索"
layout: "single"
---

<!-- 💡 パスの先頭に /starknote を追加 -->
<link href="/starknote/_pagefind/pagefind-ui.css" rel="stylesheet">
<script src="/starknote/_pagefind/pagefind-ui.js"></script>

<!-- 検索ボックスが表示される要素 -->
<div id="search"></div>

<script>
    window.addEventListener('DOMContentLoaded', (event) => {
        new PagefindUI({ 
            element: "#search", 
            showImages: false,
            bundlePath: "/starknote/_pagefind/",
            // 👇 以下の2行を追加
            showSubResults: true,       // 該当する複数のセクションを表示
            highlightParam: "h",        // URLにハイライト用のパラメータを付与
            translations: {
                placeholder: "キーワードを入力...",
                clear_search: "消去",
                load_more: "もっと見る",
                search_label: "このサイトを検索",
                filters_label: "フィルター",
                zero_results: "[WARN] 「[SEARCH_TERM]」を検出できませんでした。",
                many_results: "[SEARCH_COUNT] 件の検索結果が見つかりました。",
                one_result: "[SEARCH_COUNT] 件の検索結果が見つかりました。",
                alt_search: "「[SEARCH_TERM]」の代わりに「[ORIGINAL_TERM]」を検索しています。",
                search_suggestion: "もしかして: [DERIVED_TERM]",
                searching: "検索中..."
            }
        });
    });
</script>
```


`./.git/info/exclude` の中に以下を追記．

```
static/_pagefind
```

ローカルでの実行は以下．
```
# 1. ローカルに一度静的ファイルを出力する
$ hugo

# 2. 生成されたファイルを元に、static/ フォルダ内へPagefindのデータを書き出す
$ npx -y pagefind --site public --output-subdir ../static/_pagefind

$ hugo server または $ hugo server -D
```


## サイト編集
`config.toml`が設定ファイルになる．サイトのタイトルなどの設定を行う．  
ブログの記事は`content/post/`の中にマークダウンファイル`○○.md`を作成する．  

ブログの記事のhtmlは`themes\hugo-notepadium\layouts\_default\single.html`である．  
スタイルファイルは`themes\hugo-notepadium\assets\css\style.css`である．  
ダークモードは`themes\hugo-notepadium\assets\css\dark-style.css`．  

画像は`static\image`に入れる．  
こうすると，  
```
![This is a image](image/asusn-ctf-2/im13.png)  
```
![This is a image](image/asusn-ctf-2/im13.png)  
で表示できる．ただし，GitHub Pages でサイトを公開する場合は，画像のパスに注意が必要．  
githubで画像を開き，画像を右クリックして新しいタブで画像を開くで，開いたURLをそのままコピーするのが確実である．  
```
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/asusn-ctf-2/im13.png)
```
![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/asusn-ctf-2/im13.png)


### フォント変更
参考：[GitHub PagesとHugoを使ってブログを作ってみた](https://natsu-dev.github.io/posts/2021-02-28/)

`themes\hugo-notepadium\assets\css\style.css`で変更．
```
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@100..900&display=swap');
html {
  font-family: 'Noto Sans JP', sans-serif;
  font-size: 15px;
  font-weight: 400;
  line-height: 1.5;
}

code,
tt,
kbd,
samp {
  font-family: "Lucida Console";
  font-weight: 400;
  font-size: 85%;
}
```

### 見出しの調整
```
h2 {
	font-size: 1.2em;
	border-left: solid 5px #D0D0D0;
	padding: 0.1em 0.5em;
}

h3 {
	font-size: 1.1em;
	border-left: solid 5px #E0E0E0;
	padding: 0.1em 0.5em;
}

h4 {
	font-size: 1.0em;
	border-left: solid 5px #F0F0F0;
	padding: 0.1em 0.5em;
}
```

### Notepadiumのバグ修正
ブログ記事の一番下のカテゴリとタグのリンクがおかしかったので，以下で修正．  
`print "/categories/"`の/を削除して，`print "categories/"`にする．  
`print "/tags/"`の/を削除して，`print "tags/"`にする．  
相対パスであるべきところが絶対パスになっていて，正しくなかったので．
```
{{- if or .Params.categories .Params.tags -}}
<section class="article labels">
    {{- range .Params.categories -}}
      {{- $category := replace . "#" "%23" -}}
      {{- $category = replace $category "." "%2e" -}}
      {{- $url := print "categories/" ($category | urlize) "/" -}}
      <a class="category" href="{{- $url | relLangURL -}}">{{- . -}}</a>
    {{- end -}}
    {{- range .Params.tags -}}
      {{- $tag := replace . "#" "%23" -}}
      {{- $tag = replace $tag "." "%2e" -}}
      {{- $url := print "tags/" ($tag | urlize) "/" -}}
      <a class="tag" href="{{- $url | relLangURL -}}">{{- . -}}</a>
    {{- end -}}
</section>
{{- end -}}
```

### 目次の追加
`themes\hugo-notepadium\layouts\_default\single.html`  
`themes\hugo-notepadium\assets\css\style.css`  
を修正．画面幅が1600px以下の場合は，最初は目次を非表示にするようにした．

### 横幅を広げた
```
#content {
  display: block;
  margin: 0 auto;
  max-width: 1000px;
}
```

https://sat8bit.github.io/posts/hugo-with-github-pages/

### コードブロックにキャプションを付けられるショーコードの作成
参考：  
・ https://maku77.github.io/p/gxk6qat/  
・ https://discourse.gohugo.io/t/topic/31065  

{{< code lang="" title="themes\hugo-notepadium\layouts\shortcodes\code.html" hl_lines="">}}
{{ $title := .Get "title" }}
{{ $lang := or (.Get "lang") "" }}
{{ $line := or (.Get "hl_lines") "" }}
<figure class="xCodeBlock">
{{ with $title }}<figcaption class="xCodeBlock_title">{{ . }}</figcaption>{{ end }}
<div class="xCodeBlock_code">{{ highlight (trim .Inner "\r\n") $lang (dict "hl_lines" $line) }}</div>
</figure>
{{< /code >}}

{{< code lang="css" title="themes\hugo-notepadium\assets\css\style.css" hl_lines="">}}
figure{
  line-height: 18px;
  letter-spacing: 0;
} 
figcaption {
  font-size: 12px;
  line-height: 22px;
  padding: 3px 6px 0 6px;;
  margin-left: 13px;
  letter-spacing: 0;
  display: inline-block;
  border-radius: 0 0 3px 3px;
  background-color: rgb(253, 226, 226);
}
.xCodeBlock {
  margin: 10px 0;
  border-radius: 3px;
  background-color: #f9f6f6;
}
.codeblock {
  margin: 0;
}
pre {
  margin: 0px;
}
{{< /code >}}

{{< code lang="" title="themes\hugo-notepadium\assets\css\dark-style.css" hl_lines="">}}
figcaption {
  background-color: rgb(94, 94, 94);
}
.xCodeBlock {
  background-color: #282a36;
}
{{< /code >}}

を追加．以下のようにすることで，タイトルを付けられる．
<pre><code>
&#123;&#123;&#60; code lang="" title="" hl_lines="" &#125;&#125;&#62;

&#123;&#123;&#60; /code &#125;&#125;&#62;
</code></pre>


## GitHub Pages
先に，レポジトリを作成し，作ったサイト`example\`の中を全てpushしておく．  
その後，GitHubリポジトリの「Settings」の「Pages」でBuild and deploymentのSourceを、GitHub Actionsに設定．  
"Configure" ボタンを押すと、ワークフローの作成画面になります。特に変更せず、そのまま "main" ブランチにコミットします。  

GitHubリポジトリの「Settings」の「Pages」でVisit site でサイトへ行ける．

https://note.com/hideki_ikemoto/n/n97a61f1ead6b

### GitHubコマンド
```
$ git init
$ git add .
$ git commit -m "message"
($ git status) しなくてよい．確認のため．
$ git push
```
リモートだけ変更した場合，`git pull`でローカルに，リモートの更新を反映できる．

### Githubへの除外設定

`./.git/info/exclude` の中に記載のフォルダ・ファイルはGithubへpushされない．