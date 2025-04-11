<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>شاليهات نادي الجلاء</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Cairo', sans-serif;
            background-color: #f8f9fa;
        }
        header {
            background-color: #dc3545;
            color: white;
            padding: 1.5em;
            text-align: center;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        header img {
            width: 80px;
            margin-left: 10px;
        }
        .navbar {
            background-color: #ffc107;
        }
        .navbar a {
            color: white;
            font-weight: bold;
        }
        .container {
            background-color: white;
            padding: 2em;
            margin-top: 2em;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        .chalet-card {
            border: 2px solid #dc3545;
            padding: 1em;
            margin: 0.5em;
            border-radius: 8px;
            text-align: center;
        }
        .chalet-card.available {
            background-color: #28a745;
            color: white;
        }
        .chalet-card.booked {
            background-color: #dc3545;
            color: white;
        }
        .form-section {
            background-color: #fff3cd;
            padding: 1.5em;
            border-radius: 8px;
            margin-top: 2em;
        }
        footer {
            background-color: #dc3545;
            color: white;
            text-align: center;
            padding: 1em;
            width: 100%;
            margin-top: 2em;
        }
        .btn-report {
            background-color: #ffc107;
            color: #dc3545;
            font-weight: bold;
        }
    </style>
</head>
<body>
    <header>
        <img src="https://www.afhotels.com.eg/img/logo.png" alt="لوجو نادي الجلاء">
        <h1>شاليهات نادي الجلاء</h1>
    </header>
    <nav class="navbar navbar-expand-lg">
        <div class="container-fluid">
            <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav">
                    <li class="nav-item"><a class="nav-link" href="#home">الرئيسية</a></li>
                    <li class="nav-item"><a class="nav-link" href="#add">إضافة حجز</a></li>
                    <li class="nav-item"><a class="nav-link" href="#report">تقرير الحجوزات</a></li>
                </ul>
            </div>
        </div>
    </nav>
    <div class="container">
        <h2>حالة الشاليهات</h2>
        <div class="mb-3">
            <label for="chaletFilter" class="form-label">تصفية الشاليهات</label>
            <select class="form-control" id="chaletFilter" onchange="filterChalets()">
                <option value="all">الكل</option>
                <option value="available">فاضي</option>
                <option value="booked">محجوز</option>
            </select>
        </div>
        <div class="row" id="chalets">
        </div>
        <div class="form-section" id="add">
            <h2>إضافة حجز جديد</h2>
            <form id="bookingForm">
                <div class="mb-3">
                    <label for="guestName" class="form-label">اسم الجيست</label>
                    <input type="text" class="form-control" id="guestName" required>
                </div>
                <div class="mb-3">
                    <label for="guestPhone" class="form-label">رقم الهاتف</label>
                    <input type="tel" class="form-control" id="guestPhone" required>
                </div>
                <div class="mb-3">
                    <label for="adults" class="form-label">عدد البالغين</label>
                    <input type="number" class="form-control" id="adults" min="1" required>
                </div>
                <div class="mb-3">
                    <label for="children" class="form-label">عدد الأطفال</label>
                    <input type="number" class="form-control" id="children" min="0" required>
                </div>
                <div class="mb-3">
                    <label for="chaletNumber" class="form-label">رقم الشاليه</label>
                    <select class="form-control" id="chaletNumber" required>
                    </select>
                </div>
                <div class="mb-3">
                    <label for="checkIn" class="form-label">تاريخ الوصول</label>
                    <input type="date" class="form-control" id="checkIn" required>
                </div>
                <div class="mb-3">
                    <label for="checkOut" class="form-label">تاريخ المغادرة</label>
                    <input type="date" class="form-control" id="checkOut" required>
                </div>
                <button type="submit" class="btn btn-danger">إضافة الحجز</button>
            </form>
        </div>
        <div id="report">
            <h2>تقرير الحجوزات</h2>
            <button class="btn btn-report" onclick="generateReport()">تحميل تقرير الحجوزات (CSV)</button>
        </div>
    </div>
    <footer>
        <p>جميع الحقوق محفوظة © 2025 - شاليهات نادي الجلاء</p>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        let chalets = Array.from({length: 25}, (_, i) => ({
            id: i + 1,
            status: 'available',
            guest: null,
            phone: null,
            adults: null,
            children: null,
            checkIn: null,
            checkOut: null
        }));

        function loadChalets() {
            const chaletContainer = document.getElementById('chalets');
            const filter = document.getElementById('chaletFilter')?.value || 'all';
            chaletContainer.innerHTML = '';
            let filteredChalets = chalets;
            if (filter === 'available') {
                filteredChalets = chalets.filter(chalet => chalet.status === 'available');
            } else if (filter === 'booked') {
                filteredChalets = chalets.filter(chalet => chalet.status === 'booked');
            }
            filteredChalets.forEach(chalet => {
                const card = document.createElement('div');
                card.className = `col-md-2 chalet-card ${chalet.status}`;
                card.innerHTML = `
                    <h5>شاليه ${chalet.id}</h5>
                    <p>${
                        chalet.status === 'available' 
                        ? 'فاضي' 
                        : `محجوز: ${chalet.guest}<br>البالغين: ${chalet.adults}<br>الأطفال: ${chalet.children}<br>الوصول: ${chalet.checkIn}<br>المغادرة: ${chalet.checkOut}`
                    }</p>
                    ${chalet.status === 'booked' ? `<button class="btn btn-sm btn-light" onclick="cancelBooking(${chalet.id})">إلغاء</button>` : ''}
                `;
                chaletContainer.appendChild(card);
            });
        }

        function filterChalets() {
            loadChalets();
        }

        function loadChaletOptions() {
            const select = document.getElementById('chaletNumber');
            select.innerHTML = '';
            chalets.forEach(chalet => {
                if (chalet.status === 'available') {
                    const option = document.createElement('option');
                    option.value = chalet.id;
                    option.text = `شاليه ${chalet.id}`;
                    select.appendChild(option);
                }
            });
        }

        document.getElementById('bookingForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const guestName = document.getElementById('guestName').value;
            const guestPhone = document.getElementById('guestPhone').value;
            const adults = document.getElementById('adults').value;
            const children = document.getElementById('children').value;
            const chaletNumber = document.getElementById('chaletNumber').value;
            const checkIn = document.getElementById('checkIn').value;
            const checkOut = document.getElementById('checkOut').value;

            const chalet = chalets.find(c => c.id == chaletNumber);
            chalet.status = 'booked';
            chalet.guest = guestName;
            chalet.phone = guestPhone;
            chalet.adults = adults;
            chalet.children = children;
            chalet.checkIn = checkIn;
            chalet.checkOut = checkOut;

            saveChalets();
            loadChalets();
            loadChaletOptions();
            this.reset();
            alert('تم إضافة الحجز بنجاح!');
        });

        function cancelBooking(chaletId) {
            const chalet = chalets.find(c => c.id == chaletId);
            chalet.status = 'available';
            chalet.guest = null;
            chalet.phone = null;
            chalet.adults = null;
            chalet.children = null;
            chalet.checkIn = null;
            chalet.checkOut = null;

            saveChalets();
            loadChalets();
            loadChaletOptions();
            alert('تم إلغاء الحجز بنجاح!');
        }

        function saveChalets() {
            localStorage.setItem('chalets', JSON.stringify(chalets));
        }

        function loadSavedChalets() {
            const saved = localStorage.getItem('chalets');
            if (saved) {
                chalets = JSON.parse(saved);
            }
        }

        function generateReport() {
            const BOM = '\uFEFF';
            const headers = [
                'رقم الشاليه',
                'اسم الجيست',
                'رقم الهاتف',
                'عدد البالغين',
                'عدد الأطفال',
                'تاريخ الوصول',
                'تاريخ المغادرة'
            ];
            const rows = chalets
                .filter(chalet => chalet.status === 'booked')
                .map(chalet => [
                    chalet.id,
                    `"${chalet.guest}"`,
                    chalet.phone,
                    chalet.adults,
                    chalet.children,
                    chalet.checkIn,
                    chalet.checkOut
                ]);
            const csvContent = BOM + [
                headers.join(','),
                ...rows.map(row => row.join(','))
            ].join('\n');
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = 'تقرير_الحجوزات.csv';
            link.click();
            URL.revokeObjectURL(link.href);
        }

        loadSavedChalets();
        loadChalets();
        loadChaletOptions();
    </script>
</body>
</html>
