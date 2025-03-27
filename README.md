<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>文书分类管理系统</title>
    <style>
        /* 保留所有原有样式 */
        body {
            font-family: 'Microsoft YaHei', Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        /* 其他样式保持不变... */
    </style>
</head>
<body>
    <!-- HTML结构保持不变 -->
    <div class="container">
        <!-- 原有HTML内容 -->
    </div>

    <script>
        // 修复后的JavaScript代码

        // 初始数据
        let documents = [
            { category: "常用回复", title: "问候语", content: "您好，在的，有什么可以帮助您的吗？" },
            { category: "常用回复", content: "感谢您的咨询，祝您生活愉快！" },
            { category: "技术支持", title: "密码重置", content: "您可以点击登录页面的'忘记密码'链接，按照提示操作重置密码。" }
        ];
        
        // 当前选中的分类（null表示"全部"）
        let currentCategory = null;
        let currentSearchTerm = '';
        
        // DOM元素
        // ...保留原有DOM元素获取代码...

        // 初始化页面
        function initPage() {
            loadData();
            renderCategories();
            renderContent();
            setupEventListeners();
        }

        // 渲染内容表格（修复版）
        function renderContent() {
            contentBodyEl.innerHTML = '';
            
            // 获取筛选后的文档
            let filteredDocs = filterDocuments();
            
            if (filteredDocs.length === 0) {
                const noResultsText = currentSearchTerm ? 
                    `没有找到包含"${currentSearchTerm}"的内容` : 
                    '没有找到相关内容';
                const row = document.createElement('tr');
                row.innerHTML = `<td colspan="3">${noResultsText}</td>`;
                contentBodyEl.appendChild(row);
                return;
            }
            
            // 渲染文档列表
            filteredDocs.forEach((doc, index) => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${highlightText(doc.category, currentSearchTerm)}</td>
                    <td>${doc.title ? highlightText(doc.title, currentSearchTerm) : '<span class="optional-field">无标题</span>'}</td>
                    <td>${highlightText(doc.content, currentSearchTerm)}</td>
                `;
                
                // 添加点击事件编辑文档
                row.addEventListener('click', () => {
                    // 找到文档在原始数组中的索引
                    const originalIndex = documents.findIndex(d => 
                        d.category === doc.category && 
                        (d.title || '') === (doc.title || '') && 
                        d.content === doc.content
                    );
                    
                    if (originalIndex !== -1) {
                        editDocument(originalIndex);
                    }
                });
                
                contentBodyEl.appendChild(row);
            });
        }

        // 文档筛选逻辑（修复版）
        function filterDocuments() {
            let filtered = documents;
            
            // 1. 先按分类筛选
            if (currentCategory) {
                filtered = filtered.filter(doc => doc.category === currentCategory);
            }
            
            // 2. 再按搜索词筛选
            if (currentSearchTerm) {
                const keyword = currentSearchTerm.toLowerCase();
                filtered = filtered.filter(doc => 
                    doc.category.toLowerCase().includes(keyword) || 
                    (doc.title && doc.title.toLowerCase().includes(keyword)) || 
                    doc.content.toLowerCase().includes(keyword)
                );
            }
            
            return filtered;
        }

        // 搜索内容
        function searchContent() {
            currentSearchTerm = searchInputEl.value.trim();
            renderContent();
        }

        // 渲染分类按钮（修复版）
        function renderCategories() {
            const categories = [...new Set(documents.map(doc => doc.category))];
            
            categoriesEl.innerHTML = '';
            
            // "全部"按钮
            const allBtn = document.createElement('button');
            allBtn.className = `category-btn ${currentCategory === null ? 'active' : ''}`;
            allBtn.textContent = '全部';
            allBtn.addEventListener('click', () => {
                currentCategory = null;
                renderCategories();
                renderContent();
            });
            categoriesEl.appendChild(allBtn);
            
            // 各分类按钮
            categories.forEach(category => {
                const categoryItem = document.createElement('div');
                categoryItem.className = 'category-item';
                
                const btn = document.createElement('button');
                btn.className = `category-btn ${currentCategory === category ? 'active' : ''}`;
                btn.textContent = category;
                btn.addEventListener('click', () => {
                    currentCategory = category;
                    renderCategories();
                    renderContent();
                });
                
                const deleteBtn = document.createElement('button');
                deleteBtn.className = 'delete-category-btn';
                deleteBtn.innerHTML = '&times;';
                deleteBtn.title = '删除此分类';
                deleteBtn.addEventListener('click', (e) => {
                    e.stopPropagation();
                    deleteCategory(category);
                });
                
                categoryItem.appendChild(btn);
                categoryItem.appendChild(deleteBtn);
                categoriesEl.appendChild(categoryItem);
            });
            
            // 添加分类按钮
            const addBtn = document.createElement('button');
            addBtn.className = 'category-btn';
            addBtn.textContent = '+ 添加分类';
            addBtn.addEventListener('click', () => {
                editCategoryEl.value = '';
                editTitleEl.value = '';
                editContentEl.value = '';
                delete saveContentEl.dataset.index;
                editCategoryEl.focus();
            });
            categoriesEl.appendChild(addBtn);
        }

        // 其他函数保持不变...
        // loadData(), saveData(), deleteCategory(), editDocument(), saveDocument()等...
        // highlightText(), exportToJSON(), exportToCSV(), importData(), resetData()等...

        // 初始化页面
        initPage();
    </script>
</body>
</html>
