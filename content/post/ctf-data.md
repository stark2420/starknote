+++
author = "Hugo Authors"
title = "📌CTF Writeups"
date = "2025-12-01"
weight = -1
description = ""
tags = [
    "CTF",
    "writeup",
]
categories = [
    "Security",
    "Programming",
]
image = "https://raw.githubusercontent.com/stark2420/starknote/refs/heads/main/static/image/ctf-data/cover.png"
+++

解いたCTF問題の一覧．
<!--more-->

<style>
.cat {
  display: inline-block;
  padding: 2px 8px;
  border-radius: 6px;
  font-size: 0.85em;
  font-weight: 600;
  color: #1f2937;   /* 読みやすい濃色 */
  white-space: nowrap;
  border: 1px solid rgba(0,0,0,0.1);
}

th.sortable {
  cursor: pointer;
  user-select: none;
}

th.sortable::after {
  content: "";
  margin-left: 6px;
  font-size: 0.7em;
  opacity: 0.6;
}

th.sortable.asc::after {
  content: "▲";
}

th.sortable.desc::after {
  content: "▼";
}

th.sortable:hover::after {
  opacity: 1;
}
th.sortable.asc,
th.sortable.desc {
  color: #2563eb; /* 青系 */
}
</style>

<td><span class="cat">Pwn</span></td>
<td><span class="cat">Web</span></td>
<td><span class="cat">Rev</span></td>
<td><span class="cat">Crypto</span></td>
<td><span class="cat">Misc</span></td>
<td><span class="cat">Osint</span></td>

<table id="sortable-table">
  <thead>
    <tr>
      <th onclick="sortTable(0, this)" class="sortable">Name</th>
      <th onclick="sortTable(1, this)" class="sortable">Event</th>
      <th onclick="sortTable(2, this)" class="sortable">Category</th>
      <th onclick="sortTable(3, this)" class="sortable">Difficulty</th>
      <th onclick="sortTable(4, this)" class="sortable">Tags</th>
      <th onclick="sortTable(5, this)" class="sortable">Dates</th>
      <th onclick="sortTable(6, this)" class="sortable">備考</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><a href="/starknote/post/alpacahack/#alpacahack-2100-welcome">AlpacaHack 2100</a></td>
      <td>Daily AlpacaHack</td>
      <td><span class="cat">Misc</span></td>
      <td>Welcome</td>
      <td>URL</td>
      <td>2025-12-01</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="/starknote/post/alpacahack/#emojify-medium">Emojify</a></td>
      <td>Daily AlpacaHack</td>
      <td><span class="cat">Web</span></td>
      <td>Medium</td>
      <td>JavaScript, URL()</td>
      <td>2025-12-03</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="/starknote/post/alpacahack/#log-viewer-medium">Log Viewer</a></td>
      <td>Daily AlpacaHack</td>
      <td><span class="cat">Web</span></td>
      <td>Medium</td>
      <td>awk</td>
      <td>2025-12-22</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="/starknote/post/alpacahack/#simpleoverwrite-medium">simpleoverwrite</a></td>
      <td>Daily AlpacaHack</td>
      <td><span class="cat">Pwn</span></td>
      <td>Medium</td>
      <td>BOF</td>
      <td>2025-12-27</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="/starknote/post/alpacahack/#secret-table-medium">secret-table</a></td>
      <td>Daily AlpacaHack</td>
      <td><span class="cat">Web</span></td>
      <td>Medium</td>
      <td>SQLi</td>
      <td>2026-01-08</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="/starknote/post/alpacahack/#basic-buffer-overflow-medium">Basic Buffer Overflow</a></td>
      <td>Daily AlpacaHack</td>
      <td><span class="cat">Pwn</span></td>
      <td>Medium</td>
      <td>BOF</td>
      <td>2026-01-12</td>
      <td></td>
    </tr>
    <tr>
      <td><a href="/starknote/post/alpacahack/#free-comment-hard">free-comment</a></td>
      <td>Daily AlpacaHack</td>
      <td><span class="cat">Misc</span></td>
      <td>Hard</td>
      <td>pyexec</td>
      <td>2026-01-14</td>
      <td></td>
    </tr>
  </tbody>
</table>

<script>
const CATEGORY_COLORS = {
  Pwn:   '#fecaca', // 赤（少し濃い）
  Web:   '#bfdbfe', // 青
  Rev:   '#ddd6fe', // 紫
  Crypto:'#fde68a', // 黄
  Misc:  '#a7f3d0', // 緑
  Osint: '#a5f3fc'  // 水色
};

document.querySelectorAll('.cat').forEach(el => {
  const name = el.innerText.trim();
  el.style.backgroundColor =
    CATEGORY_COLORS[name] || autoColor(name);
});

/* 未定義カテゴリ用：少し濃い自動色 */
function autoColor(str) {
  let hash = 0;
  for (let i = 0; i < str.length; i++) {
    hash = str.charCodeAt(i) + ((hash << 5) - hash);
  }
  const hue = hash % 360;
  return `hsl(${hue}, 65%, 80%)`;
}

let sortDirection = {};

function sortTable(colIndex, th) {
  const table = document.getElementById("sortable-table");
  const tbody = table.tBodies[0];
  const rows = Array.from(tbody.rows);

  const dir = sortDirection[colIndex] === "asc" ? "desc" : "asc";
  sortDirection = {};           // 他列リセット
  sortDirection[colIndex] = dir;

  // 全ヘッダのアイコンをリセット
  table.querySelectorAll("th.sortable").forEach(h => {
    h.classList.remove("asc", "desc");
  });
  th.classList.add(dir);

  rows.sort((a, b) => {
    let x = a.cells[colIndex].innerText.trim();
    let y = b.cells[colIndex].innerText.trim();

    const dx = Date.parse(x);
    const dy = Date.parse(y);
    if (!isNaN(dx) && !isNaN(dy)) {
      return dir === "asc" ? dx - dy : dy - dx;
    }

    return dir === "asc"
      ? x.localeCompare(y, "ja")
      : y.localeCompare(x, "ja");
  });

  rows.forEach(row => tbody.appendChild(row));
}
</script>