+++
author = "Hugo Authors"
title = "stable-diffusionの使い方(オリジナルキャラの作成)"
date = "2025-01-01"
description = ""
tags = [
]
categories = [
]
draft = true
+++

<!--more-->

## stable-diffusionのインストール
https://romptn.com/article/5211 を参照．

1. Python 3.10.6
2. git
3. Stable Diffusion

使うときは，作成したフォルダ→「stable-diffusion-webui」→下から２番目の「webui-user.bat」をクリックしてコマンドを実行．
```
http://127.0.0.1:7860/
```

## Stable Diffusionに『ControlNet』を導入する
https://romptn.com/article/7868#toc4 を参照．

## IP-Adapterモデルをダウンロード(似た画像のみを作成するため)
https://romptn.com/article/33719 を参照．

## 使い方

おすすめのモデル10選
https://romptn.com/article/6236  
MeinaMixの使い方  
https://romptn.com/article/14898  
モデルをダウンロード後は，`stable-diffusion-webui\models\Stable-diffusion`に入れる．

まず，左上のモデルを設定．meinamix_v12Final.safetensors [a5e5a941a3]  
プロンプト
```
male child, boy, very short hair, detailed beautiful blue eyes, detailed beautiful face, very short brown hair, best quality, highly detailed, light brown hair, blue hair ornaments, hasp, different-dimension world
```
ネガティブプロンプト
```
(worst quality, low quality:1.4)
```
Generateで画像生成．

IP-Adapterの使い方は，
下の方のControlNet v1.1.455を開き，画像を入力．  
Enableにチェック．  
Control Typeを`IP-Adapter`にする．  
Preprocessorを`ip-adapter_clip_sdxl_plus_vith`，Modelを`ip-adapter_sd15 [6a3f6166]`などにする．  
Generateで画像生成．

### 元画像の作成
Copilotで「萌え系の可愛い男の子のアイコンを作ってほしい」という．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/stable-diffusion/immy-original.jpg)

この画像に対して，IP-Adapterを使用．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/stable-diffusion/immy.png)