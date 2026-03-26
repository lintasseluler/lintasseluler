lintasseluler

<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lintas Seluler - Price List & Kalkulator iPhone</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;900&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #050505; color: #f5f5f7; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.08); }
        .gradient-blue { background: linear-gradient(135deg, #0071e3 0%, #002f6c 100%); }
        select { appearance: none; background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke='white'%3E%3Cpath stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='current'/%3E%3C/svg%3E"); }
        .active-minus { background-color: #0071e3 !important; color: white !important; border-color: #0071e3 !important; }
    </style>
</head>
<body>
    <nav class="sticky top-0 z-50 glass px-6 py-4 flex justify-between items-center">
        <div class="flex items-center gap-2">
            <div class="bg-blue-600 w-8 h-8 rounded-lg flex items-center justify-center font-black italic shadow-lg shadow-blue-900/20">LS</div>
            <h1 class="font-bold tracking-tighter uppercase text-xs">Lintas Seluler</h1>
        </div>
        <div id="updateDate" class="text-[9px] font-bold opacity-40 uppercase tracking-widest text-right">Memuat Data...</div>
    </nav>

    <main class="max-w-5xl mx-auto px-6 py-10 md:py-20">
        <header class="text-center mb-16">
            <h2 class="text-5xl md:text-7xl font-black tracking-tighter leading-none mb-4 uppercase italic">Jual iPhone<br><span class="text-gray-600">Terima Tarikan</span></h2>
            <div class="flex items-center justify-center gap-2 text-blue-500">
                <i data-lucide="map-pin" size="14"></i>
                <p class="text-xs font-bold uppercase tracking-[0.3em] italic">Bekasi iPhone Specialist</p>
            </div>
        </header>

        <div class="grid lg:grid-cols-12 gap-6 items-start mb-24">
            <div class="lg:col-span-7 glass rounded-[2.5rem] p-8 space-y-10">
                <div>
                    <label class="text-[10px] font-black text-blue-500 mb-4 block tracking-widest uppercase opacity-80">1. Pilih Model iPhone</label>
                    <select id="modelSelect" class="w-full bg-transparent border-b border-white/10 py-4 text-xl font-bold outline-none cursor-pointer focus:border-blue-500 transition-colors">
                        </select>
                </div>

                <div>
                    <label class="text-[10px] font-black text-blue-500 mb-4 block tracking-widest uppercase opacity-80">2. Status IMEI</label>
                    <div class="grid grid-cols-2 gap-4">
                        <button id="btnResmi" onclick="toggleImei(false)" class="py-4 rounded-2xl border font-bold text-xs transition-all bg-white text-black border-white">Resmi Indo</button>
                        <button id="btnInter" onclick="toggleImei(true)" class="py-4 rounded-2xl border font-bold text-xs transition-all border-white/10 text-gray-500">All Provider</button>
                    </div>
                </div>

                <div>
                    <label class="text-[10px] font-black text-blue-500 mb-4 block tracking-widest uppercase opacity-80">3. Kondisi / Minus</label>
                    <div id="minusGrid" class="grid grid-cols-2 sm:grid-cols-3 gap-3">
                        </div>
                </div>
            </div>

            <div class="lg:col-span-5 lg:sticky lg:top-24">
                <div class="gradient-blue rounded-[3rem] p-10 text-white shadow-2xl relative overflow-hidden">
                    <div class="absolute -top-10 -right-10 w-40 h-40 bg-white/10 rounded-full blur-3xl"></div>
                    <p class="text-[10px] font-black uppercase tracking-[0.2em] mb-2 opacity-70">Estimasi Tertinggi</p>
                    <div class="flex items-baseline gap-2 mb-10">
                        <span class="text-xl font-bold opacity-50 font-mono">Rp</span>
                        <h3 id="displayPrice" class="text-6xl font-black tracking-tighter leading-none">0</h3>
                    </div>
                    
                    <div class="space-y-3">
                        <a id="waLink" href="#" target="_blank" class="w-full flex items-center justify-center gap-3 bg-white text-black py-5 rounded-[1.5rem] font-black text-sm shadow-xl active:scale-95 transition-all uppercase tracking-tight">
                            <i data-lucide="message-circle" size="20" fill="black"></i> Hubungi Lintas Seluler
                        </a>
                        <p class="text-[9px] text-center opacity-50 italic">Harga dapat berubah sesuai kondisi fisik saat cek unit.</p>
                    </div>
                </div>
            </div>
        </div>

        <section>
            <div class="flex justify-between items-end mb-10">
                <h3 class="text-3xl font-black tracking-tighter uppercase italic">Unit Ready<br><span class="text-blue-600 text-sm tracking-[0.5em] not-italic font-bold">STOK TERBATAS</span></h3>
            </div>
            <div id="katalogGrid" class="grid grid-cols-1 md:grid-cols-3 gap-6">
                </div>
        </section>
    </main>

    <footer class="py-20 text-center">
        <div class="opacity-20 text-[9px] font-black tracking-[0.5em] uppercase mb-4">Lintas Seluler ID • Bekasi Kota</div>
        <div class="flex justify-center gap-6 opacity-40">
            <i data-lucide="instagram" size="18"></i>
            <i data-lucide="smartphone" size="18"></i>
        </div>
    </footer>

    <script>
        let PRICES = {};
        let MINUS_VALS = [];
        let STOCK = [];
        let WA_NUM = "";
        let state = { model: "", isAllProv: false, minus: [] };

        async function init() {
            try {
                const res = await fetch('prices.json');
                const data = await res.json();
                
                PRICES = data.iphone_prices;
                MINUS_VALS = data.minus_settings;
                STOCK = data.stock_ready;
                WA_NUM = data.whatsapp_number;

                document.getElementById('updateDate').innerText = `Update ${data.last_update}`;
                
                // Load Dropdown
                const sel = document.getElementById('modelSelect');
                Object.keys(PRICES).forEach((m, i) => {
                    if(i === 0) state.model = m;
                    sel.add(new Option(m, m));
                });

                // Load Minus Buttons
                const mGrid = document.getElementById('minusGrid');
                MINUS_VALS.forEach(m => {
                    const b = document.createElement('button');
                    b.className = "p-4 rounded-2xl border border-white/5 bg-white/5 text-[10px] font-bold uppercase text-gray-500 text-left transition-all hover:bg-white/10";
                    b.innerHTML = `<div class="mb-1 text-white">${m.label}</div><div class="opacity-40">-${(m.cut/1000)}RB</div>`;
                    b.onclick = () => toggleMinus(m.id, b);
                    mGrid.appendChild(b);
                });

                renderKatalog();
                renderPrice();
                lucide.createIcons();

                // Select listener
                sel.onchange = (e) => { state.model = e.target.value; renderPrice(); };

            } catch (e) {
                console.error("Data gagal dimuat. Pastikan file prices.json tersedia.", e);
            }
        }

        function toggleImei(isInter) {
            state.isAllProv = isInter;
            document.getElementById('btnResmi').className = !isInter ? "py-4 rounded-2xl border font-bold text-xs transition-all bg-white text-black border-white" : "py-4 rounded-2xl border font-bold text-xs transition-all border-white/10 text-gray-500";
            document.getElementById('btnInter').className = isInter ? "py-4 rounded-2xl border font-bold text-xs transition-all bg-red-600 text-white border-red-600" : "py-4 rounded-2xl border font-bold text-xs transition-all border-white/10 text-gray-500";
            renderPrice();
        }

        function toggleMinus(id, el) {
            if(state.minus.includes(id)) {
                state.minus = state.minus.filter(x => x !== id);
                el.classList.remove('active-minus');
            } else {
                state.minus.push(id);
                el.classList.add('active-minus');
            }
            renderPrice();
        }

        function renderPrice() {
            let total = PRICES[state.model] || 0;
            if(state.isAllProv) total -= 800000; // Potongan All Provider (Inter)
            
            state.minus.forEach(mId => {
                const m = MINUS_VALS.find(x => x.id === mId);
                if(m) total -= m.cut;
            });

            const final = Math.max(0, total);
            document.getElementById('displayPrice').innerText = final.toLocaleString('id-ID');
            
            const msg = `Halo Lintas Seluler,%0A%0ASaya mau jual unit:%0A📱 Model: ${state.model}%0A🌍 IMEI: ${state.isAllProv ? 'All Provider (Inter)' : 'Resmi Indo'}%0A⚠️ Kondisi: ${state.minus.length > 0 ? state.minus.join(', ') : 'Mulus / Like New'}%0A%0AEstimasi Web: *Rp ${final.toLocaleString('id-ID')}*%0AApakah bisa dijemput ke lokasi saya?`;
            document.getElementById('waLink').href = `https://wa.me/${WA_NUM}?text=${msg}`;
        }

        function renderKatalog() {
            const kGrid = document.getElementById('katalogGrid');
            STOCK.forEach(s => {
                kGrid.innerHTML += `
                    <div class="glass p-8 rounded-[2rem] hover:border-blue-500/50 transition-all group">
                        <span class="text-[9px] font-black text-blue-400 bg-blue-400/10 px-3 py-1 rounded-full uppercase mb-6 inline-block">${s.tag}</span>
                        <h4 class="text-2xl font-black mb-2">${s.name}</h4>
                        <p class="text-xl font-medium text-gray-400 mb-8 tracking-tighter">Rp ${s.price}</p>
                        <a href="https://wa.me/${WA_NUM}?text=Cek stok unit: ${s.name}" class="w-full py-4 bg-white/5 text-white border border-white/10 rounded-2xl font-bold text-xs uppercase flex justify-center group-hover:bg-white group-hover:text-black transition-all italic">Ambil Unit Ini</a>
                    </div>`;
            });
        }

        init();
    </script>
</body>
</html>