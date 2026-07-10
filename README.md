<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>IPTV Pro Manager</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <style>
        :root {
            --primary: #4f46e5;
            --primary-dark: #4338ca;
            --bg: #f8fafc;
            --surface: #ffffff;
            --text: #1e293b;
            --text-light: #64748b;
            --border: #e2e8f0;
            --danger: #ef4444;
            --success: #10b981;
            --warning: #f59e0b;
            --info: #3b82f6;
            --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
            --radius: 10px;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, sans-serif; -webkit-tap-highlight-color: transparent; }
        body { background-color: var(--bg); color: var(--text); padding-bottom: 80px; min-height: 100vh; }

        /* --- Auth --- */
        #auth-container { display: flex; justify-content: center; align-items: center; min-height: 100vh; background: linear-gradient(135deg, var(--primary), #818cf8); padding: 15px; }
        .auth-card { background: var(--surface); padding: 1.5rem; border-radius: 1rem; width: 100%; max-width: 400px; box-shadow: 0 10px 25px rgba(0,0,0,0.2); text-align: center; }
        .password-wrapper { position: relative; margin-bottom: 1rem; }
        .password-wrapper i { position: absolute; left: 15px; top: 50%; transform: translateY(-50%); color: var(--text-light); cursor: pointer; }
        .auth-input { width: 100%; padding: 12px 15px; margin-bottom: 10px; border: 1px solid var(--border); border-radius: var(--radius); outline: none; font-size: 16px; background: #fff; }
        .password-wrapper input { padding-left: 40px; }
        .auth-btn { width: 100%; padding: 14px; background: var(--primary); color: white; border: none; border-radius: var(--radius); font-weight: bold; font-size: 16px; cursor: pointer; margin-top: 10px; }

        /* --- Layout --- */
        #app-container { display: none; }
        nav { background: var(--surface); padding: 15px 20px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .logo { font-size: 1.2rem; font-weight: 800; color: var(--primary); display: flex; align-items: center; gap: 8px; }
        
        /* Desktop Navigation Styles */
        .desktop-menu { display: none; gap: 10px; }
        .desktop-menu .nav-link { background: none; border: none; color: var(--text-light); font-size: 0.95rem; font-weight: 600; cursor: pointer; display: flex; align-items: center; gap: 6px; padding: 8px 12px; border-radius: var(--radius); transition: all 0.2s ease; }
        .desktop-menu .nav-link:hover { background: #f1f5f9; color: var(--primary); }
        .desktop-menu .nav-link.active { color: var(--primary); background: #eeebff; }
        .desktop-menu .nav-link.hidden { display: none; }

        .top-actions { display: flex; gap: 10px; align-items: center; }
        .icon-btn { background: none; border: none; font-size: 1.3rem; color: var(--text-light); cursor: pointer; padding: 5px; border-radius: 50%; width: 36px; height: 36px; display: inline-flex; align-items: center; justify-content: center; transition: background 0.2s; }
        .icon-btn:hover { background: #f1f5f9; }

        /* --- Bottom Nav --- */
        .bottom-nav { position: fixed; bottom: 0; left: 0; width: 100%; background: var(--surface); display: flex; justify-content: space-around; padding: 10px 0; box-shadow: 0 -2px 10px rgba(0,0,0,0.05); z-index: 999; border-top: 1px solid var(--border); }
        .nav-item { background: none; border: none; display: flex; flex-direction: column; align-items: center; color: var(--text-light); font-size: 0.8rem; cursor: pointer; width: 20%; gap: 3px; }
        .nav-item i { font-size: 1.3rem; }
        .nav-item.active { color: var(--primary); }
        .nav-item.hidden { display: none; }

        /* --- Container --- */
        .container { padding: 12px; max-width: 1400px; margin: 0 auto; }
        .section { display: none; animation: fadeIn 0.3s ease-out; }
        .section.active { display: block; }

        /* --- Dashboard --- */
        .chart-container { height: 260px; margin-bottom: 15px; background: var(--surface); padding: 15px; border-radius: var(--radius); box-shadow: var(--shadow); border: 1px solid var(--border); }
        .chart-container.hidden { display: none; }
        .controls-area { display: flex; flex-direction: column; gap: 10px; margin-bottom: 15px; }
        .form-control { width: 100%; padding: 10px 12px; border: 1px solid var(--border); border-radius: var(--radius); outline: none; background: #fff; font-size: 15px; transition: border-color 0.2s; }
        .form-control:focus { border-color: var(--primary); }

        /* --- TABLE STYLE --- */
        .table-card { background: var(--surface); border-radius: var(--radius); box-shadow: var(--shadow); border: 1px solid var(--border); overflow: hidden; }
        .table-wrapper { overflow-x: auto; -webkit-overflow-scrolling: touch; }
        table { width: 100%; border-collapse: collapse; white-space: nowrap; }
        th { background: #f1f5f9; color: var(--text-light); font-weight: 600; padding: 10px 12px; text-align: right; font-size: 0.8rem; }
        td { padding: 10px 12px; border-bottom: 1px solid var(--border); color: var(--text); font-size: 0.85rem; vertical-align: middle; }
        tr:last-child td { border-bottom: none; }
        tr:hover { background-color: #f8fafc; }

        .status-badge { padding: 4px 8px; border-radius: 20px; font-size: 0.75rem; font-weight: bold; display: inline-block; min-width: 55px; text-align: center; cursor: pointer; user-select: none; }
        .status-active { background: #dcfce7; color: #166534; }
        .status-warning { background: #fef9c3; color: #854d0e; }
        .status-expired { background: #fee2e2; color: #991b1b; }
        .status-none { background: #f1f5f9; color: #64748b; }

        /* Buttons */
        .btn { padding: 10px 14px; border: none; border-radius: 8px; cursor: pointer; font-weight: 600; font-size: 14px; display: inline-flex; align-items: center; justify-content: center; gap: 6px; transition: opacity 0.2s; }
        .btn:active { opacity: 0.8; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-success { background: var(--success); color: white; }
        .btn-danger { background: var(--danger); color: white; }
        .btn-warning { background: var(--warning); color: white; }
        .btn-sm { padding: 5px 8px; font-size: 12px; border-radius: 6px; }

        /* Forms Grid & Multi-Column Layouts */
        .form-grid-layout { display: grid; grid-template-columns: 1fr; gap: 15px; }
        
        .card { background: var(--surface); padding: 15px; border-radius: var(--radius); margin-bottom: 15px; border: 1px solid var(--border); box-shadow: var(--shadow); }
        .card h4 { margin-bottom: 12px; color: var(--primary); border-bottom: 1px solid #f1f5f9; padding-bottom: 8px; font-size: 1rem; }
        .form-group { margin-bottom: 12px; }
        .form-group label { display: block; margin-bottom: 5px; color: var(--text-light); font-size: 0.85rem; }
        
        .stats-grid { display: grid; grid-template-columns: 1fr; gap: 10px; margin-bottom: 15px; }
        .stat-card { text-align: center; padding: 15px; border-radius: var(--radius); background: var(--surface); box-shadow: var(--shadow); border: 1px solid var(--border); }
        .stat-value { font-size: 1.3rem; font-weight: 800; display: block; margin-top: 5px; }
        .stat-title { font-size: 0.8rem; color: var(--text-light); }

        /* Modals */
        .modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.6); display: none; justify-content: center; align-items: center; z-index: 2000; backdrop-filter: blur(3px); padding: 15px; }
        .modal { background: var(--surface); width: 100%; max-width: 500px; max-height: 85vh; overflow-y: auto; border-radius: 16px; padding: 20px; box-shadow: 0 20px 25px -5px rgba(0,0,0,0.15); }
        .detail-row { display: flex; justify-content: space-between; padding: 10px 0; border-bottom: 1px solid #f1f5f9; font-size: 0.9rem; }
        .date-popover { background: #1e293b; color: #ffffff; padding: 12px 16px; border-radius: 12px; position: fixed; z-index: 3000; font-size: 0.85rem; display: none; pointer-events: none; text-align: center; box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.3); border: 1px solid rgba(255, 255, 255, 0.1); }

        /* --- Media Queries --- */

        /* شاشات الهواتف المتوسطة فما فوق */
        @media (min-width: 480px) {
            .auth-card { padding: 2rem; }
            .logo { font-size: 1.3rem; }
            .stats-grid { grid-template-columns: repeat(2, 1fr); gap: 12px; }
            th { font-size: 0.85rem; padding: 12px 15px; }
            td { font-size: 0.9rem; padding: 12px 15px; }
            .container { padding: 15px; }
            .card { padding: 20px; }
        }

        /* شاشات التابلت والأجهزة المتوسطة */
        @media (min-width: 769px) {
            body { padding-bottom: 20px; }
            .bottom-nav { display: none; }
            .desktop-menu { display: flex; }
            .controls-area { flex-direction: row; justify-content: space-between; align-items: center; }
            .controls-area .form-control { max-width: 300px; }
            .stats-grid { grid-template-columns: repeat(3, 1fr); gap: 15px; }
            
            /* تحويل نماذج الإدخال إلى نظام متعدد الأعمدة لتقليص المساحة العمودية */
            .form-grid-layout { grid-template-columns: repeat(2, 1fr); gap: 20px; }
            .form-card-full { grid-column: span 2; }
        }

        /* الشاشات الكبيرة والمكتبية العريضة */
        @media (min-width: 1200px) {
            .container { padding: 20px; }
            .form-grid-layout { gap: 25px; }
            th, td { padding: 14px 18px; }
        }

        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

    <!-- AUTHENTICATION -->
    <div id="auth-container">
        <div class="auth-card">
            <h2 style="color: var(--primary); margin-bottom: 0.5rem;">IPTV Pro</h2>
            <p style="color: var(--text-light); margin-bottom: 2rem;">لوحة تحكم ذكية</p>
            <form id="auth-form">
                <input type="email" id="auth-email" class="auth-input" placeholder="البريد الإلكتروني" required>
                <div class="password-wrapper">
                    <input type="password" id="auth-password" class="auth-input" placeholder="كلمة المرور" required>
                    <i class="fas fa-eye" onclick="togglePasswordVisibility('auth-password', this)"></i>
                </div>
                <div class="password-wrapper" id="confirm-password-wrapper" style="display: none;">
                    <input type="password" id="auth-confirm-password" class="auth-input" placeholder="تأكيد كلمة المرور">
                    <i class="fas fa-eye" onclick="togglePasswordVisibility('auth-confirm-password', this)"></i>
                </div>
                <div style="display: flex; align-items: center; gap: 10px; margin-bottom: 15px; justify-content: flex-start;">
                    <input type="checkbox" id="remember-me" style="width: 18px; height: 18px;">
                    <label for="remember-me" style="font-size: 0.95rem;">تذكرني</label>
                </div>
                <div style="text-align: left; margin-bottom: 20px;">
                    <span id="forgot-password-link" style="color: var(--primary); font-size: 0.9rem; text-decoration: underline; cursor: pointer;" onclick="resetPassword()">نسيت كلمة المرور؟</span>
                </div>
                <button type="submit" class="auth-btn" id="auth-submit-btn">تسجيل الدخول</button>
            </form>
            <p style="margin-top:20px; color:var(--text-light); cursor:pointer;" onclick="toggleAuthMode()" id="auth-switch-text">إنشاء حساب جديد</p>
            <p id="auth-error" style="color:var(--danger); background:#fee2e2; padding:10px; border-radius:8px; margin-top:10px; display:none;"></p>
        </div>
    </div>

    <!-- APP CONTAINER -->
    <div id="app-container">
        
        <!-- TOP NAVBAR -->
        <nav>
            <div class="logo"><i class="fas fa-layer-group"></i> IPTV Pro</div>
            
            <!-- Desktop/Tablet Menu Navigation (ظاهر فقط في الشاشات العريضة) -->
            <div class="desktop-menu">
                <button class="nav-link active" id="btn-desktop-dashboard" onclick="showSection('dashboard')"><i class="fas fa-home"></i> الرئيسية</button>
                <button class="nav-link" id="btn-desktop-add" onclick="showSection('add'); resetForm();"><i class="fas fa-plus-circle"></i> إضافة</button>
                <button class="nav-link" id="btn-desktop-finance" onclick="showSection('finance')"><i class="fas fa-wallet"></i> المالية</button>
                <button class="nav-link" id="btn-desktop-trash" onclick="showSection('trash')"><i class="fas fa-trash"></i> المحذوفات</button>
                <button class="nav-link hidden" id="btn-desktop-settings" onclick="showSection('settings')"><i class="fas fa-cogs"></i> الإعدادات</button>
            </div>

            <div class="top-actions">
                <button class="icon-btn" onclick="toggleChartVisibility()"><i class="fas fa-chart-pie"></i></button>
                <button class="icon-btn" style="color:var(--danger)" onclick="logout()"><i class="fas fa-sign-out-alt"></i></button>
            </div>
        </nav>

        <!-- MAIN CONTENT -->
        <div class="container">
            <div id="loading" style="text-align: center; padding: 20px; display: none;">
                <i class="fas fa-spinner fa-spin fa-2x" style="color: var(--primary);"></i>
            </div>

            <!-- 1. DASHBOARD -->
            <div id="dashboard" class="section active">
                <div class="chart-container hidden" id="chartContainer">
                    <canvas id="salesChart"></canvas>
                </div>

                <div class="controls-area">
                    <select id="filterSelect" class="form-control" onchange="renderTable()">
                        <option value="all">الكل (الأقرب للانتهاء)</option>
                        <option value="iptv_asc">IPTV: الأقل وقتاً</option>
                        <option value="iptv_desc">IPTV: الأكثر وقتاً</option>
                        <option value="ibo_lifetime">IBO: مدى الحياة</option>
                        <option value="ibo_asc">IBO: الأقل وقتاً</option>
                    </select>
                    <input type="text" id="searchInput" placeholder="بحث بالاسم..." class="form-control" onkeyup="renderTable()">
                </div>

                <div class="table-card">
                    <div class="table-wrapper">
                        <table>
                            <thead>
                                <tr>
                                    <th>الاسم</th>
                                    <th>IPTV (يوم)</th>
                                    <th>IBO (يوم)</th>
                                    <th>الجهاز</th>
                                    <th>إجراءات</th>
                                </tr>
                            </thead>
                            <tbody id="subscribersTableBody"></tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- 2. ADD SUBSCRIBER -->
            <div id="add" class="section">
                <h3 id="formTitle" style="margin-bottom: 20px; color: var(--primary);">إضافة مشترك</h3>
                <form id="subscriberForm">
                    <input type="hidden" id="editId">
                    
                    <div class="form-grid-layout">
                        <!-- البيانات الأساسية -->
                        <div class="card form-card-full">
                            <h4>البيانات الأساسية</h4>
                            <div class="form-group"><label>الاسم</label><input type="text" id="name" class="form-control" required></div>
                            <div class="form-group"><label>الهاتف</label><input type="tel" id="phone" class="form-control"></div>
                            <div class="form-group"><label>الجهاز</label><select id="deviceType" class="form-control"><option value="">-- اختر --</option></select></div>
                        </div>

                        <!-- اشتراك IPTV -->
                        <div class="card">
                            <h4>اشتراك IPTV</h4>
                            <div class="form-group"><label>البدء</label><input type="date" id="startDate" class="form-control" required></div>
                            <div class="form-group"><label>المدة (أشهر)</label><input type="number" id="duration" class="form-control" required></div>
                            <div class="form-group"><label>السعر</label><input type="number" id="iptvPrice" class="form-control"></div>
                        </div>

                        <!-- IBO Player -->
                        <div class="card">
                            <h4>IBO Player</h4>
                            <div class="form-group"><label>MAC</label><input type="text" id="iboMac" class="form-control"></div>
                            <div class="form-group"><label>Key</label><input type="text" id="iboKey" class="form-control"></div>
                            <div class="form-group"><label>التفعيل</label><input type="date" id="iboStartDate" class="form-control"></div>
                            <div class="form-group">
                                <label>المدة</label>
                                <select id="iboDuration" class="form-control">
                                    <option value="">بدون تفعيل</option>
                                    <option value="year">سنة</option>
                                    <option value="lifetime">مدى الحياة</option>
                                </select>
                            </div>
                            <div class="form-group"><label>السعر</label><input type="number" id="iboPrice" class="form-control"></div>
                        </div>

                        <!-- السيرفر -->
                        <div class="card form-card-full">
                            <h4>السيرفر</h4>
                            <div class="form-group"><label>Host</label><input type="text" id="host" class="form-control"></div>
                            <div class="form-group"><label>User</label><input type="text" id="username" class="form-control"></div>
                            <div class="form-group"><label>Pass</label><input type="text" id="password" class="form-control"></div>
                            <div class="form-group"><label>M3U</label><textarea id="m3u" class="form-control" rows="2"></textarea></div>
                        </div>
                    </div>

                    <div style="display: flex; gap: 10px; margin-top: 15px;">
                        <button type="submit" class="btn btn-primary" style="flex:1">حفظ</button>
                        <button type="button" class="btn btn-warning" style="flex:1" onclick="showSection('dashboard')">إلغاء</button>
                    </div>
                </form>
            </div>

            <!-- 3. FINANCE -->
            <div id="finance" class="section">
                <div class="stats-grid">
                    <div class="stat-card"><span class="stat-title">المبيعات</span><span class="stat-value" id="totalSales" style="color:var(--primary)">0</span></div>
                    <div class="stat-card"><span class="stat-title">التعبئة</span><span class="stat-value" id="totalRecharge" style="color:var(--warning)">0</span></div>
                    <div class="stat-card"><span class="stat-title">الصافي</span><span class="stat-value" id="netProfit" style="color:var(--success)">0</span></div>
                </div>
                <div class="card">
                    <h4>إضافة تعبئة</h4>
                    <div style="display: flex; gap: 10px;">
                        <input type="number" id="rechargeAmount" class="form-control" placeholder="المبلغ">
                        <button onclick="addRecharge()" class="btn btn-success" style="width: auto;">+</button>
                    </div>
                    <input type="text" id="rechargeNote" class="form-control" placeholder="ملاحظة" style="margin-top: 10px;">
                </div>
                <h4>سجل التعبئات</h4>
                <div id="rechargeListMobile" style="margin-top: 10px;"></div>
            </div>

            <!-- 4. TRASH -->
            <div id="trash" class="section">
                <h3 style="color:var(--danger); margin-bottom:15px;">سلة المهملات</h3>
                <div class="table-card" style="border-color: var(--danger);">
                    <div class="table-wrapper">
                        <table>
                            <thead style="background-color: #fee2e2;">
                                <tr><th>الاسم</th><th>إجراءات</th></tr>
                            </thead>
                            <tbody id="trashTableBody"></tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- 5. SETTINGS -->
            <div id="settings" class="section">
                <div class="card">
                    <h4>إدارة الأجهزة</h4>
                    <div style="display: flex; gap: 10px; margin: 10px 0;">
                        <input type="text" id="newDeviceInput" class="form-control" placeholder="اسم الجهاز">
                        <button class="btn btn-primary" onclick="addDevice()" style="width: auto;">+</button>
                    </div>
                    <div id="devicesList" style="display: flex; flex-wrap: wrap; gap: 8px;"></div>
                </div>
            </div>
        </div>

        <!-- BOTTOM NAV (ظاهر فقط في شاشات الهاتف والتابلت الصغير) -->
        <div class="bottom-nav">
            <button class="nav-item active" id="btn-dashboard" onclick="showSection('dashboard')"><i class="fas fa-home"></i> <span>الرئيسية</span></button>
            <button class="nav-item" id="btn-add" onclick="showSection('add'); resetForm();"><i class="fas fa-plus-circle"></i> <span>إضافة</span></button>
            <button class="nav-item" id="btn-finance" onclick="showSection('finance')"><i class="fas fa-wallet"></i> <span>المالية</span></button>
            <button class="nav-item" id="btn-trash" onclick="showSection('trash')"><i class="fas fa-trash"></i> <span>المحذوفات</span></button>
            <button class="nav-item hidden" id="btn-settings" onclick="showSection('settings')"><i class="fas fa-cogs"></i> <span>الإعدادات</span></button>
        </div>
    </div>

    <!-- Modals -->
    <div id="detailsModal" class="modal-overlay">
        <div class="modal">
            <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">
                <h3>التفاصيل</h3>
                <button onclick="document.getElementById('detailsModal').style.display='none'" style="background:none; border:none; font-size: 1.5rem; cursor:pointer;">&times;</button>
            </div>
            <div id="detailsContent"></div>
            <div style="margin-top: 20px; display: grid; grid-template-columns: 1fr 1fr; gap: 10px;">
                <button id="modalEditBtn" class="btn btn-warning">تعديل</button>
                <button onclick="document.getElementById('detailsModal').style.display='none'" class="btn btn-primary">إغلاق</button>
            </div>
        </div>
    </div>

    <div id="dateModal" class="date-popover">
        <div id="dateModalTitle" style="font-weight:bold; margin-bottom:5px; color:#fbbf24"></div>
        <div>البدء: <span id="dateModalStart"></span></div>
        <div>الانتهاء: <span id="dateModalEnd"></span></div>
    </div>

    <!-- Firebase -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, onAuthStateChanged, signOut, sendPasswordResetEmail, setPersistence, browserLocalPersistence, browserSessionPersistence } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-auth.js";
        import { getFirestore, collection, addDoc, updateDoc, deleteDoc, doc, onSnapshot, query, where, getDocs, setDoc, orderBy } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyA3EcwcPbdJo5j79fS0j2Hhw2W7LLEWxEc",
            authDomain: "control-iptv-49b63.firebaseapp.com",
            projectId: "control-iptv-49b63",
            storageBucket: "control-iptv-49b63.firebasestorage.app",
            messagingSenderId: "60685582544",
            appId: "1:60685582544:web:81c0cb70ba16512b449ed5",
            measurementId: "G-RZD7YKRDKZ"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);

        let currentUser = null;
        let isAdmin = false;
        let subscribers = [];
        let recharges = [];
        let trash = [];
        let managedDevices = [];
        let salesChartInstance = null;
        let isLoginMode = true;
        let unsubscribes = [];

        function clearListeners() {
            unsubscribes.forEach(unsub => { if (typeof unsub === 'function') unsub(); });
            unsubscribes = [];
        }

        onAuthStateChanged(auth, async (user) => {
            if (user) {
                currentUser = user;
                document.getElementById('auth-container').style.display = 'none';
                document.getElementById('app-container').style.display = 'block';
                clearListeners();
                await loadData(user.uid);
            } else {
                currentUser = null;
                clearListeners();
                document.getElementById('auth-container').style.display = 'flex';
                document.getElementById('app-container').style.display = 'none';
            }
        });

        async function loadData(uid) {
            document.getElementById('loading').style.display = 'block';
            
            const userUnsub = onSnapshot(doc(db, "users", uid), (snap) => {
                if (snap.exists()) {
                    isAdmin = (snap.data().role === 'admin');
                    const settingsBtn = document.getElementById('btn-settings');
                    const settingsDesktopBtn = document.getElementById('btn-desktop-settings');
                    if (isAdmin) {
                        if (settingsBtn) settingsBtn.classList.remove('hidden');
                        if (settingsDesktopBtn) settingsDesktopBtn.classList.remove('hidden');
                        renderAdminDevices();
                    } else {
                        if (settingsBtn) settingsBtn.classList.add('hidden');
                        if (settingsDesktopBtn) settingsDesktopBtn.classList.add('hidden');
                    }
                }
            });
            unsubscribes.push(userUnsub);

            const subQuery = query(collection(db, "subscribers"), where("userId", "==", uid));
            const subUnsub = onSnapshot(subQuery, (snap) => {
                subscribers = snap.docs.map(d => ({ id: d.id, ...d.data() }));
                renderTable();
                updateChart();
                calculateFinances();
            });
            unsubscribes.push(subUnsub);

            const rechQuery = query(collection(db, "recharges"), where("userId", "==", uid));
            const rechUnsub = onSnapshot(rechQuery, (snap) => {
                recharges = snap.docs.map(d => ({ id: d.id, ...d.data() }));
                recharges.sort((a,b) => new Date(b.date) - new Date(a.date));
                renderRechargeTable();
                calculateFinances();
            });
            unsubscribes.push(rechUnsub);
            
            const trashQuery = query(collection(db, "trash"), where("userId", "==", uid));
            const trashUnsub = onSnapshot(trashQuery, (snap) => {
                trash = snap.docs.map(d => ({ id: d.id, ...d.data() }));
                renderTrash();
            });
            unsubscribes.push(trashUnsub);

            const devUnsub = onSnapshot(collection(db, "managed_devices"), (snap) => {
                managedDevices = snap.docs.map(d => ({ id: d.id, ...d.data() }));
                renderDeviceSelect();
                if (isAdmin) renderAdminDevices();
            });
            unsubscribes.push(devUnsub);

            document.getElementById('loading').style.display = 'none';
        }

        window.toggleAuthMode = () => {
            isLoginMode = !isLoginMode;
            document.getElementById('auth-submit-btn').innerText = isLoginMode ? "تسجيل الدخول" : "إنشاء حساب";
            document.getElementById('auth-switch-text').innerText = isLoginMode ? "ليس لديك حساب؟ إنشاء حساب" : "لديك حساب؟ تسجيل الدخول";
            document.getElementById('confirm-password-wrapper').style.display = isLoginMode ? 'none' : 'block';
            document.getElementById('auth-confirm-password').required = !isLoginMode;
        };

        window.togglePasswordVisibility = (fid, icon) => {
            const inp = document.getElementById(fid);
            const isPass = inp.type === 'password';
            inp.type = isPass ? 'text' : 'password';
            icon.className = isPass ? 'fas fa-eye-slash' : 'fas fa-eye';
        };

        document.getElementById('auth-form').addEventListener('submit', async (e) => {
            e.preventDefault();
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-password').value;
            const confirmPass = document.getElementById('auth-confirm-password').value;
            const rememberMe = document.getElementById('remember-me').checked;
            
            try {
                if (isLoginMode) {
                    await setPersistence(auth, rememberMe ? browserLocalPersistence : browserSessionPersistence);
                    await signInWithEmailAndPassword(auth, email, pass);
                } else {
                    if(pass !== confirmPass) throw new Error("كلمة المرور غير متطابقة");
                    if(pass.length < 6) throw new Error("كلمة المرور ضعيفة");
                    const cred = await createUserWithEmailAndPassword(auth, email, pass);
                    await setDoc(doc(db, 'users', cred.user.uid), { email, role: 'user', createdAt: new Date().toISOString() });
                }
            } catch (err) {
                const el = document.getElementById('auth-error');
                el.innerText = err.message; el.style.display = 'block';
            }
        });

        window.logout = () => signOut(auth);
        window.resetPassword = async () => {
            const email = document.getElementById('auth-email').value;
            if(email) { 
                try {
                    await sendPasswordResetEmail(auth, email); 
                    alert("تم إرسال رابط استعادة كلمة المرور إلى البريد الإلكتروني."); 
                } catch(e) {
                    alert("حدث خطأ أثناء إرسال البريد الإلكتروني: " + e.message);
                }
            } else {
                alert("يرجى إدخال البريد الإلكتروني أولاً.");
            }
        };

        window.toggleChartVisibility = () => {
            const container = document.getElementById('chartContainer');
            container.classList.toggle('hidden');
            if (!container.classList.contains('hidden') && salesChartInstance) {
                salesChartInstance.resize();
            }
        };

        window.showSection = (id) => {
            document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            
            // مزامنة أزرار الملاحة السفلية للهواتف
            document.querySelectorAll('.bottom-nav .nav-item').forEach(b => b.classList.remove('active'));
            const botBtn = document.getElementById('btn-'+id);
            if(botBtn) botBtn.classList.add('active');

            // مزامنة أزرار الملاحة العلوية للحواسب
            document.querySelectorAll('.desktop-menu .nav-link').forEach(b => b.classList.remove('active'));
            const deskBtn = document.getElementById('btn-desktop-'+id);
            if(deskBtn) deskBtn.classList.add('active');
        }

        window.renderTable = () => {
            const tbody = document.getElementById('subscribersTableBody');
            const term = document.getElementById('searchInput').value.toLowerCase();
            const filter = document.getElementById('filterSelect').value;
            tbody.innerHTML = '';
            
            let displayData = [...subscribers];
            if(filter === 'iptv_asc' || filter === 'all') displayData.sort((a,b) => new Date(a.endDate) - new Date(b.endDate));
            else if (filter === 'iptv_desc') displayData.sort((a,b) => new Date(b.endDate) - new Date(a.endDate));
            else if (filter === 'ibo_lifetime') displayData = displayData.filter(s => s.iboDuration === 'lifetime');
            else if (filter === 'ibo_asc') {
                displayData = displayData.filter(s => s.iboDuration === 'year' && s.iboStartDate);
                displayData.sort((a,b) => {
                    let endA = new Date(a.iboStartDate); endA.setFullYear(endA.getFullYear()+1);
                    let endB = new Date(b.iboStartDate); endB.setFullYear(endB.getFullYear()+1);
                    return endA - endB;
                });
            }

            displayData.forEach(sub => {
                if(sub.name.toLowerCase().includes(term)) {
                    const iptvDays = getDays(sub.endDate); 
                    const iptvClass = iptvDays > 30 ? 'status-active' : (iptvDays > 0 ? 'status-warning' : 'status-expired');
                    const iptvText = iptvDays > 0 ? `${iptvDays} يوم` : "منتهي";

                    let iboText = "-";
                    let iboClass = "status-none";

                    if (sub.iboDuration === 'lifetime') { iboText = "∞"; iboClass = "status-active"; } 
                    else if (sub.iboDuration === 'year' && sub.iboStartDate) {
                        let start = new Date(sub.iboStartDate);
                        let end = new Date(start);
                        end.setFullYear(end.getFullYear() + 1);
                        let diff = Math.ceil((end - new Date()) / (1000 * 60 * 60 * 24));
                        iboText = diff > 0 ? `${diff} يوم` : "منتهي";
                        iboClass = diff > 30 ? 'status-active' : (diff > 0 ? 'status-warning' : 'status-expired');
                    }

                    tbody.innerHTML += `
                        <tr>
                            <td><span style="color:var(--primary);font-weight:bold;cursor:pointer" onclick="showDetails('${sub.id}')">${sub.name}</span></td>
                            <td><span class="status-badge ${iptvClass}" onclick="showDateDetails(event, '${sub.id}', 'IPTV')">${iptvText}</span></td>
                            <td><span class="status-badge ${iboClass}" onclick="showDateDetails(event, '${sub.id}', 'IBO Player')">${iboText}</span></td>
                            <td>${sub.device || '-'}</td>
                            <td>
                                <button class="btn btn-warning btn-sm" onclick="editSub('${sub.id}')"><i class="fas fa-edit"></i></button>
                                <button class="btn btn-danger btn-sm" onclick="moveToTrash('${sub.id}')"><i class="fas fa-trash"></i></button>
                            </td>
                        </tr>
                    `;
                }
            });
        }

        window.showDateDetails = (e, id, type) => {
            e.stopPropagation();
            const sub = subscribers.find(s => s.id === id);
            if (!sub) return;

            const pop = document.getElementById('dateModal');
            document.getElementById('dateModalTitle').innerText = type;

            if (type === 'IPTV') {
                document.getElementById('dateModalStart').innerText = sub.startDate || '-';
                document.getElementById('dateModalEnd').innerText = sub.endDate || '-';
            } else {
                document.getElementById('dateModalStart').innerText = sub.iboStartDate || '-';
                let endText = '-';
                if (sub.iboDuration === 'lifetime') {
                    endText = 'مدى الحياة';
                } else if (sub.iboDuration === 'year' && sub.iboStartDate) {
                    let start = new Date(sub.iboStartDate);
                    let end = new Date(start);
                    end.setFullYear(end.getFullYear() + 1);
                    endText = end.toISOString().split('T')[0];
                }
                document.getElementById('dateModalEnd').innerText = endText;
            }

            pop.style.display = 'block';
            pop.style.left = '50%';
            pop.style.top = '50%';
            pop.style.transform = 'translate(-50%, -50%)';
            setTimeout(() => pop.style.display = 'none', 3000);
        };

        function renderRechargeTable() {
            const container = document.getElementById('rechargeListMobile');
            container.innerHTML = '';
            recharges.forEach(r => {
                container.innerHTML += `
                    <div class="card" style="margin-bottom:10px; display:flex; justify-content:space-between; align-items:center;">
                        <div>
                            <div style="font-weight:bold; font-size:1.2rem; color:var(--success)">${r.amount}</div>
                            <div style="font-size:0.8rem; color:var(--text-light)">${r.date.split('T')[0]} - ${r.note}</div>
                        </div>
                        <button class="btn btn-danger btn-sm" onclick="deleteRecharge('${r.id}')"><i class="fas fa-trash"></i></button>
                    </div>
                `;
            });
        }

        window.renderTrash = () => {
            const tbody = document.getElementById('trashTableBody');
            tbody.innerHTML = '';
            trash.forEach(i => {
                tbody.innerHTML += `
                    <tr>
                        <td>${i.name}</td>
                        <td>
                            <button class="btn btn-success btn-sm" onclick="restore('${i.id}')">استعادة</button>
                            <button class="btn btn-danger btn-sm" onclick="permDelete('${i.id}')">حذف</button>
                        </td>
                    </tr>
                `;
            });
        };

        window.restore = async (id) => {
            const item = trash.find(t => t.id === id);
            if (!item) return;
            try {
                const { id: trashDocId, deletedAt, originalId, ...subscriberData } = item;
                await addDoc(collection(db, "subscribers"), subscriberData);
                await deleteDoc(doc(db, "trash", trashDocId));
            } catch (e) {
                alert("حدث خطأ أثناء الاستعادة: " + e.message);
            }
        };

        window.permDelete = async (id) => {
            if(confirm("حذف نهائي؟ لا يمكن التراجع!")) {
                try {
                    await deleteDoc(doc(db, "trash", id));
                } catch(e) {
                    alert("حدث خطأ أثناء الحذف: " + e.message);
                }
            }
        };

        function calculateFinances() {
            let sales = subscribers.reduce((acc, s) => acc + (parseFloat(s.iptvPrice)||0) + (parseFloat(s.iboPrice)||0), 0);
            let recharge = recharges.reduce((acc, r) => acc + (parseFloat(r.amount)||0), 0);
            document.getElementById('totalSales').innerText = sales.toFixed(2);
            document.getElementById('totalRecharge').innerText = recharge.toFixed(2);
            document.getElementById('netProfit').innerText = (sales - recharge).toFixed(2);
        }

        window.addRecharge = async () => {
            const amt = document.getElementById('rechargeAmount').value;
            const note = document.getElementById('rechargeNote').value;
            if(amt) {
                try {
                    await addDoc(collection(db, "recharges"), { 
                        amount: parseFloat(amt), 
                        note: note || '', 
                        date: new Date().toISOString(), 
                        userId: currentUser.uid 
                    });
                    document.getElementById('rechargeAmount').value = '';
                    document.getElementById('rechargeNote').value = '';
                } catch(e) {
                    alert("حدث خطأ أثناء إضافة عملية الشحن: " + e.message);
                }
            }
        };

        window.deleteRecharge = async (id) => { 
            if(confirm("حذف؟")) {
                try {
                    await deleteDoc(doc(db, "recharges", id)); 
                } catch(e) {
                    alert("حدث خطأ أثناء حذف عملية الشحن: " + e.message);
                }
            }
        };

        document.getElementById('subscriberForm').addEventListener('submit', async (e) => {
            e.preventDefault();
            const id = document.getElementById('editId').value;
            const startDate = document.getElementById('startDate').value;
            const duration = parseInt(document.getElementById('duration').value);
            
            let d = new Date(startDate); 
            if (!isNaN(d.getTime())) {
                d.setMonth(d.getMonth() + duration);
            } else {
                alert("تاريخ البدء غير صالح.");
                return;
            }
            const endDate = d.toISOString().split('T')[0];

            const data = {
                name: document.getElementById('name').value,
                phone: document.getElementById('phone').value,
                device: document.getElementById('deviceType').value,
                startDate, duration, endDate,
                iptvPrice: document.getElementById('iptvPrice').value,
                iboMac: document.getElementById('iboMac').value,
                iboKey: document.getElementById('iboKey').value,
                iboStartDate: document.getElementById('iboStartDate').value,
                iboDuration: document.getElementById('iboDuration').value,
                iboPrice: document.getElementById('iboPrice').value,
                host: document.getElementById('host').value,
                username: document.getElementById('username').value,
                password: document.getElementById('password').value,
                m3u: document.getElementById('m3u').value,
                userId: currentUser.uid
            };

            try {
                if(id) await updateDoc(doc(db, "subscribers", id), data);
                else await addDoc(collection(db, "subscribers"), data);
                showSection('dashboard');
            } catch(e) {
                alert("حدث خطأ أثناء حفظ البيانات: " + e.message);
            }
        });

        window.editSub = (id) => {
            const s = subscribers.find(x => x.id === id);
            if (!s) return;
            document.getElementById('formTitle').innerText = 'تعديل: ' + s.name;
            document.getElementById('editId').value = s.id;
            document.getElementById('name').value = s.name||'';
            document.getElementById('phone').value = s.phone||'';
            document.getElementById('deviceType').value = s.device||'';
            document.getElementById('startDate').value = s.startDate||'';
            document.getElementById('duration').value = s.duration||'';
            document.getElementById('iptvPrice').value = s.iptvPrice||'';
            document.getElementById('iboMac').value = s.iboMac||'';
            document.getElementById('iboKey').value = s.iboKey||'';
            document.getElementById('iboStartDate').value = s.iboStartDate||'';
            document.getElementById('iboDuration').value = s.iboDuration||'';
            document.getElementById('iboPrice').value = s.iboPrice||'';
            document.getElementById('host').value = s.host||'';
            document.getElementById('username').value = s.username||'';
            document.getElementById('password').value = s.password||'';
            document.getElementById('m3u').value = s.m3u||'';
            showSection('add');
            document.getElementById('detailsModal').style.display = 'none';
        };

        window.resetForm = () => { 
            document.getElementById('subscriberForm').reset(); 
            document.getElementById('editId').value = ''; 
            document.getElementById('formTitle').innerText = 'إضافة مشترك'; 
        }

        window.showDetails = (id) => {
            const s = subscribers.find(x => x.id === id);
            if (!s) return;
            const content = document.getElementById('detailsContent');
            const total = (parseFloat(s.iptvPrice)||0) + (parseFloat(s.iboPrice)||0);
            
            let iboStatus = "غير مفعل", iboEnd = "-";
            if (s.iboDuration === 'lifetime') { iboStatus = "مدى الحياة"; iboEnd = "∞"; } 
            else if (s.iboDuration === 'year' && s.iboStartDate) {
                let end = new Date(s.iboStartDate); end.setFullYear(end.getFullYear()+1);
                iboEnd = end.toISOString().split('T')[0];
                let diff = Math.ceil((end - new Date())/(1000*60*60*24));
                iboStatus = diff > 0 ? `${diff} يوم` : "منتهي";
            }
            const iptvDays = getDays(s.endDate);

            content.innerHTML = `
                <div class="detail-row"><span>الاسم:</span> <span>${s.name}</span></div>
                <div class="detail-row"><span>الهاتف:</span> <a href="tel:${s.phone}">${s.phone || '-'}</a></div>
                <div class="detail-row"><span>الجهاز:</span> <span>${s.device || '-'}</span></div>
                <h4 style="color:var(--primary);margin-top:15px;border-bottom:1px solid #eee;">IPTV (${iptvDays} يوم)</h4>
                <div class="detail-row"><span>البدء:</span> <span>${s.startDate}</span></div>
                <div class="detail-row"><span>الانتهاء:</span> <span>${s.endDate}</span></div>
                <h4 style="color:var(--primary);margin-top:15px;border-bottom:1px solid #eee;">IBO (${iboStatus})</h4>
                <div class="detail-row"><span>MAC:</span> <span style="font-size:0.9rem">${s.iboMac||'-'}</span></div>
                <div class="detail-row"><span>Key:</span> <span>${s.iboKey||'-'}</span></div>
                <div class="detail-row"><span>تاريخ التفعيل:</span> <span>${s.iboStartDate||'-'}</span></div>
                <h4 style="color:var(--primary);margin-top:15px;border-bottom:1px solid #eee;">السيرفر</h4>
                <div class="detail-row"><span>Host:</span> <span style="font-size:0.85rem">${s.host||'-'}</span></div>
                <div class="detail-row"><span>User:</span> <span>${s.username||'-'}</span></div>
                <div class="detail-row"><span>Pass:</span> <span>${s.password||'-'}</span></div>
                <div style="margin-top:5px"><strong>M3U:</strong><textarea class="form-control" rows="2" readonly>${s.m3u||''}</textarea></div>
                <div class="detail-row" style="background:#f0f9ff; padding:8px; margin-top:10px; border-radius:8px"><span>المجموع:</span> <span style="font-weight:bold">${total}</span></div>
            `;
            document.getElementById('modalEditBtn').onclick = () => editSub(s.id);
            document.getElementById('detailsModal').style.display = 'flex';
        };

        window.moveToTrash = async (id) => { 
            if(confirm("نقل للسلة؟")) { 
                const s = subscribers.find(x => x.id === id); 
                if (!s) return;
                try {
                    const { id: subDocId, ...dataPayload } = s;
                    await addDoc(collection(db, "trash"), {
                        ...dataPayload,
                        originalId: subDocId,
                        deletedAt: new Date().toISOString()
                    }); 
                    await deleteDoc(doc(db, "subscribers", id)); 
                } catch(e) {
                    alert("حدث خطأ أثناء نقل البيانات لسلة المهملات: " + e.message);
                }
            }
        };

        window.addDevice = async () => { 
            const v = document.getElementById('newDeviceInput').value.trim(); 
            if(v) {
                try {
                    await addDoc(collection(db, "managed_devices"), { name: v }); 
                    document.getElementById('newDeviceInput').value = ''; 
                } catch(e) {
                    alert("حدث خطأ أثناء إضافة الجهاز: " + e.message);
                }
            }
        };

        window.deleteDevice = async (id) => { 
            if(confirm("حذف؟")) {
                try {
                    await deleteDoc(doc(db, "managed_devices", id)); 
                } catch(e) {
                    alert("حدث خطأ أثناء الحذف: " + e.message);
                }
            }
        };

        function renderAdminDevices() { 
            const d = document.getElementById('devicesList'); 
            d.innerHTML = ''; 
            managedDevices.forEach(i => d.innerHTML += `<span style="background:#ddd;padding:5px 10px;border-radius:10px;display:inline-block">${i.name} <b style="color:red;cursor:pointer" onclick="deleteDevice('${i.id}')">&times;</b></span>`); 
        }

        window.renderDeviceSelect = () => { 
            const s = document.getElementById('deviceType'); 
            const old = s.value; 
            s.innerHTML = '<option value="">اختر</option>'; 
            managedDevices.forEach(i => s.innerHTML += `<option value="${i.name}">${i.name}</option>`); 
            s.value = old; 
        };
        
        function getDays(e) { 
            if (!e) return 0;
            const diff = new Date(e) - new Date();
            return isNaN(diff) ? 0 : Math.ceil(diff / (1000 * 60 * 60 * 24)); 
        }
        
        function updateChart() {
            const canvas = document.getElementById('salesChart');
            if(!canvas) return;
            const ctx = canvas.getContext('2d');
            const months = {};
            subscribers.forEach(s => { 
                if (s.startDate && typeof s.startDate === 'string') {
                    const m = s.startDate.substring(0,7); 
                    months[m] = (months[m]||0)+1; 
                }
            });
            const labels = Object.keys(months).sort();
            if(salesChartInstance) salesChartInstance.destroy();
            salesChartInstance = new Chart(ctx, { 
                type:'bar', 
                data:{
                    labels, 
                    datasets:[{
                        label:'الاشتراكات', 
                        data:labels.map(l=>months[l]), 
                        backgroundColor:'#4f46e5', 
                        borderRadius: 5
                    }]
                }, 
                options:{
                    maintainAspectRatio:false, 
                    plugins: { legend: { display: false } }, 
                    scales: { 
                        y: { beginAtZero: true, grid: { display: false } }, 
                        x: { grid: { display: false } } 
                    } 
                } 
            });
        }

        window.addEventListener('click', (e) => {
            const modal = document.getElementById('detailsModal');
            if (e.target === modal) {
                modal.style.display = 'none';
            }
        });
    </script>
</body>
</html>
