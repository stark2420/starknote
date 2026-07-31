---
title: "検索"
layout: "single"
---

<link href="/starknote/_pagefind/pagefind-ui.css" rel="stylesheet">
<script src="/starknote/_pagefind/pagefind-ui.js"></script>

<div id="search"></div>

<script>
    window.addEventListener('DOMContentLoaded', (event) => {
        // 1. Pagefindを初期化
        const pf = new PagefindUI({ 
            element: "#search", 
            showImages: false,
            bundlePath: "/starknote/_pagefind/",
            showSubResults: true,
            highlightParam: "h",
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

        // 2. 「戻る」で戻ってきたときにURLからキーワードを復元する
        const urlParams = new URLSearchParams(window.location.search);
        const savedQuery = urlParams.get('q'); // URLの ?q=XXX を取得
        
        if (savedQuery) {
            // Pagefindの検索窓に文字を入力して検索を実行
            pf.triggerSearch(savedQuery);
        }

        // 3. 入力中にリアルタイムでURLを更新する（ブラウザ履歴を汚さないリプレイス）
        // 読者がタイピングするたびに作動します
        setTimeout(() => {
            const searchInput = document.querySelector('#search input');
            if (searchInput) {
                searchInput.addEventListener('input', (e) => {
                    const currentQuery = e.target.value;
                    const url = new URL(window.location.href);
                    
                    if (currentQuery) {
                        url.searchParams.set('q', currentQuery);
                    } else {
                        url.searchParams.delete('q');
                    }
                    // 履歴を増やさずに現在のURLだけを書き換える
                    window.history.replaceState({}, '', url.toString());
                });
            }
        }, 100); // フォームが生成されるのを僅かに待つ
    });
</script>