```html
<!DOCTYPE html>
<html lang="pt-BR" class="dark h-full bg-slate-950 text-slate-100">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AnimaDex PT-BR - Biblioteca & Leitor Permanente</title>
  
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- Tailwind Configuration for Dark Theme -->
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            brand: {
              50: '#eef2ff',
              100: '#e0e7ff',
              400: '#818cf8',
              500: '#6366f1',
              600: '#4f46e5',
              700: '#4338ca',
              900: '#312e81',
            }
          }
        }
      }
    }
  </script>

  <!-- Google Fonts: Inter & Outfit -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Outfit:wght@500;600;700;800&display=swap" rel="stylesheet">
  
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  
  <!-- Dexie.js CDN for IndexedDB Storage -->
  <script src="https://unpkg.com/dexie@latest/dist/dexie.js"></script>

  <style>
    body {
      font-family: 'Inter', sans-serif;
    }
    h1, h2, h3, h4, .font-heading {
      font-family: 'Outfit', sans-serif;
    }
    /* Custom Scrollbar */
    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: #0f172a;
    }
    ::-webkit-scrollbar-thumb {
      background: #334155;
      border-radius: 4px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #475569;
    }
    .aspect-manga {
      aspect-ratio: 3 / 4.4;
    }
  </style>
</head>
<body class="h-full flex flex-col overflow-x-hidden bg-slate-950 text-slate-100">

  <div id="app" class="min-h-screen flex flex-col justify-between">
    
    <!-- HEADER NAVBAR -->
    <header class="sticky top-0 z-30 bg-slate-900/90 backdrop-blur-md border-b border-slate-800/80 px-4 lg:px-8 py-3.5 transition-all">
      <div class="max-w-7xl mx-auto flex items-center justify-between gap-4">
        
        <!-- Logo & Branding -->
        <div class="flex items-center gap-3 cursor-pointer" onclick="app.setCategoryFilter('todos')">
          <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-brand-600 to-indigo-400 flex items-center justify-center text-white shadow-lg shadow-brand-500/20">
            <i class="fa-solid fa-book-open text-xl"></i>
          </div>
          <div>
            <h1 class="text-xl font-bold tracking-tight bg-gradient-to-r from-white via-slate-200 to-indigo-300 bg-clip-text text-transparent">
              AnimaDex PT-BR
            </h1>
            <p class="text-xs text-slate-400 hidden sm:block">Armazenamento Permanente no Seu Navegador</p>
          </div>
        </div>

        <!-- Search Bar & Add Button -->
        <div class="flex items-center gap-3">
          <div class="relative hidden md:block w-64">
            <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 text-sm"></i>
            <input 
              type="text" 
              id="searchInput" 
              placeholder="Buscar por título..." 
              oninput="app.handleSearch(this.value)"
              class="w-full bg-slate-950 border border-slate-800 rounded-xl pl-9 pr-4 py-1.5 text-sm text-slate-200 placeholder-slate-500 focus:outline-none focus:border-brand-500 focus:ring-1 focus:ring-brand-500 transition"
            >
          </div>

          <button 
            onclick="app.openModal('createWorkModal')" 
            class="bg-brand-600 hover:bg-brand-500 active:scale-95 text-white font-medium px-4 py-2 rounded-xl flex items-center gap-2 text-sm shadow-lg shadow-brand-600/30 transition-all duration-200"
          >
            <i class="fa-solid fa-plus"></i>
            <span>Nova Obra</span>
          </button>
        </div>
      </div>
    </header>

    <!-- MAIN CONTAINER -->
    <main class="flex-grow max-w-7xl w-full mx-auto p-4 sm:p-6 lg:p-8">
      
      <!-- CONTROLS AND FILTER BAR -->
      <section id="libraryControls" class="mb-6 flex flex-col sm:flex-row items-stretch sm:items-center justify-between gap-4">
        
        <!-- Category Filter Tabs -->
        <div class="flex items-center gap-1 bg-slate-900 p-1.5 rounded-xl border border-slate-800 overflow-x-auto">
          <button onclick="app.setCategoryFilter('todos')" data-category="todos" class="category-btn active px-3.5 py-1.5 rounded-lg text-xs font-semibold whitespace-nowrap bg-brand-600 text-white transition-all">
            Todos (<span id="count-all">0</span>)
          </button>
          <button onclick="app.setCategoryFilter('Mangá')" data-category="Mangá" class="category-btn px-3.5 py-1.5 rounded-lg text-xs font-medium text-slate-400 hover:text-white transition-all">
            Mangás (<span id="count-manga">0</span>)
          </button>
          <button onclick="app.setCategoryFilter('Webtoon')" data-category="Webtoon" class="category-btn px-3.5 py-1.5 rounded-lg text-xs font-medium text-slate-400 hover:text-white transition-all">
            Webtoons (<span id="count-webtoon">0</span>)
          </button>
          <button onclick="app.setCategoryFilter('Animação / GIF')" data-category="Animação / GIF" class="category-btn px-3.5 py-1.5 rounded-lg text-xs font-medium text-slate-400 hover:text-white transition-all">
            Animações / GIFs (<span id="count-animation">0</span>)
          </button>
          <button onclick="app.setCategoryFilter('Desenho / Ilustração')" data-category="Desenho / Ilustração" class="category-btn px-3.5 py-1.5 rounded-lg text-xs font-medium text-slate-400 hover:text-white transition-all">
            Desenhos (<span id="count-drawing">0</span>)
          </button>
        </div>

        <!-- Storage Status Indicator -->
        <div class="flex items-center justify-between sm:justify-end gap-3 text-xs text-slate-400 bg-slate-900/80 px-3.5 py-2 rounded-xl border border-slate-800/80">
          <span class="flex items-center gap-2">
            <i class="fa-solid fa-hard-drive text-brand-400"></i>
            <span>Uso do Navegador:</span>
          </span>
          <span id="storageUsage" class="font-mono text-slate-200 font-medium">Calculando...</span>
        </div>
      </section>

      <!-- SEARCH INPUT FOR MOBILE -->
      <div class="md:hidden mb-6">
        <div class="relative">
          <i class="fa-solid fa-magnifying-glass absolute left-3 top-1/2 -translate-y-1/2 text-slate-400 text-sm"></i>
          <input 
            type="text" 
            placeholder="Buscar por título..." 
            oninput="app.handleSearch(this.value)"
            class="w-full bg-slate-900 border border-slate-800 rounded-xl pl-9 pr-4 py-2 text-sm text-slate-200 placeholder-slate-500 focus:outline-none focus:border-brand-500 transition"
          >
        </div>
      </div>

      <!-- LIBRARY GRID CONTAINER -->
      <section id="libraryGrid" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-6 gap-4 sm:gap-6">
        <!-- Dynamic Cards Inserted Here -->
      </section>

      <!-- EMPTY STATE -->
      <div id="emptyState" class="hidden flex-col items-center justify-center py-20 text-center">
        <div class="w-20 h-20 rounded-2xl bg-slate-900 flex items-center justify-center text-slate-600 mb-4 border border-slate-800 shadow-inner">
          <i class="fa-solid fa-book-bookmark text-3xl"></i>
        </div>
        <h3 class="text-lg font-bold text-slate-200 mb-1">Sua biblioteca está vazia</h3>
        <p class="text-sm text-slate-400 max-w-md mb-6">Você ainda não adicionou nenhum mangá, desenho ou animação. Suas obras ficarão salvas permanentemente no navegador.</p>
        <button 
          onclick="app.openModal('createWorkModal')" 
          class="bg-brand-600 hover:bg-brand-500 text-white font-medium px-5 py-2.5 rounded-xl flex items-center gap-2 text-sm shadow-lg shadow-brand-600/30 transition-all"
        >
          <i class="fa-solid fa-plus"></i>
          Adicionar Primeira Obra
        </button>
      </div>

    </main>

    <!-- FOOTER -->
    <footer class="bg-slate-900/50 border-t border-slate-800/80 py-6 text-center text-xs text-slate-500">
      <div class="max-w-7xl mx-auto px-4 flex flex-col sm:flex-row items-center justify-between gap-2">
        <p>AnimaDex &copy; 2026 - Leitor de Mangás e Animações Salvo Automaticamente no Navegador.</p>
        <p class="flex items-center gap-1.5 text-slate-400">
          <i class="fa-solid fa-shield-halved text-brand-400"></i>
          <span>Armazenamento local persistente via IndexedDB</span>
        </p>
      </div>
    </footer>
  </div>

  <!-- MODAL: ADD / CREATE WORK -->
  <div id="createWorkModal" class="fixed inset-0 z-50 hidden bg-slate-950/80 backdrop-blur-sm flex items-center justify-center p-4 overflow-y-auto">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl max-w-lg w-full p-6 shadow-2xl relative transition-all">
      
      <!-- Modal Header -->
      <div class="flex items-center justify-between pb-4 mb-4 border-b border-slate-800">
        <h3 class="text-lg font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-folder-plus text-brand-500"></i>
          Cadastrar Nova Obra
        </h3>
        <button onclick="app.closeModal('createWorkModal')" class="text-slate-400 hover:text-white transition">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <!-- Modal Form -->
      <form id="createWorkForm" onsubmit="app.handleCreateWork(event)" class="space-y-4">
        
        <!-- Title Input -->
        <div>
          <label class="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-1.5">Título da Obra *</label>
          <input 
            type="text" 
            id="workTitle" 
            required 
            placeholder="Ex: Solo Leveling, Minha Animação #1, Naruto..."
            class="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-2.5 text-sm text-slate-100 placeholder-slate-500 focus:outline-none focus:border-brand-500 transition"
          >
        </div>

        <!-- Category Select -->
        <div>
          <label class="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-1.5">Categoria / Tipo *</label>
          <select 
            id="workCategory" 
            required 
            class="w-full bg-slate-950 border border-slate-800 rounded-xl px-4 py-2.5 text-sm text-slate-100 focus:outline-none focus:border-brand-500 transition"
          >
            <option value="Mangá">Mangá</option>
            <option value="Webtoon">Webtoon</option>
            <option value="Animação / GIF">Animação / GIF</option>
            <option value="Desenho / Ilustração">Desenho / Ilustração</option>
          </select>
        </div>

        <!-- Cover Upload Input -->
        <div>
          <label class="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-1.5">Capa do Mangá/Obra *</label>
          <div class="flex items-center gap-4">
            <div id="coverPreviewBox" class="w-20 h-28 bg-slate-950 border border-slate-800 rounded-xl flex flex-col items-center justify-center text-slate-600 overflow-hidden relative">
              <i class="fa-regular fa-image text-2xl" id="coverIcon"></i>
              <img id="coverImgPreview" class="w-full h-full object-cover hidden" alt="Prévia da Capa">
            </div>
            <div class="flex-grow">
              <label class="cursor-pointer bg-slate-800 hover:bg-slate-700 text-slate-200 text-xs font-medium px-4 py-2.5 rounded-xl border border-slate-700 flex items-center justify-center gap-2 transition">
                <i class="fa-solid fa-upload"></i>
                <span>Escolher Imagem de Capa</span>
                <input type="file" id="coverInput" accept="image/*" class="hidden" onchange="app.handleCoverSelect(event)" required>
              </label>
              <p class="text-[11px] text-slate-500 mt-2">Suporta PNG, JPG, WEBP e GIFs animados.</p>
            </div>
          </div>
        </div>

        <!-- Initial Pages Upload -->
        <div>
          <label class="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-1.5">Enviar Páginas / Imagens em Lote</label>
          <label class="cursor-pointer bg-slate-950 border border-dashed border-slate-800 hover:border-brand-500/50 p-4 rounded-xl flex flex-col items-center justify-center text-center transition group">
            <i class="fa-solid fa-file-arrow-up text-2xl text-slate-500 group-hover:text-brand-400 mb-1 transition"></i>
            <span class="text-xs text-slate-300 font-medium">Clique para selecionar múltiplas imagens/páginas</span>
            <span class="text-[11px] text-slate-500 mt-1" id="selectedPagesCount">Nenhum arquivo selecionado</span>
            <input type="file" id="initialPagesInput" accept="image/*" multiple class="hidden" onchange="app.handleInitialPagesSelect(event)">
          </label>
        </div>

        <!-- Modal Actions -->
        <div class="flex items-center justify-end gap-3 pt-4 border-t border-slate-800">
          <button 
            type="button" 
            onclick="app.closeModal('createWorkModal')" 
            class="px-4 py-2 text-xs font-semibold text-slate-400 hover:text-white transition"
          >
            Cancelar
          </button>
          <button 
            type="submit" 
            id="btnSaveWork"
            class="bg-brand-600 hover:bg-brand-500 text-white text-xs font-semibold px-5 py-2.5 rounded-xl flex items-center gap-2 transition"
          >
            <i class="fa-solid fa-floppy-disk"></i>
            <span>Salvar Permanentemente</span>
          </button>
        </div>

      </form>
    </div>
  </div>

  <!-- MODAL: ADD MORE PAGES / EDIT PAGES -->
  <div id="addPagesModal" class="fixed inset-0 z-50 hidden bg-slate-950/80 backdrop-blur-sm flex items-center justify-center p-4 overflow-y-auto">
    <div class="bg-slate-900 border border-slate-800 rounded-2xl max-w-xl w-full p-6 shadow-2xl relative">
      <div class="flex items-center justify-between pb-4 mb-4 border-b border-slate-800">
        <h3 class="text-lg font-bold text-white flex items-center gap-2">
          <i class="fa-solid fa-images text-brand-400"></i>
          Gerenciar Páginas - <span id="addPagesWorkTitle" class="text-brand-400"></span>
        </h3>
        <button onclick="app.closeModal('addPagesModal')" class="text-slate-400 hover:text-white transition">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <form id="addPagesForm" onsubmit="app.handleUploadMorePages(event)" class="space-y-4">
        <input type="hidden" id="addPagesWorkId">

        <div>
          <label class="block text-xs font-semibold text-slate-300 uppercase tracking-wider mb-1.5">Adicionar Mais Páginas/Imagens *</label>
          <label class="cursor-pointer bg-slate-950 border border-dashed border-slate-800 hover:border-brand-500/50 p-6 rounded-xl flex flex-col items-center justify-center text-center transition group">
            <i class="fa-solid fa-photo-film text-3xl text-slate-500 group-hover:text-brand-400 mb-2 transition"></i>
            <span class="text-sm text-slate-200 font-medium">Clique para selecionar novas páginas</span>
            <span class="text-xs text-slate-500 mt-1" id="addPagesSelectedStatus">Você pode selecionar várias páginas de uma só vez</span>
            <input type="file" id="morePagesInput" accept="image/*" multiple class="hidden" onchange="app.handleMorePagesSelect(event)" required>
          </label>
        </div>

        <div class="flex items-center justify-end gap-3 pt-4 border-t border-slate-800">
          <button type="button" onclick="app.closeModal('addPagesModal')" class="px-4 py-2 text-xs font-semibold text-slate-400 hover:text-white transition">
            Cancelar
          </button>
          <button type="submit" id="btnAddPagesSubmit" class="bg-brand-600 hover:bg-brand-500 text-white text-xs font-semibold px-5 py-2.5 rounded-xl flex items-center gap-2 transition">
            <i class="fa-solid fa-cloud-arrow-up"></i>
            <span>Adicionar Páginas</span>
          </button>
        </div>
      </form>

      <!-- Page List / Management Section -->
      <div class="mt-6 pt-4 border-t border-slate-800">
        <h4 class="text-xs font-semibold text-slate-300 uppercase tracking-wider mb-3">Páginas Atuais Salvas</h4>
        <div id="pagesManagerList" class="grid grid-cols-4 sm:grid-cols-6 gap-2.5 max-h-48 overflow-y-auto p-1 bg-slate-950/60 rounded-xl border border-slate-800">
          <!-- Page Thumbnails for deletion inserted dynamically -->
        </div>
      </div>
    </div>
  </div>

  <!-- FULLSCREEN READER VIEW MODAL -->
  <div id="readerModal" class="fixed inset-0 z-50 hidden bg-slate-950 text-slate-100 flex flex-col">
    
    <!-- Reader Top Navbar -->
    <header id="readerHeader" class="bg-slate-900/90 backdrop-blur-md border-b border-slate-800 px-4 py-2.5 flex items-center justify-between gap-4 transition-all duration-300">
      <div class="flex items-center gap-3">
        <button onclick="app.closeReader()" class="w-9 h-9 rounded-xl bg-slate-800 hover:bg-slate-700 flex items-center justify-center text-slate-200 transition" title="Fechar Leitor (Esc)">
          <i class="fa-solid fa-arrow-left"></i>
        </button>
        <div>
          <h2 id="readerTitle" class="text-sm sm:text-base font-bold text-white line-clamp-1">Título do Mangá</h2>
          <p id="readerCategory" class="text-[11px] text-slate-400">Categoria</p>
        </div>
      </div>

      <!-- Reader Controls -->
      <div class="flex items-center gap-2 sm:gap-3">
        
        <!-- View Mode Selector -->
        <div class="bg-slate-950 p-1 rounded-xl border border-slate-800 flex items-center text-xs">
          <button id="btnModePage" onclick="app.setReaderMode('paged')" class="px-2.5 py-1 rounded-lg text-slate-400 hover:text-white transition font-medium">
            <i class="fa-solid fa-file-lines sm:mr-1"></i>
            <span class="hidden sm:inline">Página a Página</span>
          </button>
          <button id="btnModeScroll" onclick="app.setReaderMode('scroll')" class="px-2.5 py-1 rounded-lg text-slate-400 hover:text-white transition font-medium">
            <i class="fa-solid fa-scroll sm:mr-1"></i>
            <span class="hidden sm:inline">Modo Webtoon</span>
          </button>
        </div>

        <!-- Add Pages Shortcut Button -->
        <button onclick="app.openAddPagesModalCurrent()" class="hidden sm:flex bg-slate-800 hover:bg-slate-700 text-xs px-3 py-1.5 rounded-xl border border-slate-700 items-center gap-1.5 transition">
          <i class="fa-solid fa-plus text-brand-400"></i>
          <span>Mais Páginas</span>
        </button>

        <!-- Fullscreen Toggle -->
        <button onclick="app.toggleFullscreen()" class="w-9 h-9 rounded-xl bg-slate-800 hover:bg-slate-700 flex items-center justify-center text-slate-200 transition" title="Tela Cheia">
          <i class="fa-solid fa-expand"></i>
        </button>
      </div>
    </header>

    <!-- Reader Main Content Area -->
    <div id="readerBody" class="flex-grow overflow-auto relative flex justify-center items-center bg-slate-950 select-none">
      
      <!-- PAGED MODE CONTAINER -->
      <div id="pagedView" class="w-full h-full flex flex-col items-center justify-center relative p-2">
        <div class="relative max-w-full max-h-full flex items-center justify-center">
          <img id="pagedImg" class="max-h-[calc(100vh-120px)] max-w-full object-contain shadow-2xl rounded-md" alt="Página Atual">
        </div>
        
        <!-- Navigation Overlay Buttons for Paged Mode -->
        <button onclick="app.prevPage()" class="absolute left-2 top-1/2 -translate-y-1/2 w-12 h-20 sm:w-16 sm:h-28 bg-slate-900/40 hover:bg-slate-900/80 backdrop-blur-sm rounded-r-2xl border-y border-r border-slate-700/50 flex items-center justify-center text-slate-200 text-xl transition opacity-40 hover:opacity-100">
          <i class="fa-solid fa-chevron-left"></i>
        </button>
        <button onclick="app.nextPage()" class="absolute right-2 top-1/2 -translate-y-1/2 w-12 h-20 sm:w-16 sm:h-28 bg-slate-900/40 hover:bg-slate-900/80 backdrop-blur-sm rounded-l-2xl border-y border-l border-slate-700/50 flex items-center justify-center text-slate-200 text-xl transition opacity-40 hover:opacity-100">
          <i class="fa-solid fa-chevron-right"></i>
        </button>
      </div>

      <!-- SCROLL WEBTOON MODE CONTAINER -->
      <div id="scrollView" class="hidden w-full h-full overflow-y-auto p-0 flex flex-col items-center">
        <div id="scrollPagesList" class="max-w-3xl w-full flex flex-col items-center">
          <!-- Dynamic Scroll Pages -->
        </div>
      </div>

      <!-- NO PAGES WARNING -->
      <div id="noPagesWarning" class="hidden flex-col items-center justify-center text-center p-6">
        <i class="fa-regular fa-folder-open text-4xl text-slate-600 mb-3"></i>
        <h3 class="text-base font-bold text-slate-300 mb-1">Esta obra ainda não possui páginas salvas</h3>
        <p class="text-xs text-slate-500 mb-4">Adicione páginas para poder realizar a leitura.</p>
        <button onclick="app.openAddPagesModalCurrent()" class="bg-brand-600 hover:bg-brand-500 text-white text-xs font-semibold px-4 py-2 rounded-xl flex items-center gap-2">
          <i class="fa-solid fa-plus"></i>
          Enviar Páginas
        </button>
      </div>

    </div>

    <!-- Reader Bottom Controls Toolbar -->
    <footer id="readerFooter" class="bg-slate-900/90 backdrop-blur-md border-t border-slate-800 px-4 py-2.5 flex items-center justify-between gap-4">
      
      <!-- Previous Page Button -->
      <button onclick="app.prevPage()" id="btnPrevPageFooter" class="px-3.5 py-1.5 rounded-xl bg-slate-800 hover:bg-slate-700 text-xs font-medium text-slate-200 flex items-center gap-1.5 transition">
        <i class="fa-solid fa-chevron-left text-[10px]"></i>
        <span class="hidden sm:inline">Anterior</span>
      </button>

      <!-- Progress Slider & Label -->
      <div class="flex-grow max-w-md flex items-center gap-3">
        <span id="pageIndicator" class="text-xs font-mono text-slate-300 font-semibold whitespace-nowrap">0 / 0</span>
        <input 
          type="range" 
          id="pageRangeInput" 
          min="1" 
          value="1" 
          oninput="app.goToPage(parseInt(this.value))" 
          class="w-full h-1.5 bg-slate-800 rounded-lg appearance-none cursor-pointer accent-brand-500"
        >
      </div>

      <!-- Next Page Button -->
      <button onclick="app.nextPage()" id="btnNextPageFooter" class="px-3.5 py-1.5 rounded-lg bg-slate-800 hover:bg-slate-700 text-xs font-medium text-slate-200 flex items-center gap-1.5 transition">
        <span class="hidden sm:inline">Próxima</span>
        <i class="fa-solid fa-chevron-right text-[10px]"></i>
      </button>

    </footer>

  </div>

  <!-- JAVASCRIPT APP ENGINE -->
  <script>
    /**
     * DEXIE.JS INDEXEDDB DATABASE SETUP
     * Provides permanent client-side storage for images/GIFs as raw Blob objects.
     */
    const db = new Dexie("AnimaDexDB");
    db.version(1).stores({
      works: '++id, title, category, createdAt',
      pages: '++id, workId, pageIndex'
    });

    /**
     * MAIN APPLICATION CLASS
     */
    class AnimaDexApp {
      constructor() {
        this.currentFilter = 'todos';
        this.searchQuery = '';
        this.currentWork = null;
        this.currentPages = [];
        this.currentPageIndex = 0;
        this.readerMode = 'paged'; // 'paged' or 'scroll'
        this.tempCoverBlob = null;
        this.tempInitialPagesBlobs = [];
        this.tempMorePagesBlobs = [];

        this.init();
      }

      async init() {
        this.bindEvents();
        await this.loadLibrary();
        await this.updateStorageUsage();
      }

      bindEvents() {
        // Keyboard Hotkeys for Reader
        window.addEventListener('keydown', (e) => {
          const readerModal = document.getElementById('readerModal');
          if (!readerModal.classList.contains('hidden')) {
            if (e.key === 'ArrowRight' || e.key === 'PageDown' || e.key === ' ') {
              this.nextPage();
            } else if (e.key === 'ArrowLeft' || e.key === 'PageUp') {
              this.prevPage();
            } else if (e.key === 'Escape') {
              this.closeReader();
            }
          }
        });
      }

      /**
       * Loads all works from Dexie IndexedDB and updates the UI grid.
       */
      async loadLibrary() {
        try {
          let collection = db.works.reverse();
          let works = await collection.toArray();

          // Calculate page count for each work
          for (let work of works) {
            work.pageCount = await db.pages.where('workId').equals(work.id).count();
          }

          // Update Filter Badges Count
          this.updateCategoryCounts(works);

          // Apply Category Filter
          if (this.currentFilter !== 'todos') {
            works = works.filter(w => w.category === this.currentFilter);
          }

          // Apply Search Filter
          if (this.searchQuery.trim() !== '') {
            const query = this.searchQuery.toLowerCase();
            works = works.filter(w => w.title.toLowerCase().includes(query));
          }

          this.renderGrid(works);
        } catch (err) {
          console.error("Erro ao carregar acervo do IndexedDB:", err);
        }
      }

      updateCategoryCounts(works) {
        document.getElementById('count-all').textContent = works.length;
        document.getElementById('count-manga').textContent = works.filter(w => w.category === 'Mangá').length;
        document.getElementById('count-webtoon').textContent = works.filter(w => w.category === 'Webtoon').length;
        document.getElementById('count-animation').textContent = works.filter(w => w.category === 'Animação / GIF').length;
        document.getElementById('count-drawing').textContent = works.filter(w => w.category === 'Desenho / Ilustração').length;
      }

      /**
       * Renders the cards grid dynamically
       */
      renderGrid(works) {
        const grid = document.getElementById('libraryGrid');
        const emptyState = document.getElementById('emptyState');

        grid.innerHTML = '';

        if (works.length === 0) {
          grid.classList.add('hidden');
          emptyState.classList.remove('hidden');
          emptyState.classList.add('flex');
          return;
        }

        grid.classList.remove('hidden');
        emptyState.classList.add('hidden');
        emptyState.classList.remove('flex');

        works.forEach(work => {
          // Create URL from stored Cover Blob
          const coverUrl = URL.createObjectURL(work.coverBlob);

          const card = document.createElement('div');
          card.className = "group bg-slate-900 border border-slate-800 rounded-2xl overflow-hidden shadow-lg hover:border-brand-500/50 hover:shadow-brand-500/10 transition-all duration-300 flex flex-col";

          card.innerHTML = `
            <div class="relative aspect-manga overflow-hidden bg-slate-950 cursor-pointer" onclick="app.openReader(${work.id})">
              <img src="${coverUrl}" class="w-full h-full object-cover group-hover:scale-105 transition duration-500" alt="${escapeHtml(work.title)}">
              
              <!-- Tag Badge -->
              <span class="absolute top-2.5 left-2.5 bg-slate-950/80 backdrop-blur-md border border-slate-800 text-slate-200 text-[10px] font-bold uppercase tracking-wider px-2 py-0.5 rounded-md">
                ${work.category}
              </span>

              <!-- Overlay Hover Buttons -->
              <div class="absolute inset-0 bg-slate-950/60 opacity-0 group-hover:opacity-100 transition-opacity duration-300 flex flex-col items-center justify-center gap-2 p-3 text-center">
                <button 
                  onclick="event.stopPropagation(); app.openReader(${work.id})" 
                  class="w-full bg-brand-600 hover:bg-brand-500 text-white font-semibold text-xs py-2 rounded-xl shadow-lg flex items-center justify-center gap-1.5 transition transform translate-y-2 group-hover:translate-y-0"
                >
                  <i class="fa-solid fa-book-open"></i>
                  <span>Ler Agora</span>
                </button>
                <button 
                  onclick="event.stopPropagation(); app.openAddPagesModal(${work.id}, '${escapeHtml(work.title)}')" 
                  class="w-full bg-slate-800/90 hover:bg-slate-700 text-slate-200 font-semibold text-xs py-1.5 rounded-xl flex items-center justify-center gap-1.5 border border-slate-700 transition"
                >
                  <i class="fa-solid fa-plus text-brand-400"></i>
                  <span>Gerenciar Páginas</span>
                </button>
              </div>
            </div>

            <!-- Card Info -->
            <div class="p-3.5 flex flex-col justify-between flex-grow">
              <div>
                <h4 class="font-bold text-sm text-slate-100 group-hover:text-brand-400 transition line-clamp-1" title="${escapeHtml(work.title)}">
                  ${escapeHtml(work.title)}
                </h4>
                <p class="text-[11px] text-slate-400 mt-1 flex items-center gap-1">
                  <i class="fa-solid fa-layer-group text-[10px]"></i>
                  <span>${work.pageCount} ${work.pageCount === 1 ? 'Página / Imagem' : 'Páginas / Imagens'}</span>
                </p>
              </div>

              <div class="mt-3 pt-2 border-t border-slate-800/80 flex items-center justify-between text-xs text-slate-500">
                <button onclick="app.deleteWork(${work.id})" class="hover:text-rose-400 transition p-1" title="Excluir Obra">
                  <i class="fa-regular fa-trash-can"></i>
                </button>
                <button onclick="app.openReader(${work.id})" class="text-brand-400 font-semibold hover:underline text-[11px]">
                  Abrir &rarr;
                </button>
              </div>
            </div>
          `;

          grid.appendChild(card);
        });
      }

      setCategoryFilter(category) {
        this.currentFilter = category;
        document.querySelectorAll('.category-btn').forEach(btn => {
          if (btn.dataset.category === category) {
            btn.classList.add('bg-brand-600', 'text-white');
            btn.classList.remove('text-slate-400');
          } else {
            btn.classList.remove('bg-brand-600', 'text-white');
            btn.classList.add('text-slate-400');
          }
        });
        this.loadLibrary();
      }

      handleSearch(query) {
        this.searchQuery = query;
        this.loadLibrary();
      }

      handleCoverSelect(e) {
        const file = e.target.files[0];
        if (file) {
          this.tempCoverBlob = file;
          const previewImg = document.getElementById('coverImgPreview');
          const previewIcon = document.getElementById('coverIcon');

          previewImg.src = URL.createObjectURL(file);
          previewImg.classList.remove('hidden');
          previewIcon.classList.add('hidden');
        }
      }

      handleInitialPagesSelect(e) {
        this.tempInitialPagesBlobs = Array.from(e.target.files);
        const countSpan = document.getElementById('selectedPagesCount');
        countSpan.textContent = `${this.tempInitialPagesBlobs.length} arquivo(s) selecionado(s)`;
      }

      handleMorePagesSelect(e) {
        this.tempMorePagesBlobs = Array.from(e.target.files);
        const statusSpan = document.getElementById('addPagesSelectedStatus');
        statusSpan.textContent = `${this.tempMorePagesBlobs.length} arquivo(s) pronto(s) para serem salvos.`;
      }

      /**
       * Saves new work and its initial pages into IndexedDB permanently.
       */
      async handleCreateWork(e) {
        e.preventDefault();
        
        const title = document.getElementById('workTitle').value.trim();
        const category = document.getElementById('workCategory').value;

        if (!title || !this.tempCoverBlob) {
          alert('Por favor, informe o título e selecione uma imagem de capa.');
          return;
        }

        const btnSave = document.getElementById('btnSaveWork');
        btnSave.disabled = true;
        btnSave.innerHTML = `<i class="fa-solid fa-spinner fa-spin"></i> Salvando no Navegador...`;

        try {
          // 1. Store Work record in IndexedDB
          const workId = await db.works.add({
            title: title,
            category: category,
            coverBlob: this.tempCoverBlob,
            createdAt: new Date().getTime()
          });

          // 2. Store Pages records in IndexedDB
          if (this.tempInitialPagesBlobs.length > 0) {
            const pageEntries = this.tempInitialPagesBlobs.map((blob, idx) => ({
              workId: workId,
              pageIndex: idx,
              imageBlob: blob
            }));
            await db.pages.bulkAdd(pageEntries);
          }

          this.closeModal('createWorkModal');
          this.resetForm('createWorkForm');
          await this.loadLibrary();
          await this.updateStorageUsage();
        } catch (err) {
          console.error("Erro ao salvar no IndexedDB:", err);
          alert('Ocorreu um erro ao salvar os arquivos localmente.');
        } finally {
          btnSave.disabled = false;
          btnSave.innerHTML = `<i class="fa-solid fa-floppy-disk"></i><span>Salvar Permanentemente</span>`;
        }
      }

      /**
       * Opens manage pages modal for specific work.
       */
      async openAddPagesModal(workId, workTitle) {
        document.getElementById('addPagesWorkId').value = workId;
        document.getElementById('addPagesWorkTitle').textContent = workTitle;
        this.tempMorePagesBlobs = [];
        document.getElementById('addPagesSelectedStatus').textContent = 'Você pode selecionar várias páginas de uma só vez';
        
        await this.loadPagesManagerList(workId);
        this.openModal('addPagesModal');
      }

      openAddPagesModalCurrent() {
        if (this.currentWork) {
          this.openAddPagesModal(this.currentWork.id, this.currentWork.title);
        }
      }

      async loadPagesManagerList(workId) {
        const managerList = document.getElementById('pagesManagerList');
        managerList.innerHTML = '';

        const pages = await db.pages.where('workId').equals(workId).sortBy('pageIndex');

        if (pages.length === 0) {
          managerList.innerHTML = `<span class="col-span-full text-center text-xs text-slate-500 py-3">Nenhuma página adicionada ainda.</span>`;
          return;
        }

        pages.forEach((page, idx) => {
          const imgUrl = URL.createObjectURL(page.imageBlob);
          const thumb = document.createElement('div');
          thumb.className = "relative aspect-manga bg-slate-900 rounded-lg overflow-hidden border border-slate-800 group";
          thumb.innerHTML = `
            <img src="${imgUrl}" class="w-full h-full object-cover">
            <span class="absolute bottom-1 left-1 bg-slate-950/80 px-1 py-0.2 rounded text-[9px] font-mono text-slate-300">${idx + 1}</span>
            <button onclick="app.deletePage(${page.id}, ${workId})" class="absolute top-1 right-1 bg-rose-600/90 text-white w-5 h-5 rounded flex items-center justify-center text-[10px] opacity-0 group-hover:opacity-100 transition">
              <i class="fa-solid fa-xmark"></i>
            </button>
          `;
          managerList.appendChild(thumb);
        });
      }

      async deletePage(pageId, workId) {
        await db.pages.delete(pageId);
        await this.loadPagesManagerList(workId);
        await this.loadLibrary();
        await this.updateStorageUsage();

        if (this.currentWork && this.currentWork.id === workId) {
          await this.openReader(workId);
        }
      }

      async handleUploadMorePages(e) {
        e.preventDefault();
        const workId = parseInt(document.getElementById('addPagesWorkId').value);

        if (!this.tempMorePagesBlobs || this.tempMorePagesBlobs.length === 0) {
          alert('Selecione ao menos uma imagem/página.');
          return;
        }

        const btnSubmit = document.getElementById('btnAddPagesSubmit');
        btnSubmit.disabled = true;
        btnSubmit.innerHTML = `<i class="fa-solid fa-spinner fa-spin"></i> Salvando...`;

        try {
          const currentCount = await db.pages.where('workId').equals(workId).count();

          const pageEntries = this.tempMorePagesBlobs.map((blob, idx) => ({
            workId: workId,
            pageIndex: currentCount + idx,
            imageBlob: blob
          }));

          await db.pages.bulkAdd(pageEntries);

          this.closeModal('addPagesModal');
          await this.loadLibrary();
          await this.updateStorageUsage();

          // Refresh Reader if open
          if (this.currentWork && this.currentWork.id === workId) {
            await this.openReader(workId);
          }
        } catch (err) {
          console.error("Erro ao adicionar mais páginas:", err);
        } finally {
          btnSubmit.disabled = false;
          btnSubmit.innerHTML = `<i class="fa-solid fa-cloud-arrow-up"></i><span>Adicionar Páginas</span>`;
        }
      }

      async deleteWork(workId) {
        if (confirm("Tem certeza de que deseja excluir esta obra e todas as suas páginas permanentemente?")) {
          await db.works.delete(workId);
          await db.pages.where('workId').equals(workId).delete();
          await this.loadLibrary();
          await this.updateStorageUsage();
        }
      }

      /**
       * Opens the fullscreen Reader
       */
      async openReader(workId) {
        const work = await db.works.get(workId);
        if (!work) return;

        this.currentWork = work;
        this.currentPages = await db.pages.where('workId').equals(workId).sortBy('pageIndex');
        this.currentPageIndex = 0;

        document.getElementById('readerTitle').textContent = work.title;
        document.getElementById('readerCategory').textContent = work.category;

        const readerModal = document.getElementById('readerModal');
        readerModal.classList.remove('hidden');

        if (this.currentPages.length === 0) {
          document.getElementById('pagedView').classList.add('hidden');
          document.getElementById('scrollView').classList.add('hidden');
          document.getElementById('noPagesWarning').classList.remove('hidden');
          document.getElementById('noPagesWarning').classList.add('flex');
          this.updatePageIndicator(0, 0);
          return;
        }

        document.getElementById('noPagesWarning').classList.add('hidden');
        document.getElementById('noPagesWarning').classList.remove('flex');

        this.renderReader();
      }

      setReaderMode(mode) {
        this.readerMode = mode;
        const btnPage = document.getElementById('btnModePage');
        const btnScroll = document.getElementById('btnModeScroll');

        if (mode === 'paged') {
          btnPage.classList.add('bg-brand-600', 'text-white');
          btnPage.classList.remove('text-slate-400');
          btnScroll.classList.remove('bg-brand-600', 'text-white');
          btnScroll.classList.add('text-slate-400');
        } else {
          btnScroll.classList.add('bg-brand-600', 'text-white');
          btnScroll.classList.remove('text-slate-400');
          btnPage.classList.remove('bg-brand-600', 'text-white');
          btnPage.classList.add('text-slate-400');
        }

        this.renderReader();
      }

      renderReader() {
        if (this.currentPages.length === 0) return;

        const pagedView = document.getElementById('pagedView');
        const scrollView = document.getElementById('scrollView');

        if (this.readerMode === 'paged') {
          scrollView.classList.add('hidden');
          pagedView.classList.remove('hidden');
          pagedView.classList.add('flex');
          this.showPagedImage();
        } else {
          pagedView.classList.add('hidden');
          pagedView.classList.remove('flex');
          scrollView.classList.remove('hidden');
          this.renderScrollPages();
        }

        this.updatePageIndicator(this.currentPageIndex + 1, this.currentPages.length);
      }

      showPagedImage() {
        const page = this.currentPages[this.currentPageIndex];
        if (!page) return;

        const img = document.getElementById('pagedImg');
        img.src = URL.createObjectURL(page.imageBlob);
        this.updatePageIndicator(this.currentPageIndex + 1, this.currentPages.length);
      }

      renderScrollPages() {
        const listContainer = document.getElementById('scrollPagesList');
        listContainer.innerHTML = '';

        this.currentPages.forEach((page, idx) => {
          const imgUrl = URL.createObjectURL(page.imageBlob);
          const imgEl = document.createElement('img');
          imgEl.src = imgUrl;
          imgEl.className = "w-full h-auto object-contain border-b border-slate-900/60";
          imgEl.loading = "lazy";
          imgEl.alt = `Página ${idx + 1}`;
          listContainer.appendChild(imgEl);
        });
      }

      nextPage() {
        if (this.currentPageIndex < this.currentPages.length - 1) {
          this.currentPageIndex++;
          this.showPagedImage();
        }
      }

      prevPage() {
        if (this.currentPageIndex > 0) {
          this.currentPageIndex--;
          this.showPagedImage();
        }
      }

      goToPage(pageNum) {
        if (pageNum >= 1 && pageNum <= this.currentPages.length) {
          this.currentPageIndex = pageNum - 1;
          this.showPagedImage();
        }
      }

      updatePageIndicator(current, total) {
        document.getElementById('pageIndicator').textContent = `${current} / ${total}`;
        const rangeInput = document.getElementById('pageRangeInput');
        rangeInput.max = total || 1;
        rangeInput.value = current;
      }

      closeReader() {
        document.getElementById('readerModal').classList.add('hidden');
        this.currentWork = null;
        this.currentPages = [];
      }

      toggleFullscreen() {
        const readerElem = document.getElementById('readerModal');
        if (!document.fullscreenElement) {
          readerElem.requestFullscreen().catch(err => {
            console.error(`Erro ao ativar tela cheia: ${err.message}`);
          });
        } else {
          document.exitFullscreen();
        }
      }

      async updateStorageUsage() {
        if (navigator.storage && navigator.storage.estimate) {
          const estimate = await navigator.storage.estimate();
          const usedMB = (estimate.usage / (1024 * 1024)).toFixed(1);
          document.getElementById('storageUsage').textContent = `${usedMB} MB Utilizados`;
        } else {
          document.getElementById('storageUsage').textContent = 'IndexedDB Ativo';
        }
      }

      openModal(modalId) {
        document.getElementById(modalId).classList.remove('hidden');
      }

      closeModal(modalId) {
        document.getElementById(modalId).classList.add('hidden');
      }

      resetForm(formId) {
        document.getElementById(formId).reset();
        this.tempCoverBlob = null;
        this.tempInitialPagesBlobs = [];
        document.getElementById('coverImgPreview').classList.add('hidden');
        document.getElementById('coverIcon').classList.remove('hidden');
        document.getElementById('selectedPagesCount').textContent = 'Nenhum arquivo selecionado';
      }
    }

    // Helper Utility for HTML escaping
    function escapeHtml(str) {
      return str.replace(/[&<>"']/g, function(m) {
        return {
          '&': '&amp;',
          '<': '&lt;',
          '>': '&gt;',
          '"': '&quot;',
          "'": '&#039;'
        }[m];
      });
    }

    // Global Instance Initialization
    let app;
    window.addEventListener('DOMContentLoaded', () => {
      app = new AnimaDexApp();
    });
  </script>
</body>
</html>
```
