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
                many_results: "[COUNT] 件の検索結果が見つかりました。", // [COUNT] に修正
                one_result: "[COUNT] 件の検索結果が見つかりました。",   // [COUNT] に修正
                alt_search: "「[SEARCH_TERM]」の代わりに「[ORIGINAL_TERM]」を検索しています。",
                search_suggestion: "もしかして: [DERIVED_TERM]",
                searching: "検索中..."
            }
        });

        // URLパラメータを取得
        const urlParams = new URLSearchParams(window.location.search);
        const savedQuery = urlParams.get('q');
        const savedCount = parseInt(urlParams.get('c'), 10) || 0;

        // 2. 検索実行と「もっと見る」の自動復元
        if (savedQuery) {
            pf.triggerSearch(savedQuery);

            if (savedCount > 0) {
                const searchContainer = document.querySelector('#search');
                
                const observer = new MutationObserver(() => {
                    const loadMoreBtn = searchContainer.querySelector('.pagefind-ui__button');
                    const currentItems = searchContainer.querySelectorAll('.pagefind-ui__result').length;

                    if (loadMoreBtn && currentItems < savedCount) {
                        loadMoreBtn.click();
                    } else if (currentItems >= savedCount || !loadMoreBtn) {
                        observer.disconnect();
                    }
                });

                observer.observe(searchContainer, { childList: true, subtree: true });
            }
        }

        // 3. 入力およびクリック時にURLパラメータ（q と c）を更新する設定
        setTimeout(() => {
            const searchContainer = document.querySelector('#search');
            
            const updateURL = () => {
                const searchInput = searchContainer.querySelector('input');
                const currentQuery = searchInput ? searchInput.value : '';
                const currentCount = searchContainer.querySelectorAll('.pagefind-ui__result').length;
                
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

            if (searchContainer) {
                searchContainer.addEventListener('input', updateURL);
                
                searchContainer.addEventListener('click', (e) => {
                    if (e.target && e.target.classList.contains('pagefind-ui__button')) {
                        setTimeout(updateURL, 100);
                    }
                });
            }
        }, 100);
    });
</script>