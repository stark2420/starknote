+++
author = "Hugo Authors"
title = "SWIMMER OSINT CTF 2026 Writeup"
date = "2026-01-18"
description = ""
tags = [
    "CTF",
    "writeup",
    "swimmer",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/cover.png"
+++

2026/1/17 12:10(JST) - 2026/1/18 0:10(JST)で開催された SWIMMER OSINT CTF 2026 に参加した．

<!--more-->
チームで参加し，結果は，3514 点で 688 チーム中 86 位だった．

最後の問題以外は全て解けた．解いた問題についてまとめておく．

https://swimmer.diverctf.org/challenges


<!-- ## briefing

### 00_agreement (10pt / 687 solves)

### 01_flag_format (10pt / 672 solves)

### 02_social_media_rule (10pt / 668 solves)

### 03_contact (10pt / 667 solves)

### 04_share (10pt / 665 solves)

### 05_AI (10pt / 664 solves)

### 06_ask_for_admin (10pt / 663 solves)

### 07_research_order (10pt / 654 solves) -->

## tgt_rain

### rain_01_social (100pt / 554 solves)[easy]
> rain は2026年時点でXのアカウントを所持していたようです。  
我々は、この人物の投稿のスクリーンショットを入手しました。  
スクリーンショットからアカウントを特定し、このアカウントのID（スクリーンネーム）を解答してください。   
例えば、@gov_online が対象のアカウントの場合、Flag は SWIMMER{@gov_online} となります。  
rain_01_social.jpg  

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/rain_01_social.jpg)

スクリーンショットに写っている「これわりと近所だったから見に行ったな」で検索すると出てきた．

https://x.com/bruto_rain

`SWIMMER{@bruto_rain}`

### rain_02_region (316pt / 154 solves)[medium]
> rain は自身のブログに趣味の投稿を行っていたようです。 投稿に用いられている写真のほとんどがこの人物の撮影したものではないフェイクのようですが、1枚だけ、実際にこの人物が撮影したと考えられる写真が存在します。 その写真を特定し、撮影地を地図上で解答してください。  

人力でフェイクかどうかを判別していった．

最終的に，以下が正しそうと判断．

![This is a image](https://brutorain.wordpress.com/wp-content/uploads/2025/12/keihan-2.png?w=1440)

星ケ丘の構内図より，トイレの位置から牧方市方面から撮影しているとわかる．

https://www.keihan.co.jp/traffic/station/215/info.html

よって，以下のあたりを提出すると正解であった．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im10.png)

### rain_03_source1 (256pt / 177 solves)[medium]
> rain は、目を引くタイトルの記事を書くことで閲覧数が多くなると考えたようです。  
この人物はこれをきっかけとして偽情報の作成・流布にのめり込んでしまったと考えられます。  
この人物が一番最初に投稿した偽記事では、ある画像に全く関係ないキャプションが付けられています。 本来の出典を探し、この画像がどこの市区町村にまつわる資料に掲載されたものか解答してください。  
都道府県名を含む必要はありません。  
例えば、平塚市 の資料に掲載されていた場合、Flagは SWIMMER{平塚市} となります。  

[国立国会図書館デジタルコレクション](https://dl.ndl.go.jp/) で画像にある「池過濾ノ中事工」を検索．

豊橋市水道誌に同じ画像が記載されていた．

https://dl.ndl.go.jp/pid/1225183/1/28?keyword=%E6%B1%A0%E9%81%8E%E6%BF%BE%E3%83%8E%E4%B8%AD%E4%BA%8B%E5%B7%A5

`SWIMMER{豊橋市}`

### rain_04_source2 (100pt / 414 solves)[easy]
> rain が2番目に作成した偽記事には、「rainが未公開の情報を見つけた」ということが書かれているようです。しかし、これは虚偽だと思われます。  
この人物の嘘を暴くには、正確な出典を探す必要があります。  
この画像の出典となる古い書籍は電子化されており、詳細が参照できるはずです。  
その資料のデジタルオブジェクト識別子（DOI）を解答してください。  
例えば、答えが 12.34567/890123 の場合、Flagは SWIMMER{12.34567/890123} となります。  

ブログはXのアカウントのプロフィールに記載されている． https://brutorain.wordpress.com/

[2番目に作成した偽記事](https://brutorain.wordpress.com/2025/12/21/%e8%aa%b0%e3%82%82%e7%9f%a5%e3%82%89%e3%81%aa%e3%81%84%e7%9c%9f%e5%ae%9f%e3%82%92%e7%99%ba%e8%a6%8b/)の
画像を画像検索すると以下のサイトがヒットした．

https://commons.wikimedia.org/wiki/File:National_Diet_Building_Competition_Submission_Watanabe_Fukuzo.jpg

> Source	http://dl.ndl.go.jp/info:ndljp/pid/967480/7

出典に飛ぶと，識別子（DOI）10.11501/967480 だとわかった．

`SWIMMER{10.11501/967480}`

### rain_05_date (100pt / 283 solves)[medium]
> rain は2025年12月25日 (JST)、ある場所に来たことをX上で投稿しています。  
しかし、この画像は2025年の別の日に撮影された写真のようで、事実とは異なるようです。 この写真が本当に撮影された日を YYYY/MM/DD の形式で解答してください。  
ただし、撮影日は日本時間（JST）を基準とします。 例えば、答えが2025年6月8日の場合、Flagは SWIMMER{2025/06/08} となります。  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">さっき関西着いた<br>週末までこっち居ます <a href="https://t.co/7Am8zjo2x4">pic.twitter.com/7Am8zjo2x4</a></p>&mdash; brutorain (@bruto_rain) <a href="https://twitter.com/bruto_rain/status/2004085702469075019?ref_src=twsrc%5Etfw">December 25, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

この画像は大阪城ホールであるとわかる．

画像の中央辺りに看板が写っている．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im11.png)

画像検索すると，画像には「10000 Freude」というテキストが記載されていると判明．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im12.png)

「大阪城ホール 過去イベント 10000」で検索すると，12月7日（日）サントリー1万人の第九のイベントが出てきた．

https://www.suntory.co.jp/culture-sports/daiku/

`SWIMMER{2025/12/07}`

### rain_06_ai (100pt / 248 solves)[hard]
> rain のブログには、東京駅の画像があります。しかし、これは実際に撮影したものではなく、AIで作成された画像のようです。  
この画像が何というツールで作成されたのか特定し、解答してください。  
例えば、答えが Adobe Firefly の場合、Flagは SWIMMER{Adobe Firefly} となります。  

https://brutorain.wordpress.com/2026/01/03/%e6%97%a5%e5%b8%b8%e3%82%92%e8%a8%98%e9%8c%b2%e3%81%99%e3%82%8b/

`tokyo_0003.jpeg`をダウンロードする．

画像のプロパティをみると，コメントに以下が書かれていた．
```
{"aigc_info":{"aigc_label_type":0,"source_info":"dreamina"},"data":{"os":"web","product":"dreamina","exportType":"generation","pictureId":"0"},"trace_info":
```

よって，Dreaminaだとわかった．

`SWIMMER{Dreamina}`

## tgt_debeyohiru

### debeyohiru_01_social (100pt / 429 solves)[medium]
> 調査対象の人物はソフトウェアエンジニアで、2026年以降には debeyohiru というIDでの活動が確認されています。  
この人物がこのIDでの活動を開始したのは2026年1月のようです。この人物がこれ以前に使用していたIDを特定できないでしょうか？  
2026年1月時点で、noteというサービスに古いIDのアカウントが残存しているようです。  
このアカウントのIDを解答してください。  
例えば、digital_jpn_gc が対象のアカウントの場合、 Flag は SWIMMER{digital_jpn_gc} となります。  

「debeyohiru」で検索するとBlueskyのアカウント`@debeyohiru.bsky.social`を発見．

投稿の中に，noteのスクショがあった．

<blockquote class="bluesky-embed" data-bluesky-uri="at://did:plc:wgxr2fnwbyylu2bj3cx3lrfm/app.bsky.feed.post/3mavlx6v3ak2c" data-bluesky-cid="bafyreifnzg6dzqzanxhjx6xrzyut2ymlezt6sn6b2z45k2ntppw5er5okq" data-bluesky-embed-color-mode="system"><p lang="ja">これも全然反響なかったな。正直寂しい。<br><br><a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mavlx6v3ak2c?ref_src=embed">[image or embed]</a></p>&mdash; Debeyohiru (<a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm?ref_src=embed">@debeyohiru.bsky.social</a>) <a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mavlx6v3ak2c?ref_src=embed">2025年12月27日 0:23</a></blockquote><script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>

noteというサービスで検索すると出てきた．

https://note.com/furaigo5

`SWIMMER{furaigo5}`

### debeyohiru_02_profile (100pt / 373 solves)[easy]
> debeyohiru は2026年1月時点で求職中で、プロフィールページをウェブ上に公開していたようです。  
このページを探り出し、そのURLを解答してください。  
例えば、 https://example.com/foobar が対象のページの場合、 Flagは SWIMMER{https://example.com/foobar} となります。  

「furaigo5」で検索すると出てきた．

https://furaigo5.github.io/profile/

`SWIMMER{https://furaigo5.github.io/profile/}`

### debeyohiru_03_email (100pt / 371 solves)[easy]
> debeyohiru が2026年現在、普段使っているメールアドレスが知りたいです。  
この人物が現在使用中とおぼしきメールアドレスを探り出し、解答してください。  
例えば、メールアドレスがfoobar@example.comの場合、Flagは SWIMMER{foobar@example.com} となります。  
警告: このアドレスに対してメールを送信してはなりません。解答に際して、メールを送る必要は一切ありません。  

[プロフィールページ](https://furaigo5.github.io/profile/)の下の方に記載されていた．

`SWIMMER{furaigo5.onionsoup@gmail.com}`

### debeyohiru_04_meal (194pt / 198 solves)[medium]
> debeyohiru はある料理が好物で、よく食べているようです。 直近では2026年1月10日の夕食にその料理を食べたことが確認されています。 この人物がこの日の夕食に食べたメニューを特定し、店舗のメニューに記載された名前で解答してください。  
例えば、マクドナルドでビッグマックを食べたことが特定できた場合、Flagは SWIMMER{ビッグマック} になります。 メニュー名の表記は店舗のメニューの 日本語 表記に準拠してください。  

Blueskyの投稿の中に以下がある．

<blockquote class="bluesky-embed" data-bluesky-uri="at://did:plc:wgxr2fnwbyylu2bj3cx3lrfm/app.bsky.feed.post/3mazue5yqdc2n" data-bluesky-cid="bafyreibsskpiqzk4h6yghewd2ay3fjkn4bqgbiv3hlxk3nwfjj4y4nuedu" data-bluesky-embed-color-mode="system"><p lang="ja">早めの夕食<br><br><a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mazue5yqdc2n?ref_src=embed">[image or embed]</a></p>&mdash; Debeyohiru (<a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm?ref_src=embed">@debeyohiru.bsky.social</a>) <a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mazue5yqdc2n?ref_src=embed">2025年12月28日 17:04</a></blockquote><script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>

<blockquote class="bluesky-embed" data-bluesky-uri="at://did:plc:wgxr2fnwbyylu2bj3cx3lrfm/app.bsky.feed.post/3mc2utbra622w" data-bluesky-cid="bafyreicezhzn4b4pkv5wxhxngmt27wqx4y2ry3tcyzsjqrahqk5i5gltgi" data-bluesky-embed-color-mode="system"><p lang="ja">今宵も来たぞ。今日は単品でいいや</p>&mdash; Debeyohiru (<a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm?ref_src=embed">@debeyohiru.bsky.social</a>) <a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mc2utbra622w?ref_src=embed">2026年1月10日 20:11</a></blockquote><script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>

ポムの樹で食べたことはわかる．

また，以下の投稿より，

<blockquote class="bluesky-embed" data-bluesky-uri="at://did:plc:wgxr2fnwbyylu2bj3cx3lrfm/app.bsky.feed.post/3mbqqz6vd5225" data-bluesky-cid="bafyreicq5sbv5ulqcxcqwabhqdm7qjewd66p3fkcrzyk36rzpsow722aly" data-bluesky-embed-color-mode="system"><p lang="ja">新春モザイクアートを観測<br><br><a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mbqqz6vd5225?ref_src=embed">[image or embed]</a></p>&mdash; Debeyohiru (<a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm?ref_src=embed">@debeyohiru.bsky.social</a>) <a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mbqqz6vd5225?ref_src=embed">2026年1月6日 19:36</a></blockquote><script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>

「新春 モザイクアート 駅」と検索すると，渋谷駅であることがわかる．

https://x.com/REALITY_app/status/2008071246639235175

渋谷駅周辺のポムの樹は1店舗しかない．ポムの樹渋谷スペイン坂店のクチコミにふらいごが書き込みをしているのを発見．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im08.png)

クチコミから，冬限定メニューということがわかる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im09.png)

調べると，豚肉とリンゴのホタテトマトクリームオムライスだとわかる．

https://www.pomunoki.com/img/release/release-281.pdf#zoom=100

`SWIMMER{豚肉とリンゴのホタテトマトクリームオムライス}`

### debeyohiru_05_hidden1 (100pt / 276 solves)[medium]
> debeyohiru の本名が知りたいです。  
この人物の実名と考えられるものを解答してください。 ローマ字（アルファベット）表記で入力してください。漢字の表記を考慮する必要はありません。  
例えば、Sanae Takaichiが実名の場合、Flagは SWIMMER{Sanae Takaichi} となります。  

プロフィールページは既に debeyohiru_02_profile で特定済み． https://furaigo5.github.io/profile/

このページの`script.js`をみるとコメントアウトで`@author Gotanno Tsubasa`と書かれていた．

```
/**
 * @fileoverview furaigo5 Workspace - メインスクリプト
 * ナビゲーションのスムーズスクロールとモバイルメニューの制御を担当
 * @author Gotanno Tsubasa
 */

/**
 * DOMContentLoadedイベントで初期化処理を実行
 */
document.addEventListener('DOMContentLoaded', () => {
    initSmoothScroll();
    initMobileMenu();
});

```

`SWIMMER{Gotanno Tsubasa}`

### debeyohiru_06_hidden2 (100pt / 258 solves)[medium]
> debeyohiru が 2025年12月 時点で使用していたと考えられるスマートフォンの機種が知りたいです。  
なお、複数の端末を使用していたと考えられる場合は、アンダーバー（_）で繋いで全てを解答してください。この場合、回答順序は問いません。  
また、メーカー名は不要です。  
例えば、Xperia 10 VIIとiPhone 17を使用していた場合、 Flagは SWIMMER{Xperia 10 VII_iPhone 17} となります。  

Blueskyのアカウントで 2026/01/10 17:31 にスマホを変えたとあるので，過去のウェブサイトをみる必要がある．

<blockquote class="bluesky-embed" data-bluesky-uri="at://did:plc:wgxr2fnwbyylu2bj3cx3lrfm/app.bsky.feed.post/3mc2lwrkq2s2c" data-bluesky-cid="bafyreicpthxlxviiqjhad4nrzmgonxqcf6bhpwxcqg5szq23pwdgbx5zje" data-bluesky-embed-color-mode="system"><p lang="ja">初売りでスマホ買い替えたから、プロフィールも更新しておこう</p>&mdash; Debeyohiru (<a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm?ref_src=embed">@debeyohiru.bsky.social</a>) <a href="https://bsky.app/profile/did:plc:wgxr2fnwbyylu2bj3cx3lrfm/post/3mc2lwrkq2s2c?ref_src=embed">2026年1月10日 17:31</a></blockquote><script async src="https://embed.bsky.app/static/embed.js" charset="utf-8"></script>

まず， https://web.archive.org/ を確認したが存在せず．

次に， https://archive.md/ を確認したところ，2026年1月2日 09:08:41 UTCのアーカイブを発見した．

```
ガジェット
スマートフォン: Pixel 8 Pro / iPhone 13 mini（検証用）
タブレット: iPad Pro 11インチ（資料閲覧・手書きメモ）
その他: Steam Deck（ゲーム兼Linuxいじり用）、Raspberry Pi 4（自宅サーバー）
```

現在は以下であった．
```
ガジェット
スマートフォン: Xiaomi 15 Ultra / iPhone 13 mini（検証用）
タブレット: iPad Pro 11インチ（資料閲覧・手書きメモ）
その他: Steam Deck（ゲーム兼Linuxいじり用）、Raspberry Pi 4（自宅サーバー）
```

よって，flagは以下である．

`SWIMMER{Pixel 8 Pro_iPhone 13 mini}`

## tgt_lilica

### lilica_01_social (100pt / 483 solves)[easy]
> lilica は2026年時点でクリエイターとして活動しており、「黄昏ブロッサムリリカ」と名乗っていたことが分かっています。 また、Xのアカウントを持っていたことが確認されています。  
そのXアカウントのID（スクリーンネーム）を解答してください。  
例えば、@gov_online が対象のアカウントの場合、Flag は SWIMMER{@gov_online} となります。  

「黄昏ブロッサムリリカ」と検索するとXのアカウント`@twilight_lilica`を発見．

https://x.com/twilight_lilica

`SWIMMER{@twilight_lilica}`

### lilica_02_virtual_identity (100pt / 338 solves)[easy]
> lilica はVRにも関心があるようで、未来でもVR関連の活動がわずかながら確認されています。  
lilica が2026年時点で使っていたVRChatのユーザーIDを特定し、解答してください。  
VRChatのユーザー情報はブラウザからも確認できます。  
例えば、対象アカウントのURLが https://vrchat.com/home/user/usr_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx の場合、 Flagは SWIMMER{usr_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx} となります。  

https://vrchat.com/home で「黄昏ブロッサムリリカ」と検索すると出てきた．

https://vrchat.com/home/user/usr_b103fac6-8341-4b89-a606-920092e75e43

`SWIMMER{usr_b103fac6-8341-4b89-a606-920092e75e43}`

### lilica_03_virtual_world (100pt / 388 solves)[easy]
> lilica はVRChatでの活動をSNSに投稿していたようです。  
2025年11月9日（日本時間）に投稿された画像にはある「ワールド」が写っているようです。このワールドのIDを解答してください。  
VRChatのワールド情報はブラウザからも確認できます。  
例えば、対象のワールドのURLが https://vrchat.com/home/world/wrld_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/info だった場合、  
Flagは SWIMMER{wrld_xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx} となります。  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">見ず知らずの人と一対一で話すの楽しいね <a href="https://t.co/J0NPIv3Fai">pic.twitter.com/J0NPIv3Fai</a></p>&mdash; 黄昏ブロッサムリリカ (@twilight_lilica) <a href="https://twitter.com/twilight_lilica/status/1987359447111639464?ref_src=twsrc%5Etfw">November 9, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

画像検索すると「NAGiSA」だとわかる．

https://prtimes.jp/main/html/rd/p/000000003.000167131.html

https://vrchat.com/home で「NAGiSA」と検索すると出てきた．

日本人向け 1対1お話しワールド NAGiSA ［JP］

https://vrchat.com/home/world/wrld_1b94e327-036b-4d09-81be-e898d71f02cb/info

`SWIMMER{wrld_1b94e327-036b-4d09-81be-e898d71f02cb}`

### lilica_04_domain (100pt / 443 solves)[easy]
> lilica は個人のWebサイトを運営していたようです。  
このWebサイトのドメイン名が取得された日付が知りたいです。YYYY/MM/DD の形式で解答してください。  
例えば、2026年1月2日 がドメイン取得日の場合、Flagは SWIMMER{2026/01/02} となります。  

「黄昏ブロッサムリリカ」と検索すると出てきた．

https://www.twilight-lilica.com/

以下のサイトで，ドメイン取得日を調べると，2025/10/05だとわかった．

https://tech-unlimited.com/whois.html

```
Domain Name: twilight-lilica.com
Registry Domain ID: 3026076793_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.discount-domain.com
Registrar URL: http://www.onamae.com
Updated Date: 2025-10-05T16:08:51Z
Creation Date: 2025-10-05T07:08:47Z
```

`SWIMMER{2025/10/05}`

### lilica_05_hosting (100pt / 333 solves)[medium]
> lilica が運営している個人サイトは、あるホスティングサービス上に存在しています。  
このサイトは規約違反を行っているわけではないので何かできるわけではありませんが…… 念の為、連絡先を知っておきたいです。  
このホスティングサービスの規約違反通報メールアドレスを解答してください。  
例えば、メールアドレスが foobar@example.com の場合、Flagは SWIMMER{foobar@example.com} となります。  
警告 : このアドレスに対してメールを送信してはなりません。 解答に際して、メールを送る必要は一切ありません。  

DNSで「正引き」と「逆引き」を行う．

```
$ nslookup twilight-lilica.com
Server:         10.255.255.254
Address:        10.255.255.254#53

Non-authoritative answer:
Name:   twilight-lilica.com
Address: 45.77.129.141


$ nslookup 45.77.129.141
141.129.77.45.in-addr.arpa      name = 45.77.129.141.vultrusercontent.com.

Authoritative answers can be found from:
```

ホスティングサービスは，Vultr(vultrusercontent.com) であるとわかる．

Vultrの規約違反通報メールアドレスは`abuse@vultr.com`である．
https://www.vultr.com/legal/use-policy/

`SWIMMER{abuse@vultr.com}`

### lilica_06_name (100pt / 255 solves)[hard]
> lilica の本名が知りたいです。  
この人物の実名と考えられるものを解答してください。  
ローマ字（アルファベット）表記で入力してください。漢字の表記を考慮する必要はありません。  
例えば、Sanae Takaichi が実名の場合、Flagは SWIMMER{Sanae Takaichi} となります。  

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">面白くなっちゃって<br>とりあえず髪留めこねてみたんだけど<br><br>これどうやったらVRCのアクセサリにできるんだろう？<a href="https://t.co/TR5T7GFce8">https://t.co/TR5T7GFce8</a> <a href="https://t.co/7IlfzAeqwc">pic.twitter.com/7IlfzAeqwc</a></p>&mdash; 黄昏ブロッサムリリカ (@twilight_lilica) <a href="https://twitter.com/twilight_lilica/status/2000551415039791278?ref_src=twsrc%5Etfw">December 15, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

このツイートのリンクから，`simple_hair_pin.fbx`のファイルをダウンロードし，適当にメモ帳で開くと，文字化けしているが，一部は読める．

```
Kaydara FBX Binary      
                      FBXHeaderExtensiont                     FBXHeaderVersionI・  ・                     
FBXVersionI  ﾈ                     EncryptionTypeI    )                      CreationTimeStamp                     VersionI・  9                    YearI・  \                    MonthI
   }                    DayI   ・                    HourI   ﾃ                    MinuteI   ・                    SecondI
                       
MillisecondIj                           r             )        CreatorS$   FBX SDK/FBX Plugins version 2020.3.4ﾘ                      
OtherFlagsｿ                    TCDefinitionI                            	             '       	SceneInfoS   GlobalInfo SceneInfoS   UserDataK             
       TypeS   UserDatap                     VersionId   ｬ                      MetaDataｶ                     VersionId   ﾙ                    TitleS                         SubjectS    "                    AuthorS    H                    KeywordsS    n                    RevisionS    ・                     CommentS                             ・                      Properties70j                    PS
   DocumentUrlS    KStringS   UrlS    SQ   C:\Users\shiharu_nanaogi\Documents\modeling\vrc_test\hair_pin\simple_hair_pin.fbx             ・       PS   SrcDocumentUrlS    KStringS   UrlS    SQ   C:\Users\shiharu_nanaogi\Documents\modeling\vrc_test\hair_pin\simple_hair_pin.fbxD             $       PS   OriginalS   CompoundS    S    ・             :       PS   Original|ApplicationVendorS    KStringS    S    S                  P       PS   Original|ApplicationNameS    KStringS    S    S   Maxon Cinema 4D 2025.3.2W              ;       PS   Original|ApplicationVersionS    KStringS    S    S    ｾ              M       PS   Original|DateTime_GMTS   DateTimeS    S    S   15/11/2025 00:00:00.000	             1       PS   Original|FileNameS    KStringS    S    S    H             %       PS
```

この中に，パス` C:\Users\shiharu_nanaogi\Documents\modeling\vrc_test\hair_pin\simple_hair_pin.fbx`があり，本名があった．

`SWIMMER{shiharu_nanaogi}`

### lilica_07_work (168pt / 206 solves)[medium]
> lilica は、Xとは別のSNSにもアカウントを所持しているようです。   
このアカウントの情報から職場の最寄り駅として推定されるものを、東京メトロの駅名 で解答してください。  
例えば、推定される職場の最寄り駅が 新宿三丁目駅 の場合、Flagは SWIMMER{新宿三丁目} となります。  

インスタグラムで本名を検索すると出てきた．

目黒川の写真などが投稿されているので，中目黒を試すと正解だった．

`SWIMMER{中目黒}`
 
## research_2025

### cx (100pt / 523 solves)[easy]
> 2025年春、かつて香港に存在していた空港の100周年を記念して、特別なフライトが実施されたようです。  
このフライトの便名を解答してください。  
例えば、便名が JL2026 のとき、Flag は SWIMMER{JL2026} となります。  

ChatGPTですぐ出てきたので，確認して提出．

`SWIMMER{CX8100}`

### pilot (100pt / 447 solves)[easy]
> cx の問題で示されたフライト中、添付画像の席に座っていた人物の名前を英語で解答してください。  
（なお、添付画像は座席を示すためのものであり、当該フライトの実際の写真ではありません）  
例えば、人物の名前が John Doe のとき、Flag は SWIMMER{John Doe} となります。  

ChatGPTですぐ出てきたので，確認して提出．

`SWIMMER{Adrian Scott}`

### flag_on_the_don (100pt / 345 solves)[easy]
> 2025年8月28日、群馬県で「太鼓の達人」を利用したイベントが開催されました。  
その会場となった建物はどこでしょうか。 OpenStreetMapのウェイ（way）番号で解答してください。  
例えば、建物が 123456789 というway番号であれば、Flagは SWIMMER{123456789} となります。  

```
"群馬県" "太鼓の達人" 2025年8月28日
```
とでも検索すれば，高齢者向けeスポーツ大会開催 群馬県渋川市のイベントが見つかる．

このイベントについて調べていくと，以下のpdf資料を発見．

https://www.city.shibukawa.lg.jp/manage/contents/upload/68870b94eeb0d.pdf

場所は，渋川市民会館だとわかった．

OpenStreetMapで渋川市民会館を調べると，ウェイ: 渋川市民会館 (628293186)とヒット．

https://www.openstreetmap.org/way/628293186

`SWIMMER{628293186}`

### obsolete (100pt / 349 solves)[easy]
> 2025年11月、「台湾有事」を巡って日中関係が悪化しました。  
その中で、在日本中国大使館のSNSアカウントは国連憲章の「敵国条項」について言及する投稿を行いました。  
これに対し、日本の外務省は「その条項は 1995年の国連決議 によって死文化（obsolete）している」と反論したことが報じられています。  
さて、この決議において、投票を 棄権 した国がいくつかあります。それはどこの国か、解答してください。  
決議に関する公式記録に記載されている国名をアンダーバー (_) で区切ったもの（順不同）をFlagとします。  
たとえば、UNITED STATES（アメリカ合衆国）とRUSSIAN FEDERATION（ロシア連邦）と記載されていた場合、Flagは SWIMMER{UNITED STATES_RUSSIAN FEDERATION} となります。

調べた結果，以下のサイトを発見．

https://press.un.org/en/1995/19951211.ga9031.html

> 3 abstentions (Cuba, Democratic People's Republic of Korea and Libya).

この三つの国名だとわかった．次に，公式記録に記載されている国名を探すと，以下のサイトを発見．

https://digitallibrary.un.org/record/284135

- DEMOCRATIC PEOPLE'S REPUBLIC OF KOREA
- CUBA
- LIBYAN ARAB JAMAHIRIYA

よって，以下が得られた．

`SWIMMER{DEMOCRATIC PEOPLE'S REPUBLIC OF KOREA_CUBA_LIBYAN ARAB JAMAHIRIYA}`

### truck (100pt / 257 solves)[easy]
> あるトラックが 8773X3XQ+JWQ を2025年6月21日 13:39:54（現地時間）ごろに通過しました。このトラックの車体に書かれていたFQDNを解答してください。  
たとえば、www.google.com の場合、Flagは SWIMMER{www.google.com} となります。  

Google Mapで`8773X3XQ+JWQ`を検索すると，"高さ制限 11フート8インチ橋 The Can Opener Bridge of Durham" が表示された．

この橋について調べると，以下のサイトにたどり着いた．

https://11foot8.com/about/the-documentary/  
> For the past ten years, Jürgen Henn has filmed over-height trucks crashing into the 11foot8 train bridge affectionately nicknamed the “Can Opener.” In that time, millions have viewed the crashes online. Regional, national, and international news organizations have dined out on the story and the goofy crash reels. But why do motorists continue to crash despite the many warnings, sensors, and signs? And what is it about these crashes that holds our attention? In this piece, we look for the humanity in human error.

この鉄道橋は，高さ制限が低いため，多くのトラックが屋根を削られる事故が起きたことで有名になり，専用のウェブサイトが運営されているらしい．

2025年6月21日の記事は以下である．

https://11foot8.com/moving-truck-runs-red-light-and-scrapes-the-11foot88-bridge/

この動画から，トラックの車体に書かれていたFQDNは`www.MiracleMoversUSA.com`だとわかる．

`SWIMMER{www.MiracleMoversUSA.com}`

### debris (100pt / 228 solves)[medium]
> 2025年12月8日、日本のあるテレビ番組で、高速道路のパトロール隊への密着取材の様子が放映されました。  
パトロール隊の108号車は、路上に落下していたトン袋の回収を命じられました。トン袋が最初に落下していた地点はどこでしょうか？  
マップ上で示して解答してください。  
content warning : 交通事故の映像が含まれる映像を視聴する必要があります（人が巻き込まれるシーンはありません）。  

以下のサイトに，テレビ番組の内容がまとめられていた．

https://mezamashi.media/articles/-/232641

「上りの護国寺合流先、音羽」という情報が得られたので，Google Mapで調べる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im05.png)

あとは，首都高速で該当の場所を探すと，サイトの画像と同じ場所が見つかった．

![This is a image](https://mezamashi.ismcdn.jp/mwimgs/7/5/1416/img_75008fbf85dc66b1c2942fc1d8a69d5a650413.jpg)

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im06.png)

2回目で正しい場所に提出できた．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im07.png)

### lattice_tower (100pt / 286 solves)[medium]
> 2025年12月、日本で地震が発生し、ある通信施設の鉄塔が被害を受けました。  
主要ニュースで報じられた名称以外に、このビルには通信施設としての別の公式な名前があるようです。  
その正式名称を日本語で解答してください。  
ただし、解答にアルファベットや数字が含まれている場合はそのまま答えてください（ただし、 半角英数字 にしてください）。  
また、そのビルを保有している会社名は含まず、ビル名だけがFlagになります。  
例えば、「SWIMMER OSINT CTF株式会社　台場SWIMMER2026ビル」だった場合、Flagは SWIMMER{台場SWIMMER2026ビル} となります。  

調べるとNTT青森八戸ビルであることがわかるが，これは正式名称ではないらしい．

https://www.ntt-east.co.jp/release/detail/20251210_02.html

「ntt青森八戸ビル 通信施設」で2025年11月30日以前で検索すると以下のサイトがヒット．

https://telecom.blog.jp/archives/02_NTTE_Hachinohe.html

`SWIMMER{八戸NW3棟局}`

### paint (100pt / 282 solves)[medium]
> 添付画像に写っている航空機は、ある映画にちなんだ特別塗装機です。  
この特別塗装機は、2017年3月21日に就航開始しましたが、2025年に引退が発表され、去る2026年1月9日、この塗装での最後の運航を終了しました。  
さて、2017年にこの特別塗装が行われた建物はどこでしょうか。座標を地図上で示してください。  
paint.jpg  

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/paint.jpg)

画像検索などで調べると，以下のサイトにたどり着いた．

https://travel.watch.impress.co.jp/docs/news/1050316.html

> 伊丹空港に隣接するMRO Japanで塗装作業が進められた

とあるので，伊丹空港に隣接するMRO Japanの場所を調べる．
しかし，MRO Japanは現在は那覇に移転されており，伊丹空港からはなくなっていたため，場所がわからない．

上記サイトに記載の動画（ https://youtu.be/Ko0SRkR00F0 ）をみると，塗装が行われたであろう建物が写っている．

Google Earth で2017/5/18の航空写真から探してみると，おそらく以下だと特定．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im03.png)

提出すると，正解だった．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im04.png)

### satellite_imagery (100pt / 344 solves)[medium]
> この衛星画像が取得（撮影）された日はいつでしょうか？ YYYY/MM/DD 形式で解答してください。  
例えば2026年1月17日の場合、Flagは SWIMMER{2026/01/17} となります。   
satellite_imagery.jpg

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/satellite_imagery.jpg)

画像に`ケータリングビル`とあるので，Google Earth で検索するとヒットする．撮影日時は2025/04/18だとわかった．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im02.png)

`SWIMMER{2025/04/18}`

### rage (100pt / 301 solves)[hard]　
> 2025年7月、メキシコシティでは住宅価格の高騰を理由とするデモが起きました。    
以下の記事URLから確認できる報道写真で破壊されている店舗がオープンした日時を、 YYYY/MM/DD （現地時間）の形式で解答してください。  
例えば、答えが2025年7月5日の場合、Flagは SWIMMER{2025/07/05} となります。  
記事 / Article: https://www.jiji.com/jc/article?k=2025070500264&g=int&p=20250705at46S&rel=pv  

まず画像検索で類似の画像を検索した結果，以下のサイトを発見．

https://nationalpost.com/video/a93578ce-59f2-11f0-820e-12676e2ca516/protests-against-surging-mass-tourism-in-mexico-city-end-in-vandalism-harassment-of-tourists

デモの様子の動画を再生できる．途中のお店のシーンで特徴的なロゴを画像検索すると，RIPNDIP という店だとわかった．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im01.png)

メキシコシティにあるRIPNDIPを調べると，Ripndip Mexico Cityだとわかる．

この店舗がオープンした日時は，以下のサイトから，2024/09/14だとわかった．

https://milled.com/ripndip/new-ripndip-mexico-city-store-PY2G_9O6DSqhFCOJ

`SWIMMER{2024/09/14}`


上記を全て解くと，以下の最終問題が出現．（ただし，research_2025カテゴリは必須ではなかったらしい．）

## ops_swimmer

### ops_swimmer (457pt / 75 solves)[hard]
> 2025年のある日、rain, debeyohiru, lilica が初めて3人で集まったようです。 この日以降、この集団は活動を徐々に活発化させました。  
それはいつ・どこだったのでしょうか？  
以下の情報を教えてください。  
日付（YYYY/MM/DD 形式）  
集合時刻（HHmm 形式。日本時間で24時間表記）  
場所（チェーン店名と店舗名）  
例えば、2025年の10月1日の20時00分（日本時間）に、マクドナルド 新宿西口店で集合している場合、 Flagは SWIMMER{2025/10/01_2000_マクドナルド新宿西口店} となります。  
店舗名は 公式サイト の日本語表記に準拠します。ただし、スペース ( ) が含まれている場合、それを省いてください。  


<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">この前コミケ前にお友達と会ってきたのもある<br><br>この世界を再構成する　絶対にする</p>&mdash; 黄昏ブロッサムリリカ (@twilight_lilica) <a href="https://twitter.com/twilight_lilica/status/2007449492468211719?ref_src=twsrc%5Etfw">January 3, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">その時の写真<br>食べかけだけど <a href="https://t.co/kSvESX6REH">pic.twitter.com/kSvESX6REH</a></p>&mdash; 黄昏ブロッサムリリカ (@twilight_lilica) <a href="https://twitter.com/twilight_lilica/status/2007451888996724926?ref_src=twsrc%5Etfw">January 3, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">さっき解散した！<br>何か新しいことが始まりそう予感 <a href="https://t.co/rVwerQP7uy">pic.twitter.com/rVwerQP7uy</a></p>&mdash; brutorain (@bruto_rain) <a href="https://twitter.com/bruto_rain/status/2005982246499742052?ref_src=twsrc%5Etfw">December 30, 2025</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">これもいつ無くなるか分からないよな<br>念の為記録しておこう<br>流石にMVは消えないと思ってるけど何があるかわからない <a href="https://t.co/SbPPtMr7Wi">pic.twitter.com/SbPPtMr7Wi</a></p>&mdash; brutorain (@bruto_rain) <a href="https://twitter.com/bruto_rain/status/2006604801514057878?ref_src=twsrc%5Etfw">January 1, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">この前の写真整理してる<br>ハンバーグうまかった <a href="https://t.co/CxLj4NzKDV">pic.twitter.com/CxLj4NzKDV</a></p>&mdash; brutorain (@bruto_rain) <a href="https://twitter.com/bruto_rain/status/2007384438939021503?ref_src=twsrc%5Etfw">January 3, 2026</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

これらの情報から，2025/12/30 にデニーズ大井町駅前店で集合したと推測できる．

「さっき解散した！」のツイートの画像はおそらくこの場所．Googleストリートビューは2018年10月のため少し古い．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/swimmer-osint-ctf-2026/im13.png)

だが，残りの集合時間がわからなかった．

[追記]  
CTF終了後，GmailアドレスからGoogleカレンダーの情報が得られることがわかった．

https://epieos.com/ などを使えば，`furaigo5.onionsoup@gmail.com`を入力すると，以下の情報が得られる．

https://calendar.google.com/calendar/u/0/embed?src=furaigo5.onionsoup@gmail.com

> 集会：2025年 12月 30日 (火曜日)·午後6:30～8:30  
notes：18:30集合　店の前

この情報から，flagは以下になる．

`SWIMMER{2025/12/30_1830_デニーズ大井町駅前店}`