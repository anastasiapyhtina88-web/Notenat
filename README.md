<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Smart Notes — Визуальные задачи</title>
    <!-- Подключаем Fabric.js для работы с холстом -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fabric.js/5.3.0/fabric.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
            background: #f0f4f8;
            color: #1a202c;
            padding: 16px;
            overflow-x: hidden;
        }
        
        /* ===== ХЕДЕР ===== */
        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 12px;
            margin-bottom: 20px;
            background: white;
            padding: 12px 20px;
            border-radius: 16px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.06);
        }
        .header h1 {
            font-size: 22px;
            font-weight: 700;
            background: linear-gradient(135deg, #667eea, #764ba2);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
        .header-controls {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }
        .header-controls button {
            padding: 8px 16px;
            border: none;
            border-radius: 10px;
            background: #edf2f7;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
            font-size: 13px;
        }
        .header-controls button:hover {
            background: #e2e8f0;
            transform: scale(0.97);
        }
        .btn-primary {
            background: #667eea !important;
            color: white !important;
        }
        .btn-primary:hover {
            background: #5a67d8 !important;
        }
        
        /* ===== ПАНЕЛЬ ФИГУР ===== */
        .toolbar {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-bottom: 16px;
            background: white;
            padding: 12px 16px;
            border-radius: 16px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.06);
            align-items: center;
        }
        .toolbar-group {
            display: flex;
            gap: 6px;
            align-items: center;
            flex-wrap: wrap;
            padding-right: 12px;
            border-right: 2px solid #e2e8f0;
            margin-right: 12px;
        }
        .toolbar-group:last-child {
            border-right: none;
            margin-right: 0;
        }
        .toolbar-group label {
            font-size: 12px;
            font-weight: 600;
            color: #4a5568;
            margin-right: 4px;
        }
        .shape-btn {
            width: 44px;
            height: 44px;
            border: 2px solid #e2e8f0;
            border-radius: 10px;
            background: white;
            cursor: pointer;
            font-size: 20px;
            transition: 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .shape-btn:hover {
            border-color: #667eea;
            background: #f7fafc;
        }
        .shape-btn.active {
            border-color: #667eea;
            background: #ebf4ff;
        }
        
        input[type="range"] {
            width: 80px;
            accent-color: #667eea;
        }
        input[type="color"] {
            width: 36px;
            height: 36px;
            border: 2px solid #e2e8f0;
            border-radius: 8px;
            cursor: pointer;
            padding: 2px;
        }
        select {
            padding: 6px 10px;
            border-radius: 8px;
            border: 2px solid #e2e8f0;
16:21


background: white;
            font-size: 13px;
            cursor: pointer;
        }
        
        /* ===== ХОЛСТ ===== */
        .canvas-wrapper {
            background: white;
            border-radius: 16px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.08);
            overflow: hidden;
            margin-bottom: 20px;
            position: relative;
        }
        #canvas-container {
            width: 100%;
            height: 600px;
            position: relative;
        }
        #canvas-container canvas {
            display: block;
            width: 100% !important;
            height: 100% !important;
        }
        
        /* ===== СПИСОК ЗАДАЧ (активные и выполненные) ===== */
        .tasks-section {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 16px;
            margin-top: 20px;
        }
        @media (max-width: 700px) {
            .tasks-section {
                grid-template-columns: 1fr;
            }
        }
        .task-board {
            background: white;
            border-radius: 16px;
            padding: 16px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.06);
            min-height: 200px;
        }
        .task-board h3 {
            font-size: 16px;
            margin-bottom: 12px;
            color: #2d3748;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .task-board h3 span {
            background: #edf2f7;
            padding: 2px 10px;
            border-radius: 20px;
            font-size: 12px;
        }
        .task-item {
            display: flex;
            align-items: center;
            gap: 10px;
            padding: 8px 12px;
            background: #f7fafc;
            border-radius: 10px;
            margin-bottom: 6px;
            border-left: 4px solid #667eea;
            cursor: pointer;
            transition: 0.2s;
            font-size: 14px;
        }
        .task-item:hover {
            background: #edf2f7;
        }
        .task-item.done {
            border-left-color: #48bb78;
            opacity: 0.6;
        }
        .task-item.done .task-text {
            text-decoration: line-through;
        }
        .task-item input[type="checkbox"] {
            width: 18px;
            height: 18px;
            accent-color: #667eea;
            cursor: pointer;
            flex-shrink: 0;
        }
        .task-text {
            flex: 1;
            word-break: break-word;
        }
        .task-shape-type {
            font-size: 18px;
        }
        .task-comment-bubble {
            background: #edf2f7;
            border-radius: 50%;
            width: 28px;
            height: 28px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            font-size: 14px;
            transition: 0.2s;
            flex-shrink: 0;
        }
        .task-comment-bubble:hover {
            background: #e2e8f0;
            transform: scale(1.1);
        }
        .empty-state {
            color: #a0aec0;
            font-size: 14px;
            text-align: center;
            padding: 30px 0;
        }
        
        /* ===== МОДАЛКА КОММЕНТАРИЯ ===== */
        .modal-overlay {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.4);
            backdrop-filter: blur(4px);
            z-index: 1000;
            align-items: center;
            justify-content: center;
        }
        .modal-overlay.show {
            display: flex;
        }
        .modal {
            background: white;
            border-radius: 20px;
            padding: 24px;
            max-width: 420px;
            width: 90%;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            animation: fadeUp 0.25s ease;
        }
        @keyframes fadeUp {
            from { transform: translateY(20px); opacity: 0; }
16:21
to { transform: translateY(0); opacity: 1; }
        }
        .modal h4 {
            margin-bottom: 12px;
            color: #2d3748;
        }
        .modal textarea {
            width: 100%;
            padding: 12px;
            border: 2px solid #e2e8f0;
            border-radius: 12px;
            font-size: 14px;
            font-family: inherit;
            resize: vertical;
            min-height: 80px;
        }
        .modal textarea:focus {
            outline: none;
            border-color: #667eea;
        }
        .modal-actions {
            display: flex;
            gap: 10px;
            margin-top: 14px;
            justify-content: flex-end;
        }
        .modal-actions button {
            padding: 8px 20px;
            border: none;
            border-radius: 10px;
            font-weight: 600;
            cursor: pointer;
            transition: 0.2s;
        }
        .modal-actions .save-btn {
            background: #667eea;
            color: white;
        }
        .modal-actions .save-btn:hover {
            background: #5a67d8;
        }
        .modal-actions .cancel-btn {
            background: #edf2f7;
        }
        .modal-actions .cancel-btn:hover {
            background: #e2e8f0;
        }
        
        /* ===== ОБЩЕЕ ===== */
        .badge {
            font-size: 11px;
            background: #edf2f7;
            padding: 2px 10px;
            border-radius: 20px;
        }
    </style>
</head>
<body>

<!-- ===== ХЕДЕР ===== -->
<div class="header">
    <h1>🧩  Smart Notes</h1>
    <div class="header-controls">
        <button onclick="exportData()">📤 Экспорт</button>
        <button onclick="importData()">📥 Импорт</button>
        <button class="btn-primary" onclick="clearAll()">🗑️  Очистить всё</button>
    </div>
</div>

<!-- ===== ПАНЕЛЬ ИНСТРУМЕНТОВ ===== -->
<div class="toolbar">
    <div class="toolbar-group">
        <label>Фигура:</label>
        <button class="shape-btn active" data-shape="rect" onclick="setShape('rect')">▭</button>
        <button class="shape-btn" data-shape="circle" onclick="setShape('circle')">◯</button>
        <button class="shape-btn" data-shape="diamond" onclick="setShape('diamond')">◇</button>
        <button class="shape-btn" data-shape="cloud" onclick="setShape('cloud')">☁</button>
        <button class="shape-btn" data-shape="heart" onclick="setShape('heart')">❤</button>
    </div>
    
    <div class="toolbar-group">
        <label>Контур:</label>
        <select id="strokeStyle" onchange="updateStroke()">
            <option value="solid">──── Сплошной</option>
            <option value="dashed">- - - Прерывистый</option>
            <option value="dotted">····· Точечный</option>
        </select>
    </div>
    
    <div class="toolbar-group">
        <label>Толщина:</label>
        <input type="range" id="strokeWidth" min="1" max="8" value="2" oninput="updateStroke()">
        <span id="strokeWidthLabel" style="font-size:12px;width:24px;">2</span>
    </div>
    
    <div class="toolbar-group">
        <label>Цвет:</label>
        <input type="color" id="shapeColor" value="#667eea" onchange="updateColor()">
    </div>
    
    <div class="toolbar-group" style="border-right:none;">
        <button class="btn-primary" onclick="addShapeToCanvas()" style="padding:8px 18px;">➕ Добавить на холст</button>
    </div>
</div>

<!-- ===== ХОЛСТ ===== -->
<div class="canvas-wrapper">
    <div id="canvas-container"></div>
</div>

<!-- ===== СПИСКИ ЗАДАЧ ===== -->
<div class="tasks-section">
    <div class="task-board" id="activeBoard">
        <h3>⏳ Активные <span id="activeCount">0</span></h3>
        <div id="activeList"></div>
    </div>
    <div class="task-board" id="doneBoard">
        <h3>✅ Выполненные <span id="doneCount">0</span></h3>
        <div id="doneList"></div>
    </div>
</div>

<!-- ===== МОДАЛКА КОММЕНТАРИЯ ===== -->
<div class="modal-overlay" id="commentModal">
    <div class="modal">
16:21
<h4>💬  Комментарий к задаче</h4>
        <textarea id="commentInput" placeholder="Введите комментарий..."></textarea>
        <div class="modal-actions">
            <button class="cancel-btn" onclick="closeCommentModal()">Отмена</button>
            <button class="save-btn" onclick="saveComment()">Сохранить</button>
        </div>
    </div>
</div>

<script>
    // ============================================================
    // 1. НАСТРОЙКА ХОЛСТА (Fabric.js)
    // ============================================================
    const container = document.getElementById('canvas-container');
    const canvas = new fabric.Canvas(container, {
        width: container.clientWidth || 800,
        height: 600,
        backgroundColor: '#ffffff',
        selection: true,
        preserveObjectStacking: true,
    });
    
    // Ресайз
    function resizeCanvas() {
        const w = container.clientWidth || 800;
        canvas.setWidth(w);
        canvas.setHeight(600);
        canvas.renderAll();
    }
    window.addEventListener('resize', resizeCanvas);
    setTimeout(resizeCanvas, 100);
    
    // ============================================================
    // 2. СОСТОЯНИЕ
    // ============================================================
    let currentShape = 'rect';
    let currentStrokeStyle = 'solid';
    let currentStrokeWidth = 2;
    let currentColor = '#667eea';
    
    let tasks = [];
    let selectedTaskId = null; // для комментария
    let nextId = 1;
    
    // Загрузка данных
    function loadData() {
        const saved = localStorage.getItem('smartNotesData');
        if (saved) {
            try {
                const data = JSON.parse(saved);
                tasks = data.tasks || [];
                nextId = data.nextId || 1;
                // Восстанавливаем объекты на холсте
                if (data.canvasObjects) {
                    canvas.loadFromJSON(data.canvasObjects, () => {
                        canvas.renderAll();
                        // Переназначить клики на объекты
                        canvas.getObjects().forEach(obj => {
                            if (obj.taskId) {
                                obj.on('mousedown', () => onShapeClick(obj.taskId));
                            }
                        });
                    });
                }
            } catch(e) { console.warn('Ошибка загрузки', e); }
        }
        renderTasks();
    }
    
    function saveData() {
        const data = {
            tasks: tasks,
            nextId: nextId,
            canvasObjects: canvas.toJSON(['taskId', 'comment', 'done']),
        };
        localStorage.setItem('smartNotesData', JSON.stringify(data));
    }
    
    // ============================================================
    // 3. ФИГУРЫ
    // ============================================================
    function setShape(shape) {
        currentShape = shape;
        document.querySelectorAll('.shape-btn').forEach(b => b.classList.remove('active'));
        document.querySelector(`[data-shape="${shape}"]`).classList.add('active');
    }
    
    function updateStroke() {
        currentStrokeStyle = document.getElementById('strokeStyle').value;
        currentStrokeWidth = parseInt(document.getElementById('strokeWidth').value);
        document.getElementById('strokeWidthLabel').textContent = currentStrokeWidth;
    }
    
    function updateColor() {
        currentColor = document.getElementById('shapeColor').value;
    }
    
    // Создание фигуры
    function createShape(shapeType, x, y, taskText) {
        const size = 80;
        const color = currentColor;
        const stroke = currentStrokeStyle === 'solid' ? '' :
                       currentStrokeStyle === 'dashed' ? [6, 4] :
                       [2, 4];
        const strokeWidth = currentStrokeWidth;
        let shape;
        const opts = {
            left: x - size/2,
            top: y - size/2,
16:21
fill: 'rgba(102, 126, 234, 0.08)',
            stroke: color,
            strokeWidth: strokeWidth,
            strokeDashArray: stroke || undefined,
            selectable: true,
            hasControls: true,
            hasBorders: true,
            originX: 'center',
            originY: 'center',
        };
        
        switch(shapeType) {
            case 'rect':
                shape = new fabric.Rect({
                    ...opts,
                    width: size,
                    height: size * 0.7,
                    rx: 12,
                    ry: 12,
                });
                break;
            case 'circle':
                shape = new fabric.Circle({
                    ...opts,
                    radius: size/2,
                });
                break;
            case 'diamond':
                shape = new fabric.Polygon([
                    {x: 0, y: -size/2},
                    {x: size/2, y: 0},
                    {x: 0, y: size/2},
                    {x: -size/2, y: 0}
                ], {
                    ...opts,
                    originX: 'center',
                    originY: 'center',
                });
                break;
            case 'cloud':
                shape = new fabric.Path('M 0 0 C 10 -20, 30 -25, 40 -10 C 55 -20, 75 -10, 70 10 C 85 15, 80 35, 65 40 C 60 55, 40 60, 25 50 C 10 55, -5 45, 0 30 C -15 25, -15 10, 0 0 Z', {
                    ...opts,
                    scaleX: 0.8,
                    scaleY: 0.6,
                    originX: 'center',
                    originY: 'center',
                    left: x,
                    top: y,
                });
                break;
            case 'heart':
                shape = new fabric.Path('M 0 -15 C -10 -30, -30 -20, -30 0 C -30 15, 0 30, 0 35 C 0 30, 30 15, 30 0 C 30 -20, 10 -30, 0 -15 Z', {
                    ...opts,
                    scaleX: 1.2,
                    scaleY: 1.2,
                    originX: 'center',
                    originY: 'center',
                    left: x,
                    top: y,
                });
                break;
            default:
                shape = new fabric.Rect({...opts, width: size, height: size/2, rx: 8});
        }
        
        // Добавляем текст внутри фигуры
        const text = new fabric.Text(taskText || 'Задача', {
            fontSize: 14,
            fontWeight: 600,
            fill: '#2d3748',
            originX: 'center',
            originY: 'center',
            left: x,
            top: y,
            selectable: false,
            evented: false,
        });
        
        const group = new fabric.Group([shape, text], {
            left: x - size/2,
            top: y - size/2,
            originX: 'center',
            originY: 'center',
            selectable: true,
            hasControls: true,
            hasBorders: true,
            taskId: null,
            comment: '',
            done: false,
        });
        
        return group;
    }
    
    // ============================================================
    // 4. ДОБАВЛЕНИЕ НА ХОЛСТ + ЗАДАЧА
    // ============================================================
    function addShapeToCanvas() {
        const centerX = canvas.width / 2 + (Math.random() - 0.5) * 120;
        const centerY = canvas.height / 2 + (Math.random() - 0.5) * 80;
        
        const taskText = prompt('Введите задачу:', 'Новая задача');
        if (taskText === null) return;
        const text = taskText.trim() || 'Задача';
        
        const group = createShape(currentShape, centerX, centerY, text);
        const taskId = nextId++;
        
        group.taskId = taskId;
        group.comment = '';
        group.done = false;
        
        // Клик по фигуре
        group.on('mousedown', () => onShapeClick(taskId));
        
        canvas.add(group);
        canvas.setActiveObject(group);
        canvas.renderAll();
16:21
// Добавляем в задачи
        tasks.push({
            id: taskId,
            text: text,
            shape: currentShape,
            done: false,
            comment: '',
            color: currentColor,
        });
        
        saveData();
        renderTasks();
    }
    
    // ============================================================
    // 5. КЛИК ПО ФИГУРЕ — ГАЛОЧКА ИЛИ КОММЕНТАРИЙ
    // ============================================================
    function onShapeClick(taskId) {
        const task = tasks.find(t => t.id === taskId);
        if (!task) return;
        
        // Если зажата клавиша Shift — открываем комментарий
        if (window.shiftKey) {
            selectedTaskId = taskId;
            document.getElementById('commentInput').value = task.comment || '';
            document.getElementById('commentModal').classList.add('show');
            return;
        }
        
        // Иначе — переключаем статус
        task.done = !task.done;
        
        // Обновляем на холсте
        const obj = canvas.getObjects().find(o => o.taskId === taskId);
        if (obj) {
            obj.done = task.done;
            // Визуально затемняем
            obj.getObjects().forEach(child => {
                if (child.type === 'rect' || child.type === 'circle' || 
                    child.type === 'polygon' || child.type === 'path') {
                    child.set({ opacity: task.done ? 0.4 : 1 });
                }
            });
            canvas.renderAll();
        }
        
        saveData();
        renderTasks();
    }
    
    // ============================================================
    // 6. КОММЕНТАРИИ (МОДАЛКА)
    // ============================================================
    function closeCommentModal() {
        document.getElementById('commentModal').classList.remove('show');
        selectedTaskId = null;
    }
    
    function saveComment() {
        if (selectedTaskId === null) return;
        const text = document.getElementById('commentInput').value;
        const task = tasks.find(t => t.id === selectedTaskId);
        if (task) {
            task.comment = text;
            // Сохраняем на объекте холста
            const obj = canvas.getObjects().find(o => o.taskId === selectedTaskId);
            if (obj) obj.comment = text;
            saveData();
            renderTasks();
        }
        closeCommentModal();
    }
    
    // Закрытие по Esc и клику вне
    document.getElementById('commentModal').addEventListener('click', function(e) {
        if (e.target === this) closeCommentModal();
    });
    document.addEventListener('keydown', (e) => {
        if (e.key === 'Escape') closeCommentModal();
        if (e.key === 'Shift') window.shiftKey = true;
    });
    document.addEventListener('keyup', (e) => {
        if (e.key === 'Shift') window.shiftKey = false;
    });
    
    // ============================================================
    // 7. ОТРИСОВКА СПИСКОВ ЗАДАЧ
    // ============================================================
    function renderTasks() {
        const active = tasks.filter(t => !t.done);
        const done = tasks.filter(t => t.done);
        
        document.getElementById('activeCount').textContent = active.length;
        document.getElementById('doneCount').textContent = done.length;
        
        // Активные
        const activeList = document.getElementById('activeList');
        if (active.length === 0) {
            activeList.innerHTML = `<div class="empty-state">✨ Нет активных задач</div>`;
        } else {
            activeList.innerHTML = active.map(task => `
                <div class="task-item" onclick="onShapeClick(${task.id})">
                    <input type="checkbox" ${task.done ? 'checked' : ''} 
                           onclick="event.stopPropagation(); toggleTask(${task.id})">
                    <span class="task-shape-type">${getShapeEmoji(task.shape)}</span>
16:21
<span class="task-text">${task.text}</span>
                    ${task.comment ? `<span class="task-comment-bubble" onclick="event.stopPropagation(); openCommentFor(${task.id})">💬 </span>` : ''}
                    <span class="badge">${task.shape}</span>
                </div>
            `).join('');
        }
        
        // Выполненные
        const doneList = document.getElementById('doneList');
        if (done.length === 0) {
            doneList.innerHTML = `<div class="empty-state">🎉  Нет выполненных задач</div>`;
        } else {
            doneList.innerHTML = done.map(task => `
                <div class="task-item done" onclick="onShapeClick(${task.id})">
                    <input type="checkbox" checked 
                           onclick="event.stopPropagation(); toggleTask(${task.id})">
                    <span class="task-shape-type">${getShapeEmoji(task.shape)}</span>
                    <span class="task-text">${task.text}</span>
                    ${task.comment ? `<span class="task-comment-bubble" onclick="event.stopPropagation(); openCommentFor(${task.id})">💬 </span>` : ''}
                    <span class="badge">${task.shape}</span>
                </div>
            `).join('');
        }
    }
    
    function getShapeEmoji(shape) {
        const map = { 'rect': '▭', 'circle': '◯', 'diamond': '◇', 'cloud': '☁', 'heart': '❤' };
        return map[shape] || '📌 ';
    }
    
    function toggleTask(id) {
        const task = tasks.find(t => t.id === id);
        if (!task) return;
        task.done = !task.done;
        
        const obj = canvas.getObjects().find(o => o.taskId === id);
        if (obj) {
            obj.done = task.done;
            obj.getObjects().forEach(child => {
                if (child.type === 'rect' || child.type === 'circle' || 
                    child.type === 'polygon' || child.type === 'path') {
                    child.set({ opacity: task.done ? 0.4 : 1 });
                }
            });
            canvas.renderAll();
        }
        
        saveData();
        renderTasks();
    }
    
    function openCommentFor(id) {
        selectedTaskId = id;
        const task = tasks.find(t => t.id === id);
        document.getElementById('commentInput').value = task?.comment || '';
        document.getElementById('commentModal').classList.add('show');
    }
    
    // ============================================================
    // 8. ЭКСПОРТ / ИМПОРТ
    // ============================================================
    function exportData() {
        const data = localStorage.getItem('smartNotesData');
        const blob = new Blob([data], {type: 'application/json'});
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `notes_backup_${new Date().toISOString().slice(0,10)}.json`;
        a.click();
        URL.revokeObjectURL(url);
    }
    
    function importData() {
        const input = document.createElement('input');
        input.type = 'file';
        input.accept = '.json';
        input.onchange = function(e) {
            const file = e.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(ev) {
                try {
                    const data = JSON.parse(ev.target.result);
                    localStorage.setItem('smartNotesData', JSON.stringify(data));
                    location.reload();
                } catch(err) {
                    alert('Ошибка: неверный файл');
                }
            };
            reader.readAsText(file);
        };
        input.click();
    }
    
    // ============================================================
    // 9. ОЧИСТКА
    // ============================================================
    function clearAll() {
        if (!confirm('Удалить все задачи и очистить холст?')) return;
        tasks = [];
16:21
canvas.clear();
        canvas.backgroundColor = '#ffffff';
        canvas.renderAll();
        saveData();
        renderTasks();
    }
    
    // ============================================================
    // 10. СТРЕЛКИ (доп.фича — соединять фигуры)
    // ============================================================
    // Простая реализация: добавляем стрелку между выбранными объектами
    document.addEventListener('keydown', (e) => {
        if (e.key === 'a' && e.ctrlKey) {
            e.preventDefault();
            const active = canvas.getActiveObjects();
            if (active.length === 2) {
                const obj1 = active[0];
                const obj2 = active[1];
                const p1 = obj1.getCenterPoint();
                const p2 = obj2.getCenterPoint();
                const line = new fabric.Line([p1.x, p1.y, p2.x, p2.y], {
                    stroke: '#4a5568',
                    strokeWidth: 2,
                    strokeDashArray: [6, 4],
                    selectable: true,
                    hasControls: false,
                    hasBorders: false,
                });
                // Треугольник-наконечник
                const angle = Math.atan2(p2.y - p1.y, p2.x - p1.x);
                const headSize = 12;
                const head = new fabric.Triangle({
                    left: p2.x - headSize/2 * Math.cos(angle) - headSize/2 * Math.sin(angle),
                    top: p2.y - headSize/2 * Math.sin(angle) + headSize/2 * Math.cos(angle),
                    width: headSize,
                    height: headSize,
                    fill: '#4a5568',
                    angle: angle * 180 / Math.PI,
                    originX: 'center',
                    originY: 'center',
                    selectable: false,
                });
                canvas.add(line);
                canvas.add(head);
                canvas.renderAll();
                saveData();
            }
        }
    });
    
    // Подсказка по стрелкам
    console.log('💡  Чтобы соединить фигуры стрелкой: выделите 2 фигуры и нажмите Ctrl+A');
    
    // ============================================================
    // 11. ЗАПУСК
    // ============================================================
    loadData();
    
    // Автосохранение при изменении холста
    canvas.on('object:modified', () => saveData());
    canvas.on('object:added', () => saveData());
    canvas.on('object:removed', () => saveData());
    
    // Чтобы сохранить при выходе
    window.addEventListener('beforeunload', saveData);
</script>
</body>
</html>
