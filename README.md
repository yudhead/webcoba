<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>INVENTORY REPORT ESKOTA</title>
    <!-- Library untuk membaca file Excel -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        /* General Body Styles */
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f4f4f9;
            color: #333;
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
        }

        /* Header Styles */
        h1, h2 {
            color: #2c3e50;
            text-align: center;
            border-bottom: 2px solid #e0e0e0;
            padding-bottom: 10px;
            margin-bottom: 30px;
        }

        /* Container Styles */
        .container {
            background-color: #ffffff;
            padding: 20px 30px;
            border-radius: 8px;
            margin-bottom: 30px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.05);
        }

        /* Form Element Styles */
        .form-group {
            margin-bottom: 15px;
            position: relative; /* Needed for autocomplete */
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="file"], input[type="number"], input[type="text"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            box-sizing: border-box; /* Ensures padding doesn't affect width */
        }
        input[type="submit"], .calc-button {
            background-color: #3498db;
            color: white;
            padding: 12px 25px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            font-weight: bold;
            transition: background-color 0.3s, transform 0.2s;
            width: 100%;
            margin-top: 10px;
        }
        input[type="submit"]:hover, .calc-button:hover {
            background-color: #2980b9;
            transform: translateY(-2px);
        }
        
        /* Autocomplete Styles */
        .autocomplete-items {
            position: absolute;
            border: 1px solid #d4d4d4;
            border-bottom: none;
            border-top: none;
            z-index: 99;
            top: 100%;
            left: 0;
            right: 0;
            max-height: 200px;
            overflow-y: auto;
        }
        .autocomplete-items div {
            padding: 10px;
            cursor: pointer;
            background-color: #fff; 
            border-bottom: 1px solid #d4d4d4; 
        }
        .autocomplete-items div:hover {
            background-color: #e9e9e9; 
        }
        .autocomplete-active {
            background-color: DodgerBlue !important; 
            color: #ffffff; 
        }

        /* Loading Spinner */
        .loader {
            border: 5px solid #f3f3f3;
            border-top: 5px solid #3498db;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 20px auto;
            display: none; /* Hidden by default */
        }
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Report and Opname Section Styles */
        .main-content {
            display: none; /* Hidden by default */
        }
        .category {
            margin-bottom: 20px;
            padding: 20px;
            border-left: 5px solid;
            border-radius: 0 8px 8px 0;
        }
        .urgent { border-color: #e74c3c; background-color: #fffafa; }
        .limit { border-color: #f39c12; background-color: #fffcf5; }
        .mendekati { border-color: #f1c40f; background-color: #fffefa; }
        
        .category h3 {
            margin-top: 0;
            font-size: 20px;
        }
        ul { list-style-type: none; padding: 0; }
        li {
            background-color: #ecf0f1;
            padding: 12px 15px;
            margin-bottom: 8px;
            border-radius: 4px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 16px;
        }
        li strong { color: #2c3e50; }

        /* Manual Stock Styles */
        .manual-stock-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px 20px;
        }
        .manual-stock-item {
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .manual-stock-item label {
            margin-bottom: 0;
            margin-right: 10px;
            flex-grow: 1;
        }
        .manual-stock-item input {
            width: 80px;
        }

        /* History Table Styles */
        #opnameHistory table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        #opnameHistory th, #opnameHistory td {
            border: 1px solid #ddd;
            padding: 10px;
            text-align: left;
        }
        #opnameHistory th {
            background-color: #ecf0f1;
            color: #2c3e50;
        }
        #opnameHistory td { background-color: #fff; }
        .selisih-plus { color: green; font-weight: bold; }
        .selisih-minus { color: red; font-weight: bold; }

        /* WhatsApp Button Styles */
        .wa-button {
            display: inline-block;
            background-color: #25D366;
            color: white;
            padding: 15px 30px;
            text-decoration: none;
            border-radius: 50px;
            font-weight: bold;
            font-size: 18px;
            transition: transform 0.2s, box-shadow 0.2s;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            margin-top: 20px;
            cursor: pointer;
            border: none;
        }
        .wa-button:hover {
            transform: scale(1.05);
            box-shadow: 0 6px 12px rgba(0,0,0,0.15);
        }
        
        /* Custom Modal Styles */
        .modal {
            display: none; 
            position: fixed; 
            z-index: 100; 
            left: 0; 
            top: 0; 
            width: 100%; 
            height: 100%; 
            overflow: auto; 
            background-color: rgba(0,0,0,0.5);
        }
        .modal-content {
            background-color: #fefefe;
            margin: 15% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 90%;
            max-width: 400px;
            text-align: center;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.3);
        }
        .modal-content p {
            font-size: 18px;
            line-height: 1.5;
        }
        .modal-buttons button {
            margin: 10px;
            padding: 10px 20px;
        }
    </style>
</head>
<body>

    <h1>📊 INVENTORY REPORT ESKOTA</h1>

    <div class="container upload-section">
        <h2>Unggah File Stok Saat Ini</h2>
        <form id="uploadForm">
            <div class="form-group">
                <label for="stokFile">Pilih File Stok Anda (.xlsx/.csv)</label>
                <input type="file" id="stokFile" name="stokFile" accept=".csv, .xlsx, application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel" required>
            </div>
            <input type="submit" value="Proses File">
        </form>
    </div>

    <div class="loader" id="loader"></div>

    <div class="main-content" id="mainContent">
        <div class="container report-section">
            <h2>Laporan Stok Otomatis</h2>
            <div class="category urgent" id="urgentCategory">
                <h3>URGENT ‼️</h3>
                <ul id="urgentList"></ul>
            </div>
            <div class="category limit" id="limitCategory">
                <h3>LIMIT ⚠️</h3>
                <ul id="limitList"></ul>
            </div>
            <div class="category mendekati" id="mendekatiCategory">
                <h3>MENDEKATI LIMIT 📈</h3>
                <ul id="mendekatiList"></ul>
            </div>
        </div>

        <div class="container manual-stock-section">
            <h2>Stok Manual</h2>
            <div id="manualStockList" class="manual-stock-grid">
                <!-- Manual stock items will be injected here by JavaScript -->
            </div>
        </div>
        
        <div style="text-align: center;">
             <button id="waButton" class="wa-button">Kirim Laporan via WhatsApp</button>
        </div>

        <div class="container opname-section">
            <h2>Kalkulator Stok Opname Manual</h2>
            <form id="opnameForm" autocomplete="off">
                <div class="form-group">
                    <label for="productInput">Pilih Bahan Baku</label>
                    <input id="productInput" type="text" placeholder="Ketik nama bahan..." required>
                </div>
                <div class="form-group">
                    <label for="containerInput">Pilih Wadah Kosong (Opsional)</label>
                    <input id="containerInput" type="text" placeholder="Ketik nama wadah...">
                    <input type="hidden" id="containerWeight" value="0">
                </div>
                <div class="form-group">
                    <label for="manualStock">Berat/Jumlah Terkini (Manual)</label>
                    <input type="number" id="manualStock" step="any" required placeholder="Contoh: 500">
                </div>
                <button type="submit" class="calc-button">Hitung & Tambah ke Histori</button>
            </form>
        </div>

        <div class="container opname-history" id="opnameHistory">
            <h2>Histori Stok Opname</h2>
            <table>
                <thead>
                    <tr>
                        <th>Nama Bahan</th>
                        <th>Hitungan Data</th>
                        <th>Hitungan Real</th>
                        <th>Selisih</th>
                    </tr>
                </thead>
                <tbody id="historyTableBody">
                    <!-- History rows will be inserted here -->
                </tbody>
            </table>
        </div>
    </div>
    
    <!-- Custom Confirmation Modal -->
    <div id="customConfirm" class="modal">
        <div class="modal-content">
            <p id="confirmMsg"></p>
            <div class="modal-buttons">
                <button id="confirmYes" class="wa-button" style="background-color: #27ae60;">Ya, Kirim</button>
                <button id="confirmNo" class="wa-button" style="background-color: #c0392b;">Batal</button>
            </div>
        </div>
    </div>

<script>
// --- DATA FILTER PERMANEN ---
const filterDataFromFile = [
    { product: 'Ayam Marinasi', batas_kritis: 4, batas_limit: 5, batas_mendekati: 6 },
    { product: 'beras', batas_kritis: 1200, batas_limit: 1600, batas_mendekati: 1800 },
    { product: 'biscoff jam', batas_kritis: 200, batas_limit: 300, batas_mendekati: 350 },
    { product: 'bowl', batas_kritis: 30, batas_limit: 50, batas_mendekati: 55 },
    { product: 'box toast L', batas_kritis: 20, batas_limit: 30, batas_mendekati: 35 },
    { product: 'box toast M', batas_kritis: 50, batas_limit: 55, batas_mendekati: 100 },
    { product: 'box toast S', batas_kritis: 50, batas_limit: 75, batas_mendekati: 100 },
    { product: 'Brown sugar', batas_kritis: 200, batas_limit: 500, batas_mendekati: 600 },
    { product: 'butter', batas_kritis: 200, batas_limit: 500, batas_mendekati: 600 },
    { product: 'caramel crumb', batas_kritis: 150, batas_limit: 200, batas_mendekati: 250 },
    { product: 'caramel saos', batas_kritis: 300, batas_limit: 350, batas_mendekati: 400 },
    { product: 'chili besar', batas_kritis: 300, batas_limit: 325, batas_mendekati: 400 },
    { product: 'chili sachet', batas_kritis: 18, batas_limit: 24, batas_mendekati: 26 },
    { product: 'choco crunchy', batas_kritis: 500, batas_limit: 600, batas_mendekati: 750 },
    { product: 'Cup L', batas_kritis: 50, batas_limit: 60, batas_mendekati: 65 },
    { product: 'Cup M', batas_kritis: 70, batas_limit: 100, batas_mendekati: 110 },
    { product: 'Cup R', batas_kritis: 70, batas_limit: 110, batas_mendekati: 120 },
    { product: 'espresso', batas_kritis: 600, batas_limit: 900, batas_mendekati: 950 },
    { product: 'gula pasir', batas_kritis: 55, batas_limit: 100, batas_mendekati: 200 },
    { product: 'Ham', batas_kritis: 20, batas_limit: 24, batas_mendekati: 28 },
    { product: 'keju blok', batas_kritis: 150, batas_limit: 160, batas_mendekati: 200 },
    { product: 'keju slice', batas_kritis: 10, batas_limit: 12, batas_mendekati: 15 },
    { product: 'mango jam', batas_kritis: 200, batas_limit: 300, batas_mendekati: 360 },
    { product: 'mayones', batas_kritis: 250, batas_limit: 300, batas_mendekati: 350 },
    { product: 'minyak', batas_kritis: 2, batas_limit: 3, batas_mendekati: 4 },
    { product: 'nastar', batas_kritis: 300, batas_limit: 350, batas_mendekati: 400 },
    { product: 'nori', batas_kritis: 1, batas_limit: 2, batas_mendekati: 2 },
    { product: 'oreo crumb', batas_kritis: 150, batas_limit: 300, batas_mendekati: 350 },
    { product: 'powder chocolate', batas_kritis: 4, batas_limit: 6, batas_mendekati: 8 },
    { product: 'powder greentea', batas_kritis: 4, batas_limit: 6, batas_mendekati: 8 },
    { product: 'powder ori', batas_kritis: 6, batas_limit: 8, batas_mendekati: 12 },
    { product: 'powder red velvet', batas_kritis: 3, batas_limit: 4, batas_mendekati: 5 },
    { product: 'powder strawberry', batas_kritis: 3, batas_limit: 4, batas_mendekati: 5 },
    { product: 'powder taro', batas_kritis: 3, batas_limit: 4, batas_mendekati: 5 },
    { product: 'powder white latte', batas_kritis: 4, batas_limit: 6, batas_mendekati: 8 },
    { product: 'Roti', batas_kritis: 32, batas_limit: 35, batas_mendekati: 48 },
    { product: 'salt cream/bubuk cream', batas_kritis: 300, batas_limit: 400, batas_mendekati: 500 },
    { product: 'saos cheese', batas_kritis: 250, batas_limit: 300, batas_mendekati: 400 },
    { product: 'saos mentai', batas_kritis: 350, batas_limit: 350, batas_mendekati: 550 },
    { product: 'saos nanban', batas_kritis: 300, batas_limit: 350, batas_mendekati: 400 },
    { product: 'saos spicy bbq', batas_kritis: 350, batas_limit: 600, batas_mendekati: 650 },
    { product: 'sirup butterscotch', batas_kritis: 250, batas_limit: 300, batas_mendekati: 350 },
    { product: 'sirup caramel', batas_kritis: 300, batas_limit: 350, batas_mendekati: 400 },
    { product: 'sirup hazelnut', batas_kritis: 100, batas_limit: 150, batas_mendekati: 200 },
    { product: 'sirup mangga', batas_kritis: 200, batas_limit: 250, batas_mendekati: 300 },
    { product: 'sirup pandan', batas_kritis: 200, batas_limit: 250, batas_mendekati: 300 },
    { product: 'sirup strawberry', batas_kritis: 300, batas_limit: 350, batas_mendekati: 400 },
    { product: 'SKM', batas_kritis: 1200, batas_limit: 1500, batas_mendekati: 2000 },
    { product: 'strawberry crumb', batas_kritis: 15, batas_limit: 20, batas_mendekati: 21 },
    { product: 'strawberry jam', batas_kritis: 500, batas_limit: 550, batas_mendekati: 600 },
    { product: 'strawberry oles', batas_kritis: 200, batas_limit: 250, batas_mendekati: 300 },
    { product: 'sweet cheese', batas_kritis: 300, batas_limit: 350, batas_mendekati: 400 },
    { product: 'teh', batas_kritis: 500, batas_limit: 600, batas_mendekati: 700 },
    { product: 'tepung', batas_kritis: 1200, batas_limit: 1500, batas_mendekati: 1800 },
    { product: 'tomat besar', batas_kritis: 200, batas_limit: 250, batas_mendekati: 300 },
    { product: 'tomat sachet', batas_kritis: 15, batas_limit: 16, batas_mendekati: 20 },
    { product: 'UHT', batas_kritis: 2850, batas_limit: 3800, batas_mendekati: 3900 },
    { product: 'UHT coconut', batas_kritis: 300, batas_limit: 500, batas_mendekati: 550 },
    { product: 'yakult', batas_kritis: 5, batas_limit: 6, batas_mendekati: 7 }
];
const dataSatuan = [
    { product: 'Ayam Marinasi', acuan_satuan: 1 }, { product: 'beras', acuan_satuan: 5000 }, { product: 'biscoff jam', acuan_satuan: 400 }, { product: 'bowl', acuan_satuan: 25 }, { product: 'box toast L', acuan_satuan: 100 }, { product: 'box toast M', acuan_satuan: 100 }, { product: 'box toast S', acuan_satuan: 100 }, { product: 'Brown sugar', acuan_satuan: 650 }, { product: 'butter', acuan_satuan: 400 }, { product: 'caramel crumb', acuan_satuan: 400 }, { product: 'caramel saos', acuan_satuan: 800 }, { product: 'chili besar', acuan_satuan: 1000 }, { product: 'chili sachet', acuan_satuan: 1000 }, { product: 'choco crunchy', acuan_satuan: 800 }, { product: 'Cup L', acuan_satuan: 50 }, { product: 'Cup M', acuan_satuan: 50 }, { product: 'Cup R', acuan_satuan: 50 }, { product: 'espresso', acuan_satuan: 950 }, { product: 'gula pasir', acuan_satuan: 1000 }, { product: 'Ham', acuan_satuan: 20 }, { product: 'keju blok', acuan_satuan: 1000 }, { product: 'keju slice', acuan_satuan: 12 }, { product: 'mango jam', acuan_satuan: 1000 }, { product: 'mayones', acuan_satuan: 1000 }, { product: 'minyak', acuan_satuan: 1000 }, { product: 'nastar', acuan_satuan: 400 }, { product: 'nori', acuan_satuan: 50 }, { product: 'oreo crumb', acuan_satuan: 400 }, { product: 'powder chocolate', acuan_satuan: 1000 }, { product: 'powder greentea', acuan_satuan: 1000 }, { product: 'powder ori', acuan_satuan: 1000 }, { product: 'powder red velvet', acuan_satuan: 1000 }, { product: 'powder strawberry', acuan_satuan: 1000 }, { product: 'powder taro', acuan_satuan: 1000 }, { product: 'powder white latte', acuan_satuan: 1000 }, { product: 'Roti', acuan_satuan: 48 }, { product: 'salt cream/bubuk cream', acuan_satuan: 1000 }, { product: 'saos cheese', acuan_satuan: 1000 }, { product: 'saos mentai', acuan_satuan: 1000 }, { product: 'saos nanban', acuan_satuan: 1000 }, { product: 'saos spicy bbq', acuan_satuan: 1000 }, { product: 'sirup butterscotch', acuan_satuan: 800 }, { product: 'sirup caramel', acuan_satuan: 800 }, { product: 'sirup hazelnut', acuan_satuan: 800 }, { product: 'sirup mangga', acuan_satuan: 800 }, { product: 'sirup pandan', acuan_satuan: 800 }, { product: 'sirup strawberry', acuan_satuan: 800 }, { product: 'SKM', acuan_satuan: 950 }, { product: 'strawberry crumb', acuan_satuan: 100 }, { product: 'strawberry jam', acuan_satuan: 800 }, { product: 'strawberry oles', acuan_satuan: 400 }, { product: 'sweet cheese', acuan_satuan: 1000 }, { product: 'teh', acuan_satuan: 800 }, { product: 'tepung', acuan_satuan: 5000 }, { product: 'tomat besar', acuan_satuan: 1000 }, { product: 'tomat sachet', acuan_satuan: 25 }, { product: 'UHT', acuan_satuan: 950 }, { product: 'UHT coconut', acuan_satuan: 1000 }, { product: 'yakult', acuan_satuan: 5 }
];

const dataWadah = [
    { nama: 'Tanpa Wadah', berat: 0 }, { nama: 'Botol Sirup', berat: 35 }, { nama: 'Wadah Selai Toast', berat: 56.1 }, { nama: 'Bucket', berat: 211 }, { nama: 'Biscoff', berat: 35.3 }, { nama: 'Gastronom', berat: 114 }, { nama: 'Panci Besar', berat: 518 }, { nama: 'Panci Kecil', berat: 198 }, { nama: 'Jurigen Besar', berat: 147.2 }, { nama: 'Jurigen Kecil', berat: 70 }, { nama: 'Wadah Jam', berat: 78.3 }, { nama: 'Wadah Ayam', berat: 98 }, { nama: 'Botol Sirup Besar', berat: 50.7 }
];

const manualStockItems = [
    'Kresek T', 'Kresek panjang', 'Kresek L', 'Kresek M', 'Kresek S', 'Sedotan', 'Sendok',
    'Nori', 'Selada', 'Timun', 'Tutup cup Sealer', 'Kertas penggaris grab', 'Kresek sampah',
    'Soklin lantai', 'Rinso', 'Sunlight', 'Cling', 'Baterai', 'Kertas thermal'
];


// --- GLOBAL STATE ---
let stokDataFromFile = []; 
let reportData = { urgent: [], limit: [], mendekati: [] };

// --- EVENT LISTENERS ---
document.getElementById('uploadForm').addEventListener('submit', handleFileUpload);
document.getElementById('opnameForm').addEventListener('submit', handleOpnameCalculation);
document.getElementById('waButton').addEventListener('click', handleWhatsAppSend);
document.getElementById('confirmYes').addEventListener('click', onConfirmYes);
document.getElementById('confirmNo').addEventListener('click', onConfirmNo);


// --- FILE HANDLING ---
function handleFileUpload(event) {
    event.preventDefault();
    const stokFileInput = document.getElementById('stokFile');
    
    if (stokFileInput.files.length === 0) {
        alert('Harap unggah File Stok Saat Ini.');
        return;
    }
    
    const loader = document.getElementById('loader');
    loader.style.display = 'block';
    document.getElementById('mainContent').style.display = 'none';

    const stokFile = stokFileInput.files[0];

    readFile(stokFile)
    .then(stokData => {
        mainProcess(stokData);
    }).catch(error => {
        alert(`Gagal memproses file: ${error}`);
        loader.style.display = 'none';
    });
}

function readFile(file) {
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onerror = () => reject(`Gagal membaca file ${file.name}`);
        
        if (file.name.endsWith('.csv')) {
            reader.readAsText(file);
            reader.onload = (e) => resolve(parseCSV(e.target.result));
        } else if (file.name.endsWith('.xlsx')) {
            reader.readAsArrayBuffer(file);
            reader.onload = (e) => resolve(parseExcel(e.target.result));
        } else {
            reject("Format file tidak didukung. Harap unggah file .csv atau .xlsx");
        }
    });
}

function parseExcel(data) {
    try {
        const workbook = XLSX.read(data, { type: 'array' });
        const firstSheetName = workbook.SheetNames[0];
        const worksheet = workbook.Sheets[firstSheetName];
        return XLSX.utils.sheet_to_json(worksheet);
    } catch (e) {
        return [];
    }
}

function parseCSV(text) {
    try {
        const lines = text.replace(/\r/g, "").split('\n').filter(line => line.trim() !== '');
        if (lines.length < 2) return [];
        
        const headers = lines[0].split(',').map(h => h.trim());
        const data = [];
        for (let i = 1; i < lines.length; i++) {
            const values = lines[i].split(',');
            let obj = {};
            headers.forEach((header, index) => {
                obj[header] = values[index] ? values[index].trim() : '';
            });
            data.push(obj);
        }
        return data;
    } catch (e) {
        return [];
    }
}

// --- MAIN PROCESSING ---
function mainProcess(stokData) {
    if (!stokData || stokData.length === 0) {
        alert("File stok kosong atau tidak valid. Tidak ada data yang bisa diproses.");
        document.getElementById('loader').style.display = 'none';
        return;
    }

    stokDataFromFile = stokData.map(item => ({
        product: item.product,
        stock: parseFloat(item.stock) || 0,
        uom: item.uom || 'gr' 
    }));
    
    processReport(stokDataFromFile, filterDataFromFile);
    setupOpnameCalculator(stokDataFromFile);
    setupManualStock();
    
    document.getElementById('loader').style.display = 'none';
    document.getElementById('mainContent').style.display = 'block';
}

function formatStok(stok, productName) {
    const satuanInfo = dataSatuan.find(d => d.product === productName);
    if (!satuanInfo || isNaN(stok) || isNaN(satuanInfo.acuan_satuan) || satuanInfo.acuan_satuan === 0) {
        return `${Math.round(stok)}`;
    }

    stok = parseFloat(stok);
    const acuan = parseFloat(satuanInfo.acuan_satuan);

    if (stok < acuan) {
        return `${Math.round(stok)}`;
    }

    const jumlahUtuh = Math.floor(stok / acuan);
    const sisa = Math.round(stok % acuan);
    
    return sisa === 0 ? `${jumlahUtuh}` : `${jumlahUtuh}/${sisa}`;
}


function processReport(stok, filter) {
    const urgentList = [], limitList = [], mendekatiList = [];

    stok.forEach(item => {
        const filterInfo = filter.find(d => d.product === item.product);
        if (!filterInfo) return;

        const stokValue = item.stock;
        const { batas_kritis, batas_limit, batas_mendekati } = filterInfo;
        
        const itemInfo = { 
            nama: item.product, 
            sisa: formatStok(stokValue, item.product),
            stok: stokValue 
        };

        if (stokValue <= batas_kritis) {
            urgentList.push(itemInfo);
        } else if (stokValue > batas_kritis && stokValue <= batas_limit) {
            limitList.push(itemInfo);
        } else if (stokValue > batas_limit && stokValue <= batas_mendekati) {
            mendekatiList.push(itemInfo);
        }
    });
    
    urgentList.sort((a, b) => a.stok - b.stok);
    limitList.sort((a, b) => a.stok - b.stok);
    mendekatiList.sort((a, b) => a.stok - b.stok);
    
    reportData = { urgent: urgentList, limit: limitList, mendekati: mendekatiList };
    
    displayReportResults(urgentList, limitList, mendekatiList);
}

function displayReportResults(urgent, limit, mendekati) {
    const populateList = (elementId, items) => {
        const ul = document.getElementById(elementId);
        ul.innerHTML = '';
        items.forEach(item => { ul.innerHTML += `<li><span>${item.nama}</span> <strong>Sisa: ${item.sisa}</strong></li>`; });
    };

    populateList('urgentList', urgent);
    populateList('limitList', limit);
    populateList('mendekatiList', mendekati);

    document.getElementById('urgentCategory').style.display = urgent.length > 0 ? 'block' : 'none';
    document.getElementById('limitCategory').style.display = limit.length > 0 ? 'block' : 'none';
    document.getElementById('mendekatiCategory').style.display = mendekati.length > 0 ? 'block' : 'none';
}

// --- MANUAL STOCK & WHATSAPP LOGIC ---
function setupManualStock() {
    const container = document.getElementById('manualStockList');
    container.innerHTML = '';
    manualStockItems.forEach(item => {
        const div = document.createElement('div');
        div.className = 'manual-stock-item';
        div.innerHTML = `
            <label for="manual-${item.replace(/\s/g, '')}">${item}</label>
            <input type="number" id="manual-${item.replace(/\s/g, '')}" class="manual-input" placeholder="Qty">
        `;
        container.appendChild(div);
    });
}

function handleWhatsAppSend(event) {
    event.preventDefault();
    
    const manualInputs = Array.from(document.getElementsByClassName('manual-input'));
    const filledManualItems = [];
    manualInputs.forEach(input => {
        if (input.value && input.value.trim() !== '') {
            const label = document.querySelector(`label[for='${input.id}']`);
            filledManualItems.push({
                nama: label.textContent,
                sisa: input.value.trim()
            });
        }
    });

    if (filledManualItems.length === 0) {
        const modal = document.getElementById('customConfirm');
        const msg = document.getElementById('confirmMsg');
        msg.textContent = "kamu yakin stok manualmu aman? tiwas diclatu mas adit lo";
        modal.style.display = 'block';
    } else {
        const combinedLimitList = [...reportData.limit, ...filledManualItems];
        generateAndOpenWhatsAppLink(reportData.urgent, combinedLimitList, reportData.mendekati);
    }
}

function onConfirmYes() {
    generateAndOpenWhatsAppLink(reportData.urgent, reportData.limit, reportData.mendekati);
    document.getElementById('customConfirm').style.display = 'none';
}

function onConfirmNo() {
    document.getElementById('customConfirm').style.display = 'none';
}

function generateAndOpenWhatsAppLink(urgent, limit, mendekati) {
    const today = new Date();
    const formattedDate = today.toLocaleDateString('id-ID', { day: 'numeric', month: 'long', year: 'numeric' });
    let waText = `Update Laporan Stok Bahan Baku\n`;
    waText += `(Tanggal: ${formattedDate})\n\n`;
    
    const addSectionToText = (title, items) => {
        if (items.length > 0) {
            waText += `${title}\n`;
            items.forEach(item => { waText += `${item.nama}, sisa ${item.sisa}\n`; });
            waText += "\n";
        }
    };

    addSectionToText("URGENT ‼️", urgent);
    addSectionToText("LIMIT ⚠️", limit);
    addSectionToText("MENDEKATI LIMIT 📈", mendekati);
    
    const waURL = `https://api.whatsapp.com/send?phone=628977916516&text=${encodeURIComponent(waText)}`;
    window.open(waURL, '_blank');
}


// --- OPNAME CALCULATOR LOGIC (WITH AUTOCOMPLETE) ---
function setupOpnameCalculator(data) {
    const productNames = data.map(item => item.product).sort((a, b) => a.localeCompare(b));
    const containerNames = dataWadah.map(item => ({
        displayText: `${item.nama} (${item.berat} gr)`,
        value: item.berat,
        matchText: item.nama
    }));

    autocomplete(document.getElementById("productInput"), productNames);
    autocomplete(document.getElementById("containerInput"), containerNames, true);
    document.getElementById('historyTableBody').innerHTML = '';
}

function handleOpnameCalculation(event) {
    event.preventDefault();
    
    const selectedProductName = document.getElementById('productInput').value;
    const selectedContainerWeight = parseFloat(document.getElementById('containerWeight').value) || 0;
    const manualStockInput = parseFloat(document.getElementById('manualStock').value);

    if (!selectedProductName) {
        alert("Silakan pilih bahan baku terlebih dahulu.");
        return;
    }
    if (isNaN(manualStockInput)) {
        alert("Silakan masukkan berat/jumlah terkini yang valid.");
        return;
    }

    const itemData = stokDataFromFile.find(item => item.product === selectedProductName);
    if (!itemData) {
        alert("Bahan baku tidak ditemukan. Pastikan nama diketik dengan benar.");
        return;
    }

    const hitunganData = itemData.stock;
    const hitunganReal = manualStockInput - selectedContainerWeight;
    const selisih = hitunganReal - hitunganData;

    addHistoryRow({
        nama: selectedProductName,
        data: hitunganData,
        real: hitunganReal,
        selisih: selisih,
        uom: itemData.uom
    });
}

function addHistoryRow(historyItem) {
    const tableBody = document.getElementById('historyTableBody');
    const newRow = tableBody.insertRow(0); 

    const uom = historyItem.uom || 'gr';

    newRow.innerHTML = `
        <td>${historyItem.nama}</td>
        <td>${Math.round(historyItem.data)} ${uom}</td>
        <td>${Math.round(historyItem.real)} ${uom}</td>
        <td class="${historyItem.selisih > 0 ? 'selisih-plus' : historyItem.selisih < 0 ? 'selisih-minus' : ''}">
            ${historyItem.selisih > 0 ? '+' : ''}${Math.round(historyItem.selisih)}
        </td>
    `;
}

// --- AUTOCOMPLETE FUNCTIONALITY ---
function autocomplete(inp, arr, isObject = false) {
    let currentFocus;
    inp.addEventListener("input", function(e) {
        let a, b, i, val = this.value;
        closeAllLists();
        if (!val) { return false;}
        currentFocus = -1;
        a = document.createElement("DIV");
        a.setAttribute("id", this.id + "autocomplete-list");
        a.setAttribute("class", "autocomplete-items");
        this.parentNode.appendChild(a);

        for (i = 0; i < arr.length; i++) {
            let item = isObject ? arr[i].matchText : arr[i];
            let displayText = isObject ? arr[i].displayText : arr[i];

            if (item.substr(0, val.length).toUpperCase() == val.toUpperCase()) {
                b = document.createElement("DIV");
                b.innerHTML = "<strong>" + displayText.substr(0, val.length) + "</strong>";
                b.innerHTML += displayText.substr(val.length);
                b.dataset.value = isObject ? arr[i].value : arr[i];
                b.dataset.display = displayText;

                b.addEventListener("click", function(e) {
                    inp.value = this.dataset.display;
                    if (isObject) {
                        document.getElementById('containerWeight').value = this.dataset.value;
                    }
                    closeAllLists();
                });
                a.appendChild(b);
            }
        }
    });

    function closeAllLists(elmnt) {
        var x = document.getElementsByClassName("autocomplete-items");
        for (var i = 0; i < x.length; i++) {
            if (elmnt != x[i] && elmnt != inp) {
                x[i].parentNode.removeChild(x[i]);
            }
        }
    }
    document.addEventListener("click", function (e) {
        closeAllLists(e.target);
    });
}

</script>
</body>
</html>
