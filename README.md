[全球实时新闻（国外版）.html](https://github.com/user-attachments/files/23831939/default.html)
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>全球实时新闻</title>
    <link rel="icon" href="https://s2.loli.net/2025/03/24/XxG7kR3bY9nPqLw.png" type="image/png">
    <meta name="description" content="支持中国、斯里兰卡、全球实时新闻浏览器">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }
        body { 
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh; color: #333; 
        }
        .container { max-width: 1200px; margin: 0 auto; padding: 20px; }
        .header { text-align: center; color: white; margin-bottom: 30px; }
        .header h1 { font-size: 2.5em; margin-bottom: 10px; text-shadow: 2px 2px 4px rgba(0,0,0,0.3); }
        .header p { font-size: 1.1em; opacity: 0.9; }
        .controls { 
            background: rgba(255,255,255,0.95); padding: 20px; border-radius: 15px; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.2); margin-bottom: 30px; 
            display: flex; gap: 15px; flex-wrap: wrap; justify-content: center; align-items: center; 
        }
        .search-box { position: relative; flex: 1; min-width: 300px; }
        .search-box input { 
            width: 100%; padding: 12px 45px 12px 15px; border: 2px solid #e1e5e9; 
            border-radius: 25px; font-size: 16px; transition: all 0.3s ease; outline: none; 
        }
        .search-box input:focus { border-color: #667eea; box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1); }
        .search-btn, .category-btn { 
            padding: 12px 24px; border: none; border-radius: 25px; 
            background: linear-gradient(45deg, #667eea, #764ba2); color: white; cursor: pointer; 
            font-size: 14px; font-weight: 600; transition: all 0.3s ease; 
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.3); 
        }
        .search-btn:hover, .category-btn:hover { 
            transform: translateY(-2px); box-shadow: 0 6px 20px rgba(102, 126, 234, 0.4); 
        }
        .category-btn.active { background: linear-gradient(45deg, #ff6b6b, #ee5a24); }
        .news-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(350px, 1fr)); gap: 25px; margin-bottom: 30px; }
        .news-card { 
            background: rgba(255,255,255,0.95); border-radius: 20px; overflow: hidden; 
            box-shadow: 0 10px 40px rgba(0,0,0,0.1); transition: all 0.3s ease; 
            border: 1px solid rgba(255,255,255,0.2); cursor: pointer; 
        }
        .news-card:hover { transform: translateY(-5px); box-shadow: 0 20px 50px rgba(0,0,0,0.15); }
        .news-image { 
            width: 100%; height: 200px; background: linear-gradient(45deg, #667eea, #764ba2); 
            display: flex; align-items: center; justify-content: center; color: white; font-size: 4em; 
            position: relative; overflow: hidden; 
        }
        .news-image img { 
            width: 100%; height: 100%; object-fit: cover; 
            transition: transform 0.3s ease; 
        }
        .news-image img:hover { transform: scale(1.05); }
        .news-content { padding: 20px; }
        .news-title { font-size: 1.2em; font-weight: 700; margin-bottom: 10px; line-height: 1.4; color: #2c3e50; }
        .news-description { color: #7f8c8d; line-height: 1.6; margin-bottom: 15px; font-size: 0.95em; }
        .news-meta { display: flex; justify-content: space-between; align-items: center; font-size: 0.85em; color: #95a5a6; }
        .category-tag { 
            background: linear-gradient(45deg, #667eea, #764ba2); color: white; 
            padding: 4px 12px; border-radius: 15px; font-size: 0.8em; font-weight: 600; 
        }
        .sri-lanka-tag { background: linear-gradient(45deg, #f39c12, #e67e22) !important; }
        .loading { text-align: center; padding: 40px; color: white; font-size: 1.2em; }
        .loading::after { content: ''; display: inline-block; width: 20px; height: 20px; border: 3px solid rgba(255,255,255,0.3); border-radius: 50%; border-top-color: #fff; animation: spin 1s ease-in-out infinite; margin-left: 10px; }
        @keyframes spin { to { transform: rotate(360deg); } }
        .no-results { text-align: center; padding: 40px; color: rgba(255,255,255,0.8); grid-column: 1 / -1; }
        .api-status { 
            position: fixed; top: 20px; right: 20px; background: #28a745; color: white; 
            padding: 10px 15px; border-radius: 20px; font-size: 12px; font-weight: bold; 
            box-shadow: 0 4px 15px rgba(40,167,69,0.3); z-index: 1000; 
        }
        .api-status.error { background: #dc3545; box-shadow: 0 4px 15px rgba(220,53,69,0.3); }
        @media (max-width: 768px) { 
            .controls { flex-direction: column; } 
            .search-box { min-width: 100%; } 
            .news-grid { grid-template-columns: 1fr; } 
        }
    </style>
</head>
<body>
    <div class="api-status" id="apiStatus">🔄 初始化中...</div>
    
    <div class="container">
        <div class="header">
            <h1>📰 全球实时新闻</h1>
            <p>✅ ReferenceError已修复 | 事件监听器优化</p>
        </div>

        <div class="controls">
            <div class="search-box">
                <input type="text" id="searchInput" placeholder="🔍 搜索（如：Sri Lanka、科伦坡）">
                <button class="search-btn" id="searchBtn">搜索</button>
            </div>
            <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                <button class="category-btn active" data-category="top-headlines">🌍 头条</button>
                <button class="category-btn" data-category="sri-lanka">🇱🇰 斯里兰卡</button>
                <button class="category-btn" data-category="china">🇨🇳 中国</button>
                <button class="category-btn" data-category="world">🌐 国际</button>
                <button class="category-btn" data-category="technology">💻 科技</button>
                <button class="category-btn" data-category="business">💰 经济</button>
            </div>
        </div>

        <div id="newsContainer" class="news-grid">
            <div class="loading">🔄 加载最新新闻...</div>
        </div>
    </div>

    <script>
        // ⭐⭐⭐ API密钥（你的密钥）⭐⭐⭐
        const API_KEY = 'pub_b016503c9aea4f61b02cc1f7f8b80a94';  
        
        const API_BASE = 'https://newsdata.io/api/1';
        let allNews = [];
        let currentCategory = 'top-headlines';

        // 🔥 图片服务
        function getStableImageUrl(type, index = 0) {
            const sriLankaSeeds = ['colombo', 'srilanka', 'tea', 'beach', 'elephant'];
            if (type === 'sri-lanka') {
                return `https://picsum.photos/400/200?random=${sriLankaSeeds[index % 5]}`;
            }
            const seed = type + index;
            return `https://picsum.photos/400/200?random=${seed}`;
        }

        function getSvgPlaceholder(type) {
            return `https://placehold.co/400x200/${type}/png?text=${encodeURIComponent(type)}&font=roboto`;
        }

        // 检查密钥
        function checkApiKey() {
            if (!API_KEY || API_KEY.includes('YOUR')) {
                updateStatus('⚠️ 使用备用数据（设置密钥以启用实时API）');
                return false;
            }
            return true;
        }

        // 主加载函数（修复）
        async function loadNews(type, query = '') {
            currentCategory = type;
            updateActiveButton(type);
            
            const container = document.getElementById('newsContainer');
            container.innerHTML = '<div class="loading">🔄 加载' + (type === 'sri-lanka' ? '🇱🇰 斯里兰卡' : type === 'china' ? '🇨🇳 中国' : '') + '新闻...</div>';
            
            // 先加载备用
            loadBackupNews(type);
            
            // 尝试API
            if (checkApiKey()) {
                try {
                    let url = `${API_BASE}/news?apikey=${API_KEY}&size=20`;
                    switch(type) {
                        case 'top-headlines': 
                            url += '&category=world&language=en'; 
                            break;
                        case 'sri-lanka': 
                            url += '&country=lk&language=en'; 
                            break;
                        case 'china': 
                            url += '&q=china&language=zh-CN'; 
                            break;
                        case 'world': 
                            url += '&category=world&language=en'; 
                            break;
                        case 'technology': 
                            url += '&category=technology&language=en'; 
                            break;
                        case 'business': 
                            url += '&category=business&language=en'; 
                            break;
                        case 'search':
                            if (!query) query = document.getElementById('searchInput').value || 'news';
                            url += `&q=${encodeURIComponent(query)}&language=en`;
                            break;
                    }

                    console.log('API URL:', url);
                    const response = await fetch(url);
                    if (!response.ok) {
                        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
                    }
                    const data = await response.json();
                    
                    if (data.status === 'success' && data.results.length > 0) {
                        allNews = data.results.map((article, index) => ({
                            ...article,
                            image_url: article.image_url || getStableImageUrl(type, index)
                        }));
                        renderNews(allNews);
                        updateStatus(`✅ ${allNews.length} 条实时新闻（API成功）`);
                        return;
                    } else {
                        console.log('API返回空结果:', data);
                    }
                } catch (error) {
                    console.error('API错误:', error);
                    updateStatus('⚠️ API加载中... 使用备用数据');
                }
            }
            
            loadBackupNews(type);
        }

        // 备用数据
        function loadBackupNews(type) {
            const backupData = {
                'sri-lanka': [
                    { 
                        title: "科伦坡港集装箱堆积严重", 
                        description: "经济危机持续，港口工人罢工导致货物积压", 
                        source_id: "Daily Mirror", 
                        pubDate: new Date(Date.now() - 1*60*60*1000).toISOString(),
                        link: "https://dailymirror.lk",
                        image_url: getStableImageUrl('sri-lanka', 0)
                    },
                    { 
                        title: "锡吉里耶石灰岩寺庙修复完成", 
                        description: "联合国教科文组织资助的世界遗产地恢复原貌", 
                        source_id: "Ada Derana", 
                        pubDate: new Date(Date.now() - 3*60*60*1000).toISOString(),
                        link: "https://adaderana.lk",
                        image_url: getStableImageUrl('sri-lanka', 1)
                    },
                    { 
                        title: "南部茶园工人要求加薪", 
                        description: "世界最大茶叶出口国面临劳工危机", 
                        source_id: "Colombo Telegraph", 
                        pubDate: new Date(Date.now() - 5*60*60*1000).toISOString(),
                        link: "https://colombotelegraph.com",
                        image_url: getStableImageUrl('sri-lanka', 2)
                    },
                    { 
                        title: "中国援助斯里兰卡电力项目", 
                        description: "汉班托塔燃煤电站恢复供电缓解全国停电", 
                        source_id: "The Island", 
                        pubDate: new Date(Date.now() - 2*60*60*1000).toISOString(),
                        link: "http://island.lk",
                        image_url: getStableImageUrl('sri-lanka', 3)
                    }
                ],
                'top-headlines': [
                    { title: "特朗普宣布永久移民禁令", description: "华盛顿枪击事件后全面暂停移民", source_id: "CNN", pubDate: new Date(Date.now() - 2*60*60*1000).toISOString(), link: "https://cnn.com", image_url: getStableImageUrl('news', 1) },
                    { title: "印尼洪灾死亡164人", description: "苏门答腊岛山体滑坡救援艰难", source_id: "BBC", pubDate: new Date(Date.now() - 4*60*60*1000).toISOString(), link: "https://bbc.com", image_url: getStableImageUrl('disaster', 2) }
                ],
                'china': [
                    { title: "中国外交部回应日本涉台言论", description: "王毅强调一个中国原则", source_id: "新华社", pubDate: new Date(Date.now() - 1*60*60*1000).toISOString(), link: "http://xinhuanet.com", image_url: getStableImageUrl('china', 1) },
                    { title: "金砖国家合作机制持续壮大", description: "专家称其灵活性有效应对全球经济动荡", source_id: "人民日报", pubDate: new Date(Date.now() - 3*60*60*1000).toISOString(), link: "http://people.com.cn", image_url: getStableImageUrl('economy', 2) }
                ]
            };
            
            const news = backupData[type] || backupData['top-headlines'];
            renderNews(news);
            updateStatus(`✅ 备用模式：${news.length} 条高质量新闻`);
        }

        // 渲染新闻
        function renderNews(articles) {
            const container = document.getElementById('newsContainer');
            if (!articles || articles.length === 0) {
                container.innerHTML = '<div class="no-results">❌ 暂无新闻，试试搜索！</div>';
                return;
            }

            container.innerHTML = articles.map(article => {
                let imageUrl = article.image_url || getStableImageUrl(currentCategory, Math.floor(Math.random() * 1000));
                const fallbackUrl = getSvgPlaceholder(currentCategory);
                const sourceClass = currentCategory === 'sri-lanka' ? 'sri-lanka-tag' : '';
                return `
                <div class="news-card" onclick="openNews('${article.link || article.url || '#'}')">
                    <div class="news-image">
                        <img src="${imageUrl}" alt="${article.title}" 
                             onerror="this.src='${fallbackUrl}'; this.onerror=null;">
                    </div>
                    <div class="news-content">
                        <div class="news-title">${article.title}</div>
                        <div class="news-description">${(article.description || '').substring(0, 150)}${(article.description || '').length > 150 ? '...' : ''}</div>
                        <div class="news-meta">
                            <span class="category-tag ${sourceClass}">${article.source_id || article.source?.name || '新闻'}</span>
                            <span>${formatTime(article.pubDate || article.publishedAt)}</span>
                        </div>
                    </div>
                </div>
            `;
            }).join('');
        }

        // 打开新闻
        function openNews(url) {
            if (url && url !== '#') {
                window.open(url, '_blank');
            } else {
                alert('暂无详细链接');
            }
        }

        // 更新激活按钮
        function updateActiveButton(type) {
            document.querySelectorAll('.category-btn').forEach(btn => btn.classList.remove('active'));
            document.querySelector(`[data-category="${type}"]`).classList.add('active');
        }

        // 更新状态
        function updateStatus(message) {
            const status = document.getElementById('apiStatus');
            status.innerHTML = message;
            status.className = 'api-status';
        }

        // 时间格式
        function formatTime(isoString) {
            const date = new Date(isoString);
            const now = new Date();
            const diff = Math.floor((now - date) / 1000 / 60);
            if (diff < 1) return '刚刚';
            if (diff < 60) return `${diff}分钟前`;
            if (diff < 1440) return `${Math.floor(diff/60)}小时前`;
            return `${Math.floor(diff/1440)}天前`;
        }

        // ✅ 关键修复：使用事件监听器（定义后绑定）
        document.addEventListener('DOMContentLoaded', function() {
            // 绑定分类按钮
            document.querySelectorAll('.category-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const category = this.getAttribute('data-category');
                    loadNews(category);
                });
            });

            // 绑定搜索按钮
            document.getElementById('searchBtn').addEventListener('click', searchNews);

            // 绑定搜索输入
            document.getElementById('searchInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') searchNews();
            });

            // 初始加载
            loadNews('top-headlines');
            updateStatus('✅ 新闻浏览器启动成功！（事件监听已优化）');
        });

        // 搜索函数
        function searchNews() {
            const query = document.getElementById('searchInput').value.trim();
            if (query) {
                loadNews('search', query);
            } else {
                loadNews(currentCategory);
            }
        }
    </script>
</body>
</html>
