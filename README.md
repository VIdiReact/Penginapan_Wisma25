<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Room Monitor - Penginapan</title>
    <style>
        :root {
            --bg-body: #f0f2f5;
            --color-kosong: #4CAF50;
            --color-terisi: #f44336;
            --color-ac-mati: #ff9800;
            --color-kotor: #e91e63;
            --color-belum-ready: #ffeb3b;
            --shadow: 0 4px 12px rgba(0,0,0,0.08);
        }

        body {
            font-family: 'Segoe UI', Tahoma, sans-serif;
            background-color: var(--bg-body);
            margin: 0;
            padding: 0;
        }

        /* --- STICKY HEADER --- */
        header {
            position: sticky;
            top: 0;
            z-index: 100;
            background-color: white;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px 40px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        .header-left {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .header-logo {
            width: 45px;
            height: 45px;
            border-radius: 8px;
        }

        header h1 {
            margin: 0;
            font-size: 1.6rem;
            color: #1a1a1a;
        }

        .header-right p {
            margin: 0;
            color: #666;
            font-weight: 500;
        }

        /* --- MAIN CONTENT --- */
        .main-content {
            padding: 40px;
        }

        .container {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
            gap: 25px;
            max-width: 1400px;
            margin: 0 auto;
        }

        /* Room Card Style */
        .room-card {
            background-color: white;
            border-radius: 16px;
            padding: 30px;
            text-align: center;
            box-shadow: var(--shadow);
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
            border: 3px solid transparent;
        }

        .room-card:hover {
            transform: translateY(-8px);
            box-shadow: 0 12px 20px rgba(0,0,0,0.15);
        }

        .room-number {
            font-size: 2.8rem;
            font-weight: 800;
            margin: 0;
            color: #333;
        }

        .room-status {
            margin-top: 15px;
            font-weight: 700;
            padding: 8px 16px;
            border-radius: 10px;
            font-size: 0.8rem;
            display: inline-block;
            text-transform: uppercase;
        }

        /* Status Colors */
        .status-kosong { border-color: var(--color-kosong); }
        .status-kosong .room-status { background-color: var(--color-kosong); color: white; }

        .status-terisi { border-color: var(--color-terisi); }
        .status-terisi .room-status { background-color: var(--color-terisi); color: white; }

        .status-ac { border-color: var(--color-ac-mati); }
        .status-ac .room-status { background-color: var(--color-ac-mati); color: white; }

        .status-kotor { border-color: var(--color-kotor); }
        .status-kotor .room-status { background-color: var(--color-kotor); color: white; }

        .status-ready { border-color: var(--color-belum-ready); }
        .status-ready .room-status { background-color: var(--color-belum-ready); color: #333; }

        /* --- POP UP / MODAL --- */
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.5);
            justify-content: center;
            align-items: center;
            backdrop-filter: blur(5px);
        }

        .modal-content {
            background-color: #ffffff;
            padding: 30px;
            border-radius: 24px;
            width: 90%;
            max-width: 380px;
            text-align: center;
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
            animation: modalPop 0.3s ease-out;
        }

        @keyframes modalPop {
            from { transform: scale(0.8); opacity: 0; }
            to { transform: scale(1); opacity: 1; }
        }

        .status-options {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-top: 20px;
        }

        .status-btn {
            border: none;
            padding: 16px;
            border-radius: 12px;
            font-size: 1rem;
            font-weight: 700;
            cursor: pointer;
            color: white;
            transition: all 0.2s ease;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
        }

        /* Efek Membesar Saat Hover di Pop-up */
        .status-btn:hover {
            transform: scale(1.06);
            filter: brightness(1.1);
            box-shadow: 0 6px 15px rgba(0,0,0,0.2);
        }

        .btn-kosong { background-color: var(--color-kosong); }
        .btn-terisi { background-color: var(--color-terisi); }
        .btn-ac { background-color: var(--color-ac-mati); }
        .btn-kotor { background-color: var(--color-kotor); }
        .btn-ready { background-color: var(--color-belum-ready); color: #333; }

        .cancel-btn {
            margin-top: 20px;
            background: none;
            border: 2px solid #ddd;
            color: #888;
            width: 100%;
            padding: 12px;
            border-radius: 12px;
            cursor: pointer;
        }
    </style>
</head>
<body>

<header>
    <div class="header-left">
        <img src="https://placehold.co/100x100/4CAF50/ffffff?text=W&font=roboto" alt="Logo" class="header-logo">
        <h1>Room Monitor</h1>
    </div>
    <div class="header-right">
        <p>Kelola hunian dan status kamar Anda secara real-time</p>
    </div>
</header>

<div class="main-content">
    <div class="container" id="roomContainer"></div>
</div>

<div class="modal" id="statusModal">
    <div class="modal-content">
        <h3 id="modalTitle">Pilih Status</h3>
        <div class="status-options">
            <button class="status-btn btn-kosong" onclick="updateStatus('Kosong')">Kosong</button>
            <button class="status-btn btn-terisi" onclick="updateStatus('Terisi')">Terisi</button>
            <button class="status-btn btn-ac" onclick="updateStatus('AC belum mati')">AC belum mati</button>
            <button class="status-btn btn-kotor" onclick="updateStatus('Kotor')">Kotor</button>
            <button class="status-btn btn-ready" onclick="updateStatus('Belum Ready')">Belum Ready</button>
        </div>
        <button class="cancel-btn" onclick="closeModal()">Batal</button>
    </div>
</div>

<script>
    const totalRooms = 24;
    const storageKey = 'WismaRoomData';
    let currentRoomIndex = null;
    let rooms = [];

    // --- FUNGSI UTAMA PENYIMPANAN ---
    function loadData() {
        const saved = localStorage.getItem(storageKey);
        if (saved) {
            // Jika ada data di memory, ambil data tersebut
            rooms = JSON.parse(saved);
        } else {
            // Jika tidak ada (pertama kali buka), buat data baru
            for (let i = 1; i <= totalRooms; i++) {
                rooms.push({
                    number: i.toString().padStart(2, '0'),
                    status: 'Kosong'
                });
            }
            saveToStorage();
        }
    }

    function saveToStorage() {
        // Simpan data ke memori browser (localStorage)
        localStorage.setItem(storageKey, JSON.stringify(rooms));
    }

    function render() {
        const container = document.getElementById('roomContainer');
        container.innerHTML = '';
        
        rooms.forEach((room, index) => {
            const card = document.createElement('div');
            card.className = `room-card ${getStatusClass(room.status)}`;
            card.innerHTML = `
                <div class="room-number">${room.number}</div>
                <div class="room-status">${room.status}</div>
            `;
            card.onclick = () => openModal(index);
            container.appendChild(card);
        });
    }

    // --- LOGIKA STATUS & MODAL ---
    function getStatusClass(status) {
        const map = {
            'Kosong': 'status-kosong',
            'Terisi': 'status-terisi',
            'AC belum mati': 'status-ac',
            'Kotor': 'status-kotor',
            'Belum Ready': 'status-ready'
        };
        return map[status] || '';
    }

    function openModal(index) {
        currentRoomIndex = index;
        document.getElementById('modalTitle').innerText = `Update Kamar ${rooms[index].number}`;
        document.getElementById('statusModal').style.display = 'flex';
    }

    function closeModal() {
        document.getElementById('statusModal').style.display = 'none';
    }

    function updateStatus(newStatus) {
        rooms[currentRoomIndex].status = newStatus;
        saveToStorage(); // Langsung simpan setelah update
        render();        // Gambar ulang tampilan
        closeModal();
    }

    // Inisialisasi
    window.onclick = (e) => { if (e.target.className === 'modal') closeModal(); }
    loadData();
    render();
</script>

</body>
</html>
