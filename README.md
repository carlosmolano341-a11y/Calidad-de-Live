<!DOCTYPE html>
<html lang="es" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard de Seguimiento de Creadores LIVE</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        /* Custom scrollbar for table container */
        .table-container::-webkit-scrollbar {
            height: 8px;
            width: 8px;
        }
        .table-container::-webkit-scrollbar-track {
            background: #f1f5f9;
        }
        .table-container::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        .table-container::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 h-full flex flex-col">

    <!-- Top Header Navigation -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-30 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex flex-col sm:flex-row justify-between items-center gap-4">
            <div class="flex items-center space-x-3">
                <div class="bg-indigo-600 text-white p-2.5 rounded-xl shadow-md">
                    <i class="fa-solid fa-chart-line text-xl"></i>
                </div>
                <div>
                    <h1 class="text-xl font-bold text-slate-900 tracking-tight">Seguimiento de Creadores LIVE</h1>
                    <p class="text-xs text-slate-500">Panel de control y auditoría en tiempo real</p>
                </div>
            </div>

            <!-- Action Toolbar -->
            <div class="flex flex-wrap items-center gap-2.5">
                <button onclick="openAddModal()" class="inline-flex items-center gap-2 bg-indigo-600 hover:bg-indigo-700 text-white text-sm font-medium px-4 py-2 rounded-lg shadow-sm transition active:scale-95">
                    <i class="fa-solid fa-user-plus"></i> Nuevo Creador
                </button>
                <button onclick="exportData()" class="inline-flex items-center gap-2 bg-white hover:bg-slate-50 text-slate-700 text-sm font-medium px-3.5 py-2 rounded-lg border border-slate-300 shadow-sm transition">
                    <i class="fa-solid fa-download text-slate-500"></i> Exportar JSON
                </button>
                <label class="inline-flex items-center gap-2 bg-white hover:bg-slate-50 text-slate-700 text-sm font-medium px-3.5 py-2 rounded-lg border border-slate-300 shadow-sm transition cursor-pointer">
                    <i class="fa-solid fa-upload text-slate-500"></i> Importar
                    <input type="file" id="importFile" accept=".json" onchange="importData(event)" class="hidden">
                </label>
                <button onclick="resetData()" class="inline-flex items-center gap-2 bg-red-50 hover:bg-red-100 text-red-700 text-sm font-medium px-3 py-2 rounded-lg border border-red-200 transition" title="Restaurar datos de ejemplo">
                    <i class="fa-solid fa-rotate-right"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content Container -->
    <main class="flex-1 max-w-[100rem] w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 flex flex-col">
        
        <!-- Search & Filter Bar -->
        <div class="bg-white rounded-xl p-4 shadow-sm border border-slate-200 mb-6 flex flex-col sm:flex-row justify-between items-center gap-4">
            <div class="relative w-full sm:w-96">
                <span class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none text-slate-400">
                    <i class="fa-solid fa-search"></i>
                </span>
                <input type="text" id="searchInput" oninput="filterTable()" placeholder="Buscar por Handle, UID, Manager o Equipo..." 
                    class="w-full pl-10 pr-4 py-2 bg-slate-50 border border-slate-300 rounded-lg text-sm focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:bg-white transition">
            </div>
            <div class="flex items-center gap-3 text-sm text-slate-600 w-full sm:w-auto justify-end">
                <span id="counterBadge" class="bg-indigo-50 text-indigo-700 font-semibold px-3 py-1 rounded-full border border-indigo-100">
                    Total: 0 Creadores
                </span>
            </div>
        </div>

        <!-- Spreadsheet Table Container -->
        <div class="bg-white rounded-xl shadow-md border border-slate-200 flex-1 flex flex-col overflow-hidden">
            <div class="table-container flex-1 overflow-auto max-h-[calc(100vh-260px)]">
                <table class="w-full border-collapse text-left text-xs whitespace-nowrap">
                    <thead class="sticky top-0 z-20 text-slate-700 font-semibold uppercase tracking-wider select-none">
                        <!-- Group Header Row -->
                        <tr class="bg-slate-100 border-b border-slate-300 text-center text-[11px]">
                            <th colspan="7" class="py-2.5 px-3 border-r border-slate-300 bg-slate-100 text-slate-800 font-bold">Información General del Creador</th>
                            <th colspan="6" class="py-2.5 px-3 border-r border-slate-300 bg-amber-50 text-amber-900 font-bold">Check List Pre-Transmisión</th>
                            <th colspan="4" class="py-2.5 px-3 border-r border-slate-300 bg-sky-50 text-sky-900 font-bold">Check List Primeros 30 Segundos</th>
                            <th colspan="2" class="py-2.5 px-3 bg-emerald-50 text-emerald-900 font-bold">Resultado y Feedback</th>
                            <th rowspan="2" class="py-2.5 px-3 bg-slate-100 text-slate-600 font-bold w-16">Acciones</th>
                        </tr>
                        <!-- Column Header Row -->
                        <tr class="bg-slate-50 border-b border-slate-200 text-slate-600 text-center">
                            <!-- General -->
                            <th class="py-2 px-3 border-r border-slate-200 font-medium">Equipo</th>
                            <th class="py-2 px-3 border-r border-slate-200 font-medium">Manager</th>
                            <th class="py-2 px-3 border-r border-slate-200 font-medium">UID</th>
                            <th class="py-2 px-3 border-r border-slate-200 font-medium">Handle</th>
                            <th class="py-2 px-3 border-r border-slate-200 font-medium">Categoría</th>
                            <th class="py-2 px-3 border-r border-slate-200 font-medium">Estado Actividad</th>
                            <th class="py-2 px-3 border-r border-slate-200 font-medium">Fecha Revisión</th>
                            
                            <!-- Pre-Transmisión -->
                            <th class="py-2 px-2 border-r border-slate-200 bg-amber-50/50 font-medium" title="Captura Previa">Cap. Pantalla</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-amber-50/50 font-medium">Encuadre</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-amber-50/50 font-medium">Iluminación</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-amber-50/50 font-medium">Música Fondo</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-amber-50/50 font-medium">Entorno</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-amber-50/50 font-medium">Disp. Creador</th>
                            
                            <!-- Primeros 30s -->
                            <th class="py-2 px-2 border-r border-slate-200 bg-sky-50/50 font-medium">Hook Inicial</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-sky-50/50 font-medium">Propósito LIVE</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-sky-50/50 font-medium">Llamado Acción</th>
                            <th class="py-2 px-2 border-r border-slate-200 bg-sky-50/50 font-medium">Tema LIVE</th>
                            
                            <!-- Resultado -->
                            <th class="py-2 px-3 border-r border-slate-200 bg-emerald-50/50 font-medium">Estatus Mejora</th>
                            <th class="py-2 px-3 bg-emerald-50/50 font-medium min-w-[240px]">Feedback Entregado</th>
                        </tr>
                    </thead>
                    <tbody id="creatorTableBody" class="divide-y divide-slate-200 text-slate-700">
                        <!-- Dynamic Rows Injected Here -->
                    </tbody>
                </table>
            </div>
            
            <!-- Table Footer Info -->
            <div class="bg-slate-50 border-t border-slate-200 px-4 py-3 text-xs text-slate-500 flex justify-between items-center">
                <span>💡 Haga clic en cualquier celda para editar sus datos directamente o use las casillas de verificación.</span>
                <span id="lastSavedText" class="font-medium text-slate-400">Guardado automático activo</span>
            </div>
        </div>
    </main>

    <!-- Modal para Agregar/Editar Creador -->
    <div id="creatorModal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4 overflow-y-auto">
        <div class="bg-white rounded-2xl shadow-2xl max-w-lg w-full overflow-hidden border border-slate-200 transform transition-all">
            <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
                <h3 id="modalTitle" class="font-bold text-base"><i class="fa-solid fa-user-pen mr-2"></i> Agregar Nuevo Creador</h3>
                <button onclick="closeModal()" class="text-slate-400 hover:text-white transition text-lg"><i class="fa-solid fa-xmark"></i></button>
            </div>
            <form id="creatorForm" onsubmit="saveCreator(event)" class="p-6 space-y-4">
                <input type="hidden" id="creatorIndex" value="-1">
                
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Equipo</label>
                        <input type="text" id="inputEquipo" required placeholder="Ej. Alpha" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Manager</label>
                        <input type="text" id="inputManager" required placeholder="Ej. María Andréa" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">UID</label>
                        <input type="text" id="inputUid" required placeholder="Ej. 7482910" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Handle (@usuario)</label>
                        <input type="text" id="inputHandle" required placeholder="Ej. @creador_live" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                </div>

                <div class="grid grid-cols-3 gap-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Categoría</label>
                        <input type="text" id="inputCategoria" required placeholder="Ej. Batallas / Música" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Estado Actividad</label>
                        <select id="inputEstadoActividad" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none bg-white">
                            <option value="Activo">Activo</option>
                            <option value="Inactivo">Inactivo</option>
                            <option value="Nuevo">Nuevo</option>
                            <option value="En Pausa">En Pausa</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-slate-700 mb-1">Fecha Revisión</label>
                        <input type="date" id="inputFechaRevision" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">URL de Captura de Pantalla / Imagen</label>
                    <input type="url" id="inputImagen" placeholder="https://ejemplo.com/foto.jpg" class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>

                <div>
                    <label class="block text-xs font-semibold text-slate-700 mb-1">Feedback Inicial / Comentarios</label>
                    <textarea id="inputFeedback" rows="3" placeholder="Escriba el feedback inicial..." class="w-full px-3 py-2 border border-slate-300 rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none"></textarea>
                </div>

                <div class="flex justify-end space-x-3 pt-3 border-t border-slate-200">
                    <button type="button" onclick="closeModal()" class="px-4 py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 font-medium rounded-lg text-sm transition">Cancelar</button>
                    <button type="submit" class="px-5 py-2 bg-indigo-600 hover:bg-indigo-700 text-white font-medium rounded-lg text-sm shadow-sm transition">Guardar Creador</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Mensaje Notificación Flotante -->
    <div id="toast" class="fixed bottom-5 right-5 z-50 transform translate-y-20 opacity-0 transition-all duration-300 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-xl flex items-center gap-3 text-sm">
        <i id="toastIcon" class="fa-solid fa-circle-check text-emerald-400"></i>
        <span id="toastMessage">Operación realizada con éxito</span>
    </div>

    <!-- JavaScript Application Logic -->
    <script>
        // Default sample data mimicking the agency tracking sheet
        const defaultCreators = [
            {
                equipo: "Equipo A",
                manager: "María Andréa",
                uid: "849201",
                handle: "@sofi_live",
                categoria: "Batallas",
                estadoActividad: "Activo",
                fechaRevision: "2026-11-01",
                imagen: "https://images.unsplash.com/photo-1534528741775-53994a69daeb?w=150&auto=format&fit=crop&q=80",
                pre: { capPantalla: true, encuadre: true, iluminacion: true, musicaFondo: true, entorno: true, disposicion: true },
                post30: { hookInicial: true, proposito: true, llamadoAccion: false, temaLive: true },
                estatusMejora: "Mejorado",
                feedback: "Buenas batallas. Es Creadora top, se le hace la recomendación de tratar de manejar un tema en sus live para que su comunidad sepan de que se trata su live. Informa que sus live son batallas y hacer reír a la gente."
            },
            {
                equipo: "Equipo B",
                manager: "María Andréa",
                uid: "928310",
                handle: "@valeria_style",
                categoria: "Lifestyle",
                estadoActividad: "Activo",
                fechaRevision: "2026-11-01",
                imagen: "https://images.unsplash.com/photo-1517841905240-472988babdf9?w=150&auto=format&fit=crop&q=80",
                pre: { capPantalla: true, encuadre: true, iluminacion: true, musicaFondo: true, entorno: true, disposicion: true },
                post30: { hookInicial: true, proposito: true, llamadoAccion: true, temaLive: true },
                estatusMejora: "Mejorado",
                feedback: "La creadora mantiene el setup con los elementos adecuados para que el entorno se vea estético, con luces adecuadas. Se encarga de atraer a su público en el proceso inicial, y empieza sus batallas de forma entusiasta. Velamos por mejorar o trasformar su setup mucho mas pero cumple con las buenas expectativas del mismo."
            },
            {
                equipo: "Equipo A",
                manager: "María Andréa",
                uid: "635291",
                handle: "@camila_music",
                categoria: "Música",
                estadoActividad: "En Revisión",
                fechaRevision: "2026-11-01",
                imagen: "https://images.unsplash.com/photo-1524504388940-b1c1722653e1?w=150&auto=format&fit=crop&q=80",
                pre: { capPantalla: true, encuadre: true, iluminacion: true, musicaFondo: true, entorno: true, disposicion: true },
                post30: { hookInicial: true, proposito: true, llamadoAccion: true, temaLive: false },
                estatusMejora: "En Revisión",
                feedback: "La iluminación de la creadora ya funcionan muy bien, se ve profesional, limpio, sin embargo se me hizo la recomendación de que su letrero led con su nombre esté prendido para que así llame más la atención su live."
            },
            {
                equipo: "Equipo C",
                manager: "María Andréa",
                uid: "519283",
                handle: "@daniela_live",
                categoria: "Charla",
                estadoActividad: "Activo",
                fechaRevision: "2026-11-01",
                imagen: "https://images.unsplash.com/photo-1494790108377-be9c29b29330?w=150&auto=format&fit=crop&q=80",
                pre: { capPantalla: true, encuadre: true, iluminacion: true, musicaFondo: false, entorno: true, disposicion: true },
                post30: { hookInicial: true, proposito: false, llamadoAccion: true, temaLive: true },
                estatusMejora: "Mejorado",
                feedback: "Creadora con excelente set, sabe escuchar y acata todas las recomendaciones, le indique lo del proposito del live y Tema del Live. Me indico que recibido, estara en revision el dia de hoy para verificar si mejora estas dos indicaciones."
            },
            {
                equipo: "Equipo B",
                manager: "María Andréa",
                uid: "482910",
                handle: "@lucia_show",
                categoria: "Entretenimiento",
                estadoActividad: "Activo",
                fechaRevision: "2026-11-01",
                imagen: "https://images.unsplash.com/photo-1507003211169-0a1dd7228f2d?w=150&auto=format&fit=crop&q=80",
                pre: { capPantalla: true, encuadre: true, iluminacion: true, musicaFondo: true, entorno: true, disposicion: true },
                post30: { hookInicial: true, proposito: true, llamadoAccion: true, temaLive: true },
                estatusMejora: "Mejorado",
                feedback: "Creadora con Live de Musica muy organizado y estructurado, le indique sobre la revision del live para mejorar algunas cosas tambien (Como saludar los primero 10 segundo) y la felicite. Ella en este momento esta en evento de tiktok el cual esta en los mejores puesto."
            }
        ];

        let creators = [];

        // Load data from localStorage or fallback to defaults
        function loadData() {
            const saved = localStorage.getItem('tiktok_creators_data');
            if (saved) {
                try {
                    creators = JSON.parse(saved);
                } catch (e) {
                    creators = defaultCreators;
                }
            } else {
                creators = defaultCreators;
            }
            renderTable();
        }

        // Save data to localStorage
        function saveData() {
            localStorage.setItem('tiktok_creators_data', JSON.stringify(creators));
            const now = new Date().toLocaleTimeString();
            document.getElementById('lastSavedText').innerText = `Último cambio guardado: ${now}`;
        }

        // Render table rows
        function renderTable(dataToRender = creators) {
            const tbody = document.getElementById('creatorTableBody');
            tbody.innerHTML = '';

            document.getElementById('counterBadge').innerText = `Total: ${dataToRender.length} Creadores`;

            if (dataToRender.length === 0) {
                tbody.innerHTML = `<tr><td colspan="19" class="text-center py-12 text-slate-400"><i class="fa-solid fa-folder-open text-3xl mb-2"></i><p>No se encontraron creadores registrados.</p></td></tr>`;
                return;
            }

            dataToRender.forEach((creator, index) => {
                const tr = document.createElement('tr');
                tr.className = index % 2 === 0 ? 'bg-white hover:bg-slate-50 transition-colors' : 'bg-slate-50/60 hover:bg-slate-100/60 transition-colors';

                // Status badge styling
                let statusColor = 'bg-slate-100 text-slate-700';
                if (creator.estatusMejora === 'Mejorado') statusColor = 'bg-emerald-100 text-emerald-800 border border-emerald-200';
                else if (creator.estatusMejora === 'En Revisión') statusColor = 'bg-amber-100 text-amber-800 border border-amber-200';
                else if (creator.estatusMejora === 'Pendiente') statusColor = 'bg-rose-100 text-rose-800 border border-rose-200';

                tr.innerHTML = `
                    <!-- General Info -->
                    <td class="py-2.5 px-3 border-r border-slate-200 font-medium text-slate-900">
                        <input type="text" value="${escapeHtml(creator.equipo)}" onchange="updateField(${index}, 'equipo', this.value)" class="bg-transparent border-b border-transparent hover:border-slate-300 focus:border-indigo-500 focus:bg-white px-1 py-0.5 rounded w-24 outline-none transition">
                    </td>
                    <td class="py-2.5 px-3 border-r border-slate-200 text-slate-700">
                        <input type="text" value="${escapeHtml(creator.manager)}" onchange="updateField(${index}, 'manager', this.value)" class="bg-transparent border-b border-transparent hover:border-slate-300 focus:border-indigo-500 focus:bg-white px-1 py-0.5 rounded w-28 outline-none transition">
                    </td>
                    <td class="py-2.5 px-3 border-r border-slate-200 text-slate-600 font-mono text-[11px]">
                        <input type="text" value="${escapeHtml(creator.uid)}" onchange="updateField(${index}, 'uid', this.value)" class="bg-transparent border-b border-transparent hover:border-slate-300 focus:border-indigo-500 focus:bg-white px-1 py-0.5 rounded w-20 outline-none transition">
                    </td>
                    <td class="py-2.5 px-3 border-r border-slate-200 font-semibold text-indigo-600">
                        <input type="text" value="${escapeHtml(creator.handle)}" onchange="updateField(${index}, 'handle', this.value)" class="bg-transparent border-b border-transparent hover:border-slate-300 focus:border-indigo-500 focus:bg-white px-1 py-0.5 rounded w-32 outline-none transition">
                    </td>
                    <td class="py-2.5 px-3 border-r border-slate-200 text-slate-700">
                        <input type="text" value="${escapeHtml(creator.categoria)}" onchange="updateField(${index}, 'categoria', this.value)" class="bg-transparent border-b border-transparent hover:border-slate-300 focus:border-indigo-500 focus:bg-white px-1 py-0.5 rounded w-28 outline-none transition">
                    </td>
                    <td class="py-2.5 px-3 border-r border-slate-200">
                        <select onchange="updateField(${index}, 'estadoActividad', this.value)" class="bg-slate-100 text-slate-700 text-xs px-2 py-1 rounded border border-slate-200 outline-none focus:ring-1 focus:ring-indigo-500">
                            <option value="Activo" ${creator.estadoActividad === 'Activo' ? 'selected' : ''}>Activo</option>
                            <option value="Inactivo" ${creator.estadoActividad === 'Inactivo' ? 'selected' : ''}>Inactivo</option>
                            <option value="Nuevo" ${creator.estadoActividad === 'Nuevo' ? 'selected' : ''}>Nuevo</option>
                            <option value="En Pausa" ${creator.estadoActividad === 'En Pausa' ? 'selected' : ''}>En Pausa</option>
                        </select>
                    </td>
                    <td class="py-2.5 px-3 border-r border-slate-200 text-slate-500">
                        <input type="date" value="${creator.fechaRevision || ''}" onchange="updateField(${index}, 'fechaRevision', this.value)" class="bg-transparent text-xs outline-none focus:bg-white px-1 py-0.5 rounded border border-transparent hover:border-slate-300">
                    </td>

                    <!-- Pre-Transmisión Checkboxes -->
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-amber-50/20">
                        <div class="flex items-center justify-center gap-1">
                            ${creator.imagen ? `<img src="${creator.imagen}" alt="Thumb" class="w-7 h-7 rounded object-cover border border-slate-300 shadow-sm" onerror="this.src='https://placehold.co/50x50/cbd5e1/475569?text=LIVE'">` : '<span class="text-[10px] text-slate-400">Sin foto</span>'}
                            <input type="checkbox" ${creator.pre.capPantalla ? 'checked' : ''} onchange="updateCheck(${index}, 'pre', 'capPantalla', this.checked)" class="w-4 h-4 text-indigo-600 rounded border-slate-300 focus:ring-indigo-500 cursor-pointer">
                        </div>
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-amber-50/20">
                        <input type="checkbox" ${creator.pre.encuadre ? 'checked' : ''} onchange="updateCheck(${index}, 'pre', 'encuadre', this.checked)" class="w-4 h-4 text-indigo-600 rounded border-slate-300 focus:ring-indigo-500 cursor-pointer">
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-amber-50/20">
                        <input type="checkbox" ${creator.pre.iluminacion ? 'checked' : ''} onchange="updateCheck(${index}, 'pre', 'iluminacion', this.checked)" class="w-4 h-4 text-indigo-600 rounded border-slate-300 focus:ring-indigo-500 cursor-pointer">
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-amber-50/20">
                        <input type="checkbox" ${creator.pre.musicaFondo ? 'checked' : ''} onchange="updateCheck(${index}, 'pre', 'musicaFondo', this.checked)" class="w-4 h-4 text-indigo-600 rounded border-slate-300 focus:ring-indigo-500 cursor-pointer">
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-amber-50/20">
                        <input type="checkbox" ${creator.pre.entorno ? 'checked' : ''} onchange="updateCheck(${index}, 'pre', 'entorno', this.checked)" class="w-4 h-4 text-indigo-600 rounded border-slate-300 focus:ring-indigo-500 cursor-pointer">
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-amber-50/20">
                        <input type="checkbox" ${creator.pre.disposicion ? 'checked' : ''} onchange="updateCheck(${index}, 'pre', 'disposicion', this.checked)" class="w-4 h-4 text-indigo-600 rounded border-slate-300 focus:ring-indigo-500 cursor-pointer">
                    </td>

                    <!-- Primeros 30s Checkboxes -->
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-sky-50/20">
                        <input type="checkbox" ${creator.post30.hookInicial ? 'checked' : ''} onchange="updateCheck(${index}, 'post30', 'hookInicial', this.checked)" class="w-4 h-4 text-sky-600 rounded border-slate-300 focus:ring-sky-500 cursor-pointer">
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-sky-50/20">
                        <input type="checkbox" ${creator.post30.proposito ? 'checked' : ''} onchange="updateCheck(${index}, 'post30', 'proposito', this.checked)" class="w-4 h-4 text-sky-600 rounded border-slate-300 focus:ring-sky-500 cursor-pointer">
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-sky-50/20">
                        <input type="checkbox" ${creator.post30.llamadoAccion ? 'checked' : ''} onchange="updateCheck(${index}, 'post30', 'llamadoAccion', this.checked)" class="w-4 h-4 text-sky-600 rounded border-slate-300 focus:ring-sky-500 cursor-pointer">
                    </td>
                    <td class="py-2.5 px-2 border-r border-slate-200 text-center bg-sky-50/20">
                        <input type="checkbox" ${creator.post30.temaLive ? 'checked' : ''} onchange="updateCheck(${index}, 'post30', 'temaLive', this.checked)" class="w-4 h-4 text-sky-600 rounded border-slate-300 focus:ring-sky-500 cursor-pointer">
                    </td>

                    <!-- Resultado y Feedback -->
                    <td class="py-2.5 px-3 border-r border-slate-200 bg-emerald-50/10">
                        <select onchange="updateField(${index}, 'estatusMejora', this.value); renderTable();" class="text-xs font-semibold px-2.5 py-1 rounded-md ${statusColor} outline-none cursor-pointer">
                            <option value="Mejorado" ${creator.estatusMejora === 'Mejorado' ? 'selected' : ''}>Mejorado</option>
                            <option value="En Revisión" ${creator.estatusMejora === 'En Revisión' ? 'selected' : ''}>En Revisión</option>
                            <option value="Pendiente" ${creator.estatusMejora === 'Pendiente' ? 'selected' : ''}>Pendiente</option>
                        </select>
                    </td>
                    <td class="py-2.5 px-3 bg-emerald-50/10">
                        <textarea rows="2" onchange="updateField(${index}, 'feedback', this.value)" class="w-full min-w-[260px] bg-transparent border border-transparent hover:border-slate-300 focus:border-indigo-500 focus:bg-white text-xs p-1.5 rounded outline-none resize-y transition">${escapeHtml(creator.feedback || '')}</textarea>
                    </td>

                    <!-- Acciones -->
                    <td class="py-2.5 px-3 text-center">
                        <div class="flex items-center justify-center gap-2">
                            <button onclick="editCreator(${index})" class="text-slate-400 hover:text-indigo-600 transition p-1" title="Editar detalles"><i class="fa-solid fa-pen-to-square"></i></button>
                            <button onclick="deleteCreator(${index})" class="text-slate-400 hover:text-red-600 transition p-1" title="Eliminar creador"><i class="fa-solid fa-trash-can"></i></button>
                        </div>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        // Helper to update inline text or select fields
        function updateField(index, field, value) {
            creators[index][field] = value;
            saveData();
        }

        // Helper to update checkboxes
        function updateCheck(index, category, subfield, value) {
            creators[index][category][subfield] = value;
            saveData();
        }

        // Open modal for adding new creator
        function openAddModal() {
            document.getElementById('modalTitle').innerHTML = '<i class="fa-solid fa-user-plus mr-2"></i> Agregar Nuevo Creador';
            document.getElementById('creatorIndex').value = '-1';
            document.getElementById('creatorForm').reset();
            document.getElementById('inputFechaRevision').valueAsDate = new Date();
            document.getElementById('creatorModal').classList.remove('hidden');
        }

        // Open modal for editing creator details
        function editCreator(index) {
            const c = creators[index];
            document.getElementById('modalTitle').innerHTML = '<i class="fa-solid fa-user-pen mr-2"></i> Editar Creador';
            document.getElementById('creatorIndex').value = index;
            document.getElementById('inputEquipo').value = c.equipo;
            document.getElementById('inputManager').value = c.manager;
            document.getElementById('inputUid').value = c.uid;
            document.getElementById('inputHandle').value = c.handle;
            document.getElementById('inputCategoria').value = c.categoria;
            document.getElementById('inputEstadoActividad').value = c.estadoActividad;
            document.getElementById('inputFechaRevision').value = c.fechaRevision || '';
            document.getElementById('inputImagen').value = c.imagen || '';
            document.getElementById('inputFeedback').value = c.feedback || '';
            document.getElementById('creatorModal').classList.remove('hidden');
        }

        // Close modal
        function closeModal() {
            document.getElementById('creatorModal').classList.add('hidden');
        }

        // Save creator form submit
        function saveCreator(event) {
            event.preventDefault();
            const index = parseInt(document.getElementById('creatorIndex').value);
            
            const creatorData = {
                equipo: document.getElementById('inputEquipo').value,
                manager: document.getElementById('inputManager').value,
                uid: document.getElementById('inputUid').value,
                handle: document.getElementById('inputHandle').value,
                categoria: document.getElementById('inputCategoria').value,
                estadoActividad: document.getElementById('inputEstadoActividad').value,
                fechaRevision: document.getElementById('inputFechaRevision').value,
                imagen: document.getElementById('inputImagen').value,
                feedback: document.getElementById('inputFeedback').value,
                pre: index >= 0 ? creators[index].pre : { capPantalla: true, encuadre: false, iluminacion: false, musicaFondo: false, entorno: false, disposicion: false },
                post30: index >= 0 ? creators[index].post30 : { hookInicial: false, proposito: false, llamadoAccion: false, temaLive: false },
                estatusMejora: index >= 0 ? creators[index].estatusMejora : 'En Revisión'
            };

            if (index >= 0) {
                creators[index] = creatorData;
                showToast('Creador actualizado correctamente');
            } else {
                creators.unshift(creatorData);
                showToast('Nuevo creador agregado correctamente');
            }

            saveData();
            renderTable();
            closeModal();
        }

        // Delete creator
        function deleteCreator(index) {
            if (confirm('¿Está seguro de eliminar este registro de creador?')) {
                creators.splice(index, 1);
                saveData();
                renderTable();
                showToast('Creador eliminado');
            }
        }

        // Reset to default data
        function resetData() {
            if (confirm('¿Desea restaurar los datos de ejemplo predeterminados? Se perderán los cambios no exportados.')) {
                creators = [...defaultCreators];
                saveData();
                renderTable();
                showToast('Datos restaurados con éxito');
            }
        }

        // Search & Filter
        function filterTable() {
            const query = document.getElementById('searchInput').value.toLowerCase();
            const filtered = creators.filter(c => 
                c.handle.toLowerCase().includes(query) ||
                c.uid.toLowerCase().includes(query) ||
                c.manager.toLowerCase().includes(query) ||
                c.equipo.toLowerCase().includes(query) ||
                c.categoria.toLowerCase().includes(query)
            );
            renderTable(filtered);
        }

        // Export data as JSON file
        function exportData() {
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(creators, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", `seguimiento_creadores_${new Date().toISOString().slice(0,10)}.json`);
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
            showToast('Datos exportados con éxito');
        }

        // Import data from JSON file
        function importData(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const parsed = JSON.parse(e.target.result);
                    if (Array.isArray(parsed)) {
                        creators = parsed;
                        saveData();
                        renderTable();
                        showToast('Datos importados correctamente');
                    } else {
                        alert('El archivo JSON no tiene el formato correcto.');
                    }
                } catch (err) {
                    alert('Error al leer el archivo JSON.');
                }
            };
            reader.readAsText(file);
            event.target.value = '';
        }

        // Toast notification helper
        function showToast(message) {
            const toast = document.getElementById('toast');
            document.getElementById('toastMessage').innerText = message;
            toast.classList.remove('translate-y-20', 'opacity-0');
            setTimeout(() => {
                toast.classList.add('translate-y-20', 'opacity-0');
            }, 3000);
        }

        // HTML Escape helper
        function escapeHtml(text) {
            if (!text) return '';
            return text.replace(/"/g, '&quot;').replace(/'/g, '&#39;');
        }

        // Initialize on load
        window.onload = function() {
            loadData();
        };
    </script>
</body>
</html>
