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
            // 💡 サブディレクトリ配下でも正しく検索できるようにbundlePathを指定
            bundlePath: "/starknote/_pagefind/",
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