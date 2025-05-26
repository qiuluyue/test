class AlloyCalculator {
    constructor() {
        this.pureMetals = null;
        this.alloys = null;
        this.elements = [];
        
        // 初始化事件监听
        document.getElementById('pureFile').addEventListener('change', this.handleFileUpload.bind(this, 'pure'));
        document.getElementById('alloyFile').addEventListener('change', this.handleFileUpload.bind(this, 'alloy'));
        document.getElementById('calculateBtn').addEventListener('click', this.optimize.bind(this));
    }

    // 文件上传处理
    handleFileUpload(type, e) {
        const file = e.target.files[0];
        const reader = new FileReader();
        
        reader.onload = (event) => {
            const data = new Uint8Array(event.target.result);
            const workbook = XLSX.read(data, {type: 'array'});
            const sheet = workbook.Sheets[workbook.SheetNames[0]];
            const jsonData = XLSX.utils.sheet_to_json(sheet);

            if (type === 'pure') {
                this.pureMetals = this.processPureMetals(jsonData);
                this.initElementInputs();
            } else {
                this.alloys = this.processAlloys(jsonData);
                this.initAlloyCheckboxes();
            }
        };
        
        reader.readAsArrayBuffer(file);
    }

    // 处理纯金属数据
    processPureMetals(data) {
        return data.map(item => ({
            name: item['金属名称'],
            element: item['元素名称'],
            price: item['单价元/kg']
        }));
    }

    // 处理合金数据
    processAlloys(data) {
        // 与原始Python逻辑相同的处理逻辑
        const alloyMap = new Map();
        
        data.forEach(row => {
            const name = row['合金名称'];
            const elements = row['所含元素'].split(',');
            const ratios = row['元素质量比'].split(',').map(Number);
            
            if (!alloyMap.has(name)) {
                alloyMap.set(name, {elements, ratios: []});
            }
            alloyMap.get(name).ratios.push(...ratios);
        });

        return Array.from(alloyMap).map(([name, info]) => {
            const total = info.ratios.reduce((a, b) => a + b, 0);
            const normalized = info.ratios.map(r => r / total);
            return {
                name,
                elements: info.elements,
                ratios: normalized,
                price: data.find(r => r['合金名称'] === name)['单价元/kg']
            };
        });
    }

    // 初始化元素输入
    initElementInputs() {
        const container = document.getElementById('elementInputs');
        this.elements = [...new Set(this.pureMetals.map(m => m.element))].filter(e => e !== 'Ti');
        
        this.elements.forEach(elem => {
            const div = document.createElement('div');
            div.className = 'input-group';
            div.innerHTML = `
                <label>${elem}:</label>
                <input type="number" min="0" max="100" step="0.1" 
                       class="element-input" data-element="${elem}" 
                       oninput="updateTiContent()">
                <span>%</span>
            `;
            container.appendChild(div);
        });
    }

    // 更新Ti含量显示
    updateTiContent() {
        const inputs = document.querySelectorAll('.element-input');
        const total = Array.from(inputs).reduce((sum, input) => 
            sum + (parseFloat(input.value) || 0), 0);
        document.getElementById('tiContent').textContent = (100 - total).toFixed(1);
    }

    // 优化计算逻辑（移植Python核心逻辑）
    optimize() {
        // 实现与Python版相同的算法逻辑
        // ...（此处需完整移植原optimize方法逻辑）
    }
}

// 初始化应用
new AlloyCalculator();
