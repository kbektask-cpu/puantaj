<!DOCTYPE html>
<html lang="tr" class="h-full bg-slate-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#2563eb">

    <!-- Standalone Mobile App (PWA) Configurations -->
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Puantaj">
    <meta name="application-name" content="Puantaj">
    <title>Mobil Puantaj & İş Takip</title>

    <!-- Web App Manifest (Inline) -->
    <link rel="manifest" href='data:application/manifest+json,{"name":"Mobil Puantaj & İş Takip","short_name":"Puantaj","start_url":".","display":"standalone","background_color":"#f8fafc","theme_color":"#2563eb","icons":[{"src":"https://img.icons8.com/color/512/planner.png","sizes":"512x512","type":"image/png"}]}'>

    <!-- App Favicon & Apple Touch Icon -->
    <link rel="apple-touch-icon" href="https://img.icons8.com/color/512/planner.png">
    <link rel="icon" type="image/png" href="https://img.icons8.com/color/512/planner.png">

    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: { sans: ['Inter', 'sans-serif'] },
                    colors: {
                        brand: {
                            50: '#eff6ff',
                            100: '#dbeafe',
                            500: '#2563eb',
                            600: '#1d4ed8',
                            700: '#1e40af',
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { 
            font-family: 'Inter', sans-serif; 
            -webkit-tap-highlight-color: transparent; 
            user-select: none;
            padding-top: env(safe-area-inset-top);
            padding-bottom: env(safe-area-inset-bottom);
        }
        .sticky-col { position: sticky; left: 0; z-index: 20; background-color: #ffffff; }
        .scroll-touch { -webkit-overflow-scrolling: touch; }
        @media print { .no-print { display: none !important; } body { background: white; padding: 0; } }
    </style>
</head>
<body class="h-full flex flex-col text-slate-800 bg-slate-50 antialiased pb-24 md:pb-6">

    <!-- Mobile Top Navigation Bar -->
    <header class="bg-white border-b border-slate-200/80 sticky top-0 z-30 px-4 py-3 shadow-sm flex items-center justify-between no-print">
        <div class="flex items-center gap-2.5">
            <div class="w-9 h-9 rounded-xl bg-brand-600 text-white flex items-center justify-center font-bold text-lg shadow-md">P</div>
            <div>
                <h1 class="text-xs font-semibold text-slate-400 uppercase tracking-wider leading-none">İŞ TAKİP / <span id="headerYear">2026</span></h1>
                <h2 class="text-base font-bold text-slate-900 mt-0.5 leading-tight">Günlük Puantaj</h2>
            </div>
        </div>

        <div class="flex items-center gap-2">
            <button onclick="saveDataManual()" type="button" class="bg-blue-600 active:bg-blue-700 text-white px-2.5 py-1.5 rounded-xl text-xs font-bold shadow-sm flex items-center gap-1 cursor-pointer">
                💾 Kaydet
            </button>
            <button onclick="shareData()" type="button" class="bg-indigo-600 active:bg-indigo-700 text-white px-2.5 py-1.5 rounded-xl text-xs font-bold shadow-sm flex items-center gap-1 cursor-pointer">
                🔗 Paylaş
            </button>
            <button onclick="downloadBackup()" type="button" class="bg-emerald-600 active:bg-emerald-700 text-white px-2.5 py-1.5 rounded-xl text-xs font-bold shadow-sm flex items-center gap-1 cursor-pointer">
                📥 Yedekle
            </button>
            <label class="bg-amber-600 active:bg-amber-700 text-white px-2.5 py-1.5 rounded-xl text-xs font-bold shadow-sm flex items-center gap-1 cursor-pointer">
                📤 Yükle <input type="file" id="backupFile" accept=".json" onchange="restoreBackup(event)" class="hidden">
            </label>
            <div class="flex items-center bg-slate-100 rounded-xl p-1 border border-slate-200/60">
                <button onclick="changeMonth(-1)" class="w-8 h-8 rounded-lg flex items-center justify-center text-slate-600 hover:bg-white cursor-pointer">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M15 19l-7-7 7-7"/></svg>
                </button>
                <span id="currentMonthDisplay" class="px-2 text-xs font-bold text-slate-800 min-w-[75px] text-center">Ekim 2026</span>
                <button onclick="changeMonth(1)" class="w-8 h-8 rounded-lg flex items-center justify-center text-slate-600 hover:bg-white cursor-pointer">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M9 5l7 7-7 7"/></svg>
                </button>
            </div>
        </div>
    </header>

    <!-- App Install Banner (App gibi yükleme bildirimi) -->
    <div id="pwaBanner" style="display: none;" class="bg-brand-600 text-white p-3 px-4 flex items-center justify-between no-print">
        <div class="flex items-center gap-2 text-xs font-medium">
            <span>📱 Uygulamayı telefonunuza ana ekran ikonu olarak yükleyebilirsiniz.</span>
        </div>
        <button onclick="dismissBanner()" class="bg-white/20 hover:bg-white/30 text-white px-2.5 py-1 rounded-lg text-xs font-bold">Tamam</button>
    </div>

    <!-- Main Container -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-3 sm:p-5 space-y-4">
        
        <!-- Hızlı Çalışan Ekle -->
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm space-y-3 no-print">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider">Hızlı Yeni Çalışan Ekle</h3>
            <div class="grid grid-cols-1 sm:grid-cols-3 gap-2">
                <input type="text" id="inputName" placeholder="Ad Soyad (Örn: Ahmet Yılmaz)" class="px-3 py-2 rounded-xl border border-slate-200 text-sm focus:outline-none focus:ring-2 focus:ring-brand-500">
                <input type="text" id="inputRole" placeholder="Görev / Unvan (Örn: Usta)" class="px-3 py-2 rounded-xl border border-slate-200 text-sm focus:outline-none focus:ring-2 focus:ring-brand-500">
                <div class="flex gap-2">
                    <input type="number" id="inputDailyWage" placeholder="Yevmiye (₺)" class="w-full px-3 py-2 rounded-xl border border-slate-200 text-sm focus:outline-none focus:ring-2 focus:ring-brand-500">
                    <button onclick="addWorkerDirect()" type="button" class="bg-brand-600 active:bg-brand-700 text-white font-bold px-5 py-2 rounded-xl text-xs whitespace-nowrap cursor-pointer shadow-sm">
                        Kayıt Ekle
                    </button>
                </div>
            </div>
        </div>

        <!-- KPI Cards -->
        <div class="grid grid-cols-2 lg:grid-cols-4 gap-2.5 sm:gap-4">
            <div class="bg-white p-3.5 sm:p-5 rounded-2xl border border-slate-200/70 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-[10px] sm:text-xs font-bold text-slate-400 uppercase">TOPLAM ÇALIŞAN</p>
                    <p id="kpiTotalEmployees" class="text-xl sm:text-3xl font-extrabold text-slate-900 mt-1">0</p>
                </div>
            </div>
            <div class="bg-white p-3.5 sm:p-5 rounded-2xl border border-slate-200/70 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-[10px] sm:text-xs font-bold text-slate-400 uppercase">ÇALIŞILAN GÜN</p>
                    <p id="kpiTotalDaysWorked" class="text-xl sm:text-3xl font-extrabold text-slate-900 mt-1">0</p>
                </div>
            </div>
            <div class="bg-white p-3.5 sm:p-5 rounded-2xl border border-slate-200/70 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-[10px] sm:text-xs font-bold text-slate-400 uppercase">TOPLAM AVANS</p>
                    <p id="kpiTotalAdvance" class="text-xl sm:text-3xl font-extrabold text-slate-900 mt-1">0 ₺</p>
                </div>
            </div>
            <div class="bg-white p-3.5 sm:p-5 rounded-2xl border border-slate-200/70 shadow-sm flex items-center justify-between">
                <div>
                    <p class="text-[10px] sm:text-xs font-bold text-slate-400 uppercase">TOPLAM MESAİ</p>
                    <p id="kpiTotalOvertime" class="text-xl sm:text-3xl font-extrabold text-slate-900 mt-1">0 Saat</p>
                </div>
            </div>
        </div>

        <div class="bg-white rounded-2xl border border-slate-200/70 shadow-sm overflow-hidden p-3.5 sm:p-5 space-y-3">
            <h3 class="text-base font-bold text-slate-900">Günlük giriş tablosu</h3>
            <div class="border border-slate-200/80 rounded-xl overflow-x-auto scroll-touch relative">
                <table class="w-full text-left border-collapse min-w-[900px]" id="attendanceTable">
                    <thead>
                        <tr class="bg-slate-50 border-b border-slate-200/80 text-slate-500 text-[11px]" id="tableHeaderRow"></tr>
                    </thead>
                    <tbody id="attendanceTbody" class="divide-y divide-slate-100 text-slate-700 text-xs"></tbody>
                </table>
            </div>
        </div>
    </main>

    <!-- Mobile Bottom Action Bar -->
    <div class="fixed bottom-0 left-0 right-0 bg-white/95 backdrop-blur-md border-t border-slate-200 p-2.5 z-30 flex items-center justify-around gap-2 no-print shadow-lg">
        <button onclick="exportCSV()" type="button" class="flex-1 bg-slate-100 text-slate-700 py-2.5 px-3 rounded-xl font-semibold text-xs flex items-center justify-center gap-1.5 border border-slate-200 cursor-pointer">
            <span>Excel / CSV İndir</span>
        </button>
        <button onclick="window.print()" type="button" class="bg-slate-100 text-slate-700 p-2.5 px-4 rounded-xl border border-slate-200 cursor-pointer text-xs font-semibold" title="Yazdır">
            Yazdır / PDF
        </button>
    </div>

    <!-- Modal: Overtime -->
    <div id="overtimeModal" style="display: none;" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 items-center justify-center p-4">
        <div class="bg-white rounded-2xl p-5 w-full max-w-xs shadow-2xl space-y-4">
            <h3 class="text-sm font-bold text-slate-900">Günlük Mesai Düzenle</h3>
            <div>
                <label class="block text-xs font-semibold text-slate-600 mb-1">Fazla Mesai Saati</label>
                <input type="number" id="inputOvertimeHours" min="0" max="24" step="0.5" class="w-full px-3 py-2 rounded-xl border border-slate-200 text-sm text-center font-bold">
            </div>
            <div class="flex gap-2">
                <button onclick="closeOvertimeModal()" type="button" class="flex-1 py-2 rounded-xl border border-slate-200 text-xs font-semibold text-slate-600 cursor-pointer">İptal</button>
                <button onclick="saveOvertime()" type="button" class="flex-1 py-2 rounded-xl bg-brand-600 text-white text-xs font-semibold cursor-pointer">Kaydet</button>
            </div>
        </div>
    </div>

    <!-- Script Application Logic -->
    <script>
        const STORAGE_KEY = 'PUANTAJ_APP_NATIVE_V1';
        let currentDate = new Date(2026, 9, 1);
        let activeOvertimeContext = null;

        let workers = JSON.parse(localStorage.getItem(STORAGE_KEY)) || [
            { id: '1', name: 'Bektaş Karabatak', role: 'Usta', dailyWage: 3200, advance: 30000, attendance: { 1: { present: true, overtime: 0 }, 2: { present: true, overtime: 0 } } }
        ];

        const dayNamesTR = ['Pz', 'Pt', 'Sa', 'Ça', 'Pe', 'Cu', 'Ct'];
        const monthNamesTR = ['Ocak', 'Şubat', 'Mart', 'Nisan', 'Mayıs', 'Haziran', 'Temmuz', 'Ağustos', 'Eylül', 'Ekim', 'Kasım', 'Aralık'];

        function save() {
            try { localStorage.setItem(STORAGE_KEY, JSON.stringify(workers)); } catch(e) {}
        }

        function saveDataManual() {
            save();
            showToast("Veriler başarıyla kaydedildi! 💾");
        }

        function showToast(message) {
            let toast = document.getElementById('customToast');
            if (!toast) {
                toast = document.createElement('div');
                toast.id = 'customToast';
                toast.className = 'fixed top-16 left-1/2 -translate-x-1/2 bg-emerald-600 text-white px-4 py-2 rounded-xl text-xs font-bold shadow-lg z-50 transition-all duration-300 opacity-0 pointer-events-none';
                document.body.appendChild(toast);
            }
            toast.textContent = message;
            toast.classList.remove('opacity-0');
            setTimeout(() => {
                toast.classList.add('opacity-0');
            }, 2000);
        }

        function shareData() {
            if (navigator.share) {
                navigator.share({
                    title: 'Puantaj Verileri',
                    text: 'Puantaj ve çalışan verileri yedeği',
                    url: window.location.href
                }).catch(err => console.log(err));
            } else {
                downloadBackup();
            }
        }

        function downloadBackup() {
            save();
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(workers, null, 2));
            const a = document.createElement('a');
            a.setAttribute("href", dataStr);
            a.setAttribute("download", "Puantaj_Yedek_" + new Date().toISOString().slice(0,10) + ".json");
            document.body.appendChild(a);
            a.click();
            a.remove();
        }

        function restoreBackup(e) {
            const file = e.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(evt) {
                try {
                    const parsed = JSON.parse(evt.target.result);
                    if (Array.isArray(parsed)) {
                        workers = parsed;
                        save();
                        init();
                        alert('Yedek veriler başarıyla yüklendi!');
                    } else {
                        alert('Yüklenen dosya geçersiz bir puantaj yedeğidir.');
                    }
                } catch(err) { alert('Dosya okunurken bir hata oluştu.'); }
            };
            reader.readAsText(file);
        }

        function init() {
            document.getElementById('currentMonthDisplay').textContent = `${monthNamesTR[currentDate.getMonth()]} ${currentDate.getFullYear()}`;
            document.getElementById('headerYear').textContent = currentDate.getFullYear();
            renderTable();
            updateKPIs();
            checkPwaStatus();
        }

        function checkPwaStatus() {
            const isStandalone = window.matchMedia('(display-mode: standalone)').matches || window.navigator.standalone;
            if (!isStandalone) {
                document.getElementById('pwaBanner').style.display = 'flex';
            }
        }

        function dismissBanner() {
            document.getElementById('pwaBanner').style.display = 'none';
        }

        function changeMonth(dir) {
            currentDate.setMonth(currentDate.getMonth() + dir);
            init();
        }

        function addWorkerDirect() {
            const name = document.getElementById('inputName').value.trim();
            const role = document.getElementById('inputRole').value.trim();
            const dailyWage = parseFloat(document.getElementById('inputDailyWage').value) || 0;

            if (!name || !role) { alert('Ad Soyad ve Görev zorunludur.'); return; }

            workers.push({ id: Date.now().toString(), name, role, dailyWage, advance: 0, attendance: {} });
            save();
            init();

            document.getElementById('inputName').value = '';
            document.getElementById('inputRole').value = '';
            document.getElementById('inputDailyWage').value = '';
        }

        function toggleAttendance(workerId, day) {
            const w = workers.find(i => i.id === workerId);
            if (!w) return;
            if (!w.attendance[day]) w.attendance[day] = { present: true, overtime: 0 };
            else w.attendance[day].present = !w.attendance[day].present;
            save();
            init();
        }

        function openOvertimeModal(workerId, day) {
            activeOvertimeContext = { workerId, day };
            const w = workers.find(i => i.id === workerId);
            const data = w && w.attendance[day] ? w.attendance[day].overtime : 0;
            document.getElementById('inputOvertimeHours').value = data;
            document.getElementById('overtimeModal').style.display = 'flex';
        }

        function closeOvertimeModal() {
            document.getElementById('overtimeModal').style.display = 'none';
        }

        function saveOvertime() {
            if (!activeOvertimeContext) return;
            const { workerId, day } = activeOvertimeContext;
            const w = workers.find(i => i.id === workerId);
            if (w) {
                const hours = parseFloat(document.getElementById('inputOvertimeHours').value) || 0;
                if (!w.attendance[day]) w.attendance[day] = { present: hours > 0, overtime: hours };
                else {
                    w.attendance[day].overtime = hours;
                    if (hours > 0) w.attendance[day].present = true;
                }
                save();
                init();
            }
            closeOvertimeModal();
        }

        function updateAdvance(workerId, val) {
            const w = workers.find(i => i.id === workerId);
            if (w) {
                w.advance = parseFloat(val) || 0;
                save();
                updateKPIs();
            }
        }

        function deleteWorker(workerId) {
            if (confirm("Silmek istediğinize emin misiniz?")) {
                workers = workers.filter(i => i.id !== workerId);
                save();
                init();
            }
        }

        function renderTable() {
            const year = currentDate.getFullYear();
            const month = currentDate.getMonth();
            const totalDays = new Date(year, month + 1, 0).getDate();
            const headerRow = document.getElementById('tableHeaderRow');
            const tbody = document.getElementById('attendanceTbody');

            let hHTML = `<th class="p-2.5 sticky-col shadow-[2px_0_5px_-2px_rgba(0,0,0,0.08)] min-w-[150px]">Çalışan</th>`;
            for (let i = 1; i <= totalDays; i++) {
                hHTML += `<th class="p-1 text-center border-l border-slate-200/60 min-w-[40px]"><div class="font-bold">${i}</div><div class="text-[10px] text-slate-400">${dayNamesTR[new Date(year, month, i).getDay()]}</div></th>`;
            }
            hHTML += `<th class="p-2 text-center border-l min-w-[60px]">Gün</th><th class="p-2 text-center border-l min-w-[65px]">Mesai</th><th class="p-2 text-center border-l min-w-[85px]">Avans</th><th class="p-2 text-right border-l min-w-[95px] pr-3">Net ₺</th><th class="p-2 border-l min-w-[40px] no-print"></th>`;
            headerRow.innerHTML = hHTML;

            tbody.innerHTML = '';
            if (workers.length === 0) {
                tbody.innerHTML = `<tr><td colspan="${totalDays + 6}" class="p-6 text-center text-slate-400">Kayıtlı çalışan yok.</td></tr>`;
                return;
            }

            workers.forEach(w => {
                let days = 0, overtime = 0;
                let rHTML = `<td class="p-2.5 sticky-col shadow-[2px_0_5px_-2px_rgba(0,0,0,0.08)] border-b"><div class="font-bold text-xs">${w.name}</div><div class="text-[10px] text-slate-400">${w.role} · ${w.dailyWage}₺</div></td>`;

                for (let i = 1; i <= totalDays; i++) {
                    const att = w.attendance[i] || { present: false, overtime: 0 };
                    if (att.present) days++;
                    if (att.overtime) overtime += parseFloat(att.overtime);
                    rHTML += `<td class="p-0.5 border-l border-b text-center"><button onclick="toggleAttendance('${w.id}', ${i})" ondblclick="openOvertimeModal('${w.id}', ${i})" type="button" class="w-8 h-8 rounded-lg mx-auto flex items-center justify-center font-bold text-xs cursor-pointer ${att.present ? 'bg-emerald-600 text-white' : 'bg-slate-100 text-slate-300'}">${att.present ? (att.overtime > 0 ? att.overtime + 's' : '✓') : '—'}</button></td>`;
                }

                const net = (days * w.dailyWage) + (overtime * (w.dailyWage / 8) * 1.5) - (w.advance || 0);
                rHTML += `<td class="p-2 text-center border-l border-b font-bold">${days}</td>`;
                rHTML += `<td class="p-2 text-center border-l border-b text-slate-600">${overtime}sa</td>`;
                rHTML += `<td class="p-1 border-l border-b"><input type="number" value="${w.advance || 0}" onchange="updateAdvance('${w.id}', this.value)" class="w-full text-center text-xs bg-slate-50 border rounded py-1"></td>`;
                rHTML += `<td class="p-2 text-right border-l border-b font-extrabold pr-3">${net.toLocaleString('tr-TR', {maximumFractionDigits:0})}₺</td>`;
                rHTML += `<td class="p-1 text-center border-l border-b no-print"><button onclick="deleteWorker('${w.id}')" type="button" class="text-slate-300 hover:text-rose-600 cursor-pointer">🗑️</button></td>`;

                const tr = document.createElement('tr');
                tr.innerHTML = rHTML;
                tbody.appendChild(tr);
            });
        }

        function updateKPIs() {
            document.getElementById('kpiTotalEmployees').textContent = workers.length;
            let dCount = 0, aTotal = 0, oTotal = 0;
            workers.forEach(w => {
                aTotal += (w.advance || 0);
                Object.values(w.attendance).forEach(a => {
                    if (a.present) dCount++;
                    if (a.overtime) oTotal += parseFloat(a.overtime);
                });
            });
            document.getElementById('kpiTotalDaysWorked').textContent = dCount;
            document.getElementById('kpiTotalAdvance').textContent = `${aTotal.toLocaleString('tr-TR')} ₺`;
            document.getElementById('kpiTotalOvertime').textContent = `${oTotal} Saat`;
        }

        function exportCSV() {
            let csv = "\uFEFFÇalışan;Unvan;Yevmiye;Toplam Gün;Toplam Avans;Net\n";
            workers.forEach(w => {
                csv += `"${w.name}";"${w.role}";${w.dailyWage};0;${w.advance || 0};0\n`;
            });
            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.setAttribute('download', 'Puantaj.csv');
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        window.onload = init;
    </script>
</body>
</html>
