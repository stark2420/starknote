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
                many_results: "[COUNT] 件の検索結果が見つかりました。",
                one_result: "[COUNT] 件の検索結果が見つかりました。",
                alt_search: "「[SEARCH_TERM]」の代わりに「[ORIGINAL_TERM]」を検索しています。",
                search_suggestion: "もしかして: [DERIVED_TERM]",
                searching: "検索中..."
            }
        });

        const searchContainer = document.querySelector('#search');
        const urlParams = new URLSearchParams(window.location.search);
        const savedQuery = urlParams.get('q');
        const savedCount = parseInt(urlParams.get('c'), 10) || 0;

        let lastQuery = null;
        let lastCount = null;

        // 2. 検索キーワードや表示件数が変化した際にURLを更新する関数
        const updateURL = () => {
            const searchInput = searchContainer.querySelector('input');
            const currentQuery = searchInput ? searchInput.value.trim() : '';
            const currentCount = searchContainer.querySelectorAll('.pagefind-ui__result').length;

            // 前回と状態が変わっていなければ何もしない（無駄な更新を防止）
            if (currentQuery === lastQuery && currentCount === lastCount) return;
            
            lastQuery = currentQuery;
            lastCount = currentCount;

            const url = new URL(window.location.href);

            if (currentQuery) {
                url.searchParams.set('q', currentQuery);
                if (currentCount > 0) {
                    url.searchParams.set('c', currentCount);
                } else {
                    url.searchParams.delete('c');
                }
            } else {
                url.searchParams.delete('q');
                url.searchParams.delete('c');
            }

            window.history.replaceState({}, '', url.toString());
        };

        // 結果の描画や「もっと見る」でDOM（件数）が増えた瞬間を監視してURLを自動保存
        const urlObserver = new MutationObserver(updateURL);
        urlObserver.observe(searchContainer, { childList: true, subtree: true });

        // キーボード入力時もURLを記録
        searchContainer.addEventListener('input', updateURL);

        // 3. 戻ってきたときの自動復元処理
        if (savedQuery) {
            if (savedCount > 0) {
                let clicking = false;

                const restoreObserver = new MutationObserver(() => {
                    if (clicking) return; // 連打・重複処理の防止

                    const currentItems = searchContainer.querySelectorAll('.pagefind-ui__result').length;
                    const loadMoreBtn = searchContainer.querySelector('.pagefind-ui__button');

                    // 表示件数が復元したい件数(savedCount)より少なければ「もっと見る」を押す
                    if (currentItems > 0 && currentItems < savedCount && loadMoreBtn) {
                        clicking = true;
                        setTimeout(() => {
                            const btn = searchContainer.querySelector('.pagefind-ui__button');
                            if (btn) btn.click();
                            clicking = false;
                        }, 150);
                    } else if (currentItems >= savedCount || (!loadMoreBtn && currentItems > 0)) {
                        // 目標件数に達した、またはこれ以上結果がない場合は監視を終了
                        restoreObserver.disconnect();
                    }
                });

                restoreObserver.observe(searchContainer, { childList: true, subtree: true });
            }

            // 検索の実行
            pf.triggerSearch(savedQuery);
        }
    });
</script>