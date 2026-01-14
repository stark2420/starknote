+++
author = "Hugo Authors"
title = "TSG CTF 2025 Writeup"
date = "2025-12-23"
description = ""
tags = [
    "CTF",
    "writeup",
    "tsg",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/tsgctf-2025/cover.png"
+++

2025/12/20 16:00(JST) - 2025/12/21 16:00(JST)で開催された TSG CTF 2025 に参加した．

<!--more-->
チームで参加し，結果は，1201 点で 489 チーム中 88 位 (Domesticチームでは26位) だった．

解いた問題についてまとめておく．

https://score.ctf.tsg.ne.jp/challenges

## Web
### image-compress-revenge [106 Solve (100)]
> I tried making an app with Vibe coding. It's easy and nice, isn't it?  
http://35.221.67.248:10502  
image-compress-revenge.tar.gz     

画像をアップロードできるWebアプリである．
ファイル名は`escape`関数によって，サニタイズされている．
該当の文字の場合，`\\`をつけることでエスケープされる．
しかし，適用される文字列の中にバックスラッシュ`\`がないことが脆弱性となる．

{{< code lang="python" title="server.ts" hl_lines="">}}
import { Elysia, t } from "elysia";
import { unlink } from "fs/promises";
import { run } from "./lib/shell.ts";

const CHARS_TO_ESCAPE = "$'\"(){}[]:;/&`~|^!? \n".split("");

export function escape(source: string): string {
	let s = source;
	for (const char of CHARS_TO_ESCAPE) {
		s = s.replaceAll(char, "\\" + char);
	}
	return s;
}

const app = new Elysia()
	.get("/", () => {
		return Bun.file("./public/index.html");
	})
	.post(
		"/compress",
		async ({ body, set }) => {
			const { image, quality } = body;

			if (image.name.includes("..")) {
				throw new Error(`Invalid file name: ${image.name}`);
			}

			const inputPath = `./tmp/inputs/${escape(image.name)}`;
			const outputPath = `./tmp/outputs/${escape(image.name)}`;
			console.log(escape(image.name));

			try {
				await Bun.write(inputPath, image);

				await run(
					`magick "${inputPath}" -quality ${quality} -strip "${outputPath}"`,
				);

				const compressed = await Bun.file(outputPath).arrayBuffer();

				set.headers["Content-Type"] = image.type;
				set.headers["Content-Disposition"] =
					`attachment; filename="${image.name}"`;

				return new Response(compressed);
			} catch (error) {
				set.status = 500;
				return { error: `Failed to compress image: ${error}` };
			} finally {
				await unlink(inputPath).catch(() => {});
				await unlink(outputPath).catch(() => {});
			}
		},
		{
			body: t.Object({
				image: t.File({
					"file-type": "image/*",
					maxSize: "10m",
				}),
				quality: t.Numeric({
					minimum: 1,
					maximum: 100,
					default: 85,
				}),
			}),
		},
	);

app.listen(process.env.PORT ?? "3000", (server) => {
	console.log(
		`🦊 server is running at http://${server.hostname}:${server.port}`,
	);
});
{{< /code >}}

また，Dockerfileから，FLAGは環境変数にあることがわかる．

```
FROM oven/bun@sha256:b5cf5ca5dc3e2a02d805802ba089401c4beabf597daabbf35a17b8e82dc2f7bc

# Install ImageMagick
RUN apt-get update && \
    apt-get install -y imagemagick && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy package files
COPY package.json bun.lock ./

# Install dependencies
RUN bun install --frozen-lockfile

# Copy application files
COPY server.ts ./
COPY public ./public
COPY lib ./lib

# Create tmp directory
RUN mkdir -p ./tmp

ENV FLAG=TSGCTFDUMMY{}
ENV PORT=3000

CMD ["bun", "run", "server.ts"]
```


具体的には，ファイル名に`\$FLAG.jpg`を入力すると，`escape`関数によって`$`がエスケープされ，`\\\$FLAG.jpg`となる．

このとき，内部動作としては，`\`がエスケープされていることになり，`$FLAG`で環境変数がよみこまれることになる．

Burp Suiteなどで実行すると，flagが得られる．

![This is a image](https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/tsgctf-2025/im02.png)


`TSGCTF{d0llar_s1gn_1s_mag1c_1n_sh3ll_env1r0nm3nt_and_r3ad0nly_15_r3qu1r3d_f0r_c0mmand_1nj3c710n_chall3ng35}`



### library [227 Solve (100)]
> Our library's new staff portal is up! Only the head librarian can access the restricted archives.  
http://35.221.67.248:10501  
library.tar.gz

## Misc
### Mission: Impossible [106 Solve (100)]
> The security system of CIA vault room is state-of-the-art.  
There are a large number of pressure-sensitive, temperature-sensitive and audio-sensitive sensors.  
And the terminal is operated by voice recognition.  
http://35.194.98.181:57860/  
mission_impossible.tar.gz  

### Sudoers Maze 100pts [143 Solve (100)]
> Where am I? Who am I?  
nc 34.180.66.205 55655  
sudoers_maze.tar.gz  