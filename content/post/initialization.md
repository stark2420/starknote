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

## サイト編集
`config.toml`が設定ファイルになる．サイトのタイトルなどの設定を行う．  
ブログの記事は`content/post/`の中にマークダウンファイル`○○.md`を作成する．  

ブログの記事のhtmlは`themes\hugo-notepadium\layouts\_default\single.html`である．  
スタイルファイルは`themes\hugo-notepadium\assets\css\style.css`である．  
ダークモードは`themes\hugo-notepadium\assets\css\dark-style.css`．  

画像は`static\image`に入れる．  
こうすると，  
```
![This is a image](/image/asusn-ctf-2/im13.png)  
```
![This is a image](/image/asusn-ctf-2/im13.png)  
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
