
```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام إدارة المؤسسات</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/chart.js@3.9.1/dist/chart.min.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary: #2563eb;
            --primary-light: #dbeafe;
            --secondary: #0f766e;
            --accent: #d97706;
            --light: #f8fafc;
            --dark: #0f172a;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
        }
        
        body {
            font-family: 'Cairo', sans-serif;
            background-color: #f1f5f9;
            color: #334155;
        }
        
        .sidebar-icon {
            font-size: 1.5rem;
            line-height: 1;
        }
        
        .chart-container {
            position: relative;
            width: 100%;
            height: 300px;
        }
        
        .org-chart .node {
            background-color: #f0f9ff;
            border: 1px solid #bae6fd;
            border-radius: 0.5rem;
            padding: 1rem;
            margin: 0.5rem;
            text-align: center;
            box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.05);
            font-size: 0.9rem;
            font-weight: 600;
            color: #0369a1;
            position: relative;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .org-chart .node:hover {
            background-color: #e0f2fe;
            transform: translateY(-2px);
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
        }
        
        .modal {
            position: fixed;
            z-index: 100;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.4);
            display: flex;
            align-items: center;
            justify-content: center;
            backdrop-filter: blur(2px);
        }
        
        .modal-content {
            background-color: #fefefe;
            margin: auto;
            padding: 20px;
            border-radius: 8px;
            width: 80%;
            max-width: 600px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            animation: modalAppear 0.3s ease-out;
        }
        
        @keyframes modalAppear {
            from { opacity: 0; transform: translateY(-20px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .close-button {
            color: #aaa;
            float: left;
            font-size: 28px;
            font-weight: bold;
        }
        
        .close-button:hover,
        .close-button:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }
        
        .nav-link.active {
            background-color: var(--primary-light);
            color: var(--primary);
            border-right: 4px solid var(--primary);
        }
        
        .nav-link:hover:not(.active) {
            background-color: #f1f5f9;
        }
        
        .kpi-card {
            transition: all 0.3s ease;
            border-left: 4px solid;
        }
        
        .kpi-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
            animation: fadeIn 0.5s;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        
        .badge {
            display: inline-block;
            padding: 0.25rem 0.5rem;
            border-radius: 9999px;
            font-size: 0.75rem;
            font-weight: 600;
        }
        
        .table-row:hover {
            background-color: #f8fafc !important;
        }
    </style>
</head>
<body class="flex h-screen overflow-hidden">
    <!-- الشريط الجانبي -->
    <div class="w-64 bg-white shadow-lg flex flex-col">
        <div class="p-4 border-b">
            <div class="flex items-center justify-center">
                <div class="bg-gray-200 border-2 border-dashed rounded-xl w-16 h-16" />
                <div class="mr-3">
                    <h2 class="text-xl font-bold text-gray-800">نظام الإدارة</h2>
                    <p class="text-sm text-gray-600">لوحة التحكم الرئيسية</p>
                </div>
            </div>
        </div>
        
        <nav class="flex-1 overflow-y-auto py-4">
            <ul>
                <li>
                    <a href="#" data-section="dashboard" class="nav-link active flex items-center p-4 text-gray-700 hover:bg-gray-50">
                        <i class="fas fa-tachometer-alt sidebar-icon ml-3"></i>
                        <span>لوحة التحكم</span>
                    </a>
                </li>
                <li>
                    <a href="#" data-section="hr" class="nav-link flex items-center p-4 text-gray-700 hover:bg-gray-50">
                        <i class="fas fa-users sidebar-icon ml-3"></i>
                        <span>الموارد البشرية</span>
                    </a>
                </li>
                <li>
                    <a href="#" data-section="operations" class="nav-link flex items-center p-4 text-gray-700 hover:bg-gray-50">
                        <i class="fas fa-cogs sidebar-icon ml-3"></i>
                        <span>العمليات</span>
                    </a>
                </li>
                <li>
                    <a href="#" data-section="org-structure" class="nav-link flex items-center p-4 text-gray-700 hover:bg-gray-50">
                        <i class="fas fa-sitemap sidebar-icon ml-3"></i>
                        <span>الهيكل التنظيمي</span>
                    </a>
                </li>
                <li>
                    <a href="#" data-section="directorates" class="nav-link flex items-center p-4 text-gray-700 hover:bg-gray-50">
                        <i class="fas fa-building sidebar-icon ml-3"></i>
                        <span>الإدارات</span>
                    </a>
                </li>
                <li>
                    <a href="#" data-section="reports" class="nav-link flex items-center p-4 text-gray-700 hover:bg-gray-50">
                        <i class="fas fa-chart-bar sidebar-icon ml-3"></i>
                        <span>التقارير</span>
                    </a>
                </li>
            </ul>
        </nav>
        
        <div class="p-4 border-t">
            <div class="flex items-center">
                <div class="bg-gray-200 border-2 border-dashed rounded-xl w-10 h-10" />
                <div class="mr-3">
                    <p class="font-medium text-gray-800">أحمد محمد</p>
                    <p class="text-xs text-gray-600">مدير النظام</p>
                </div>
            </div>
        </div>
    </div>

    <!-- المحتوى الرئيسي -->
    <div class="flex-1 flex flex-col overflow-hidden">
        <!-- شريط العنوان -->
        <header class="bg-white shadow-sm py-4 px-6 flex justify-between items-center">
            <div>
                <h1 class="text-xl font-bold text-gray-800" id="page-title">لوحة التحكم</h1>
            </div>
            <div class="flex items-center">
                <div class="relative mr-4">
                    <input type="text" placeholder="بحث..." class="border rounded-full py-2 px-4 pl-10 w-64 focus:outline-none focus:ring-2 focus:ring-blue-200">
                    <i class="fas fa-search absolute left-3 top-3 text-gray-400"></i>
                </div>
                <button class="p-2 text-gray-500 hover:text-gray-700 relative">
                    <i class="fas fa-bell text-xl"></i>
                    <span class="absolute top-0 right-0 w-2 h-2 bg-red-500 rounded-full"></span>
                </button>
                <button class="p-2 text-gray-500 hover:text-gray-700 ml-2">
                    <i class="fas fa-cog text-xl"></i>
                </button>
            </div>
        </header>

        <!-- محتوى الصفحات -->
        <div class="flex-1 overflow-y-auto p-6 bg-gray-50">
            <!-- لوحة التحكم -->
            <section id="dashboard" class="page-section">
                <div class="mb-6">
                    <h2 class="text-2xl font-bold text-gray-800 mb-4">مؤشرات الأداء الرئيسية</h2>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
                        <div class="kpi-card bg-white rounded-lg shadow p-6 border-l-4 border-blue-500">
                            <div class="flex justify-between items-start">
                                <div>
                                    <p class="text-gray-600">المالية</p>
                                    <h3 class="text-3xl font-bold mt-2">8.2M</h3>
                                    <p class="text-green-500 mt-1 flex items-center">
                                        <i class="fas fa-arrow-up mr-1"></i> 12.5%
                                    </p>
                                </div>
                                <div class="bg-blue-100 p-3 rounded-full">
                                    <i class="fas fa-wallet text-blue-600 text-xl"></i>
                                </div>
                            </div>
                        </div>
                        
                        <div class="kpi-card bg-white rounded-lg shadow p-6 border-l-4 border-teal-500">
                            <div class="flex justify-between items-start">
                                <div>
                                    <p class="text-gray-600">العملاء</p>
                                    <h3 class="text-3xl font-bold mt-2">4,892</h3>
                                    <p class="text-green-500 mt-1 flex items-center">
                                        <i class="fas fa-arrow-up mr-1"></i> 3.2%
                                    </p>
                                </div>
                                <div class="bg-teal-100 p-3 rounded-full">
                                    <i class="fas fa-users text-teal-600 text-xl"></i>
                                </div>
                            </div>
                        </div>
                        
                        <div class="kpi-card bg-white rounded-lg shadow p-6 border-l-4 border-amber-500">
                            <div class="flex justify-between items-start">
                                <div>
                                    <p class="text-gray-600">العمليات</p>
                                    <h3 class="text-3xl font-bold mt-2">1,248</h3>
                                    <p class="text-red-500 mt-1 flex items-center">
                                        <i class="fas fa-arrow-down mr-1"></i> 1.7%
                                    </p>
                                </div>
                                <div class="bg-amber-100 p-3 rounded-full">
                                    <i class="fas fa-cogs text-amber-600 text-xl"></i>
                                </div>
                            </div>
                        </div>
                        
                        <div class="kpi-card bg-white rounded-lg shadow p-6 border-l-4 border-emerald-500">
                            <div class="flex justify-between items-start">
                                <div>
                                    <p class="text-gray-600">الموظفين</p>
                                    <h3 class="text-3xl font-bold mt-2">324</h3>
                                    <p class="text-green-500 mt-1 flex items-center">
                                        <i class="fas fa-arrow-up mr-1"></i> 5.3%
                                    </p>
                                </div>
                                <div class="bg-emerald-100 p-3 rounded-full">
                                    <i class="fas fa-user-plus text-emerald-600 text-xl"></i>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                    <div class="bg-white rounded-lg shadow p-6">
                        <div class="flex justify-between items-center mb-6">
                            <h3 class="text-xl font-bold text-gray-800">أداء المشاريع</h3>
                            <div class="flex">
                                <button class="px-3 py-1 bg-gray-100 rounded-lg text-sm font-medium mr-2">شهري</button>
                                <button class="px-3 py-1 bg-blue-100 text-blue-600 rounded-lg text-sm font-medium">سنوي</button>
                            </div>
                        </div>
                        <div class="chart-container">
                            <canvas id="performanceChart"></canvas>
                        </div>
                    </div>
                    
                    <div class="bg-white rounded-lg shadow p-6">
                        <div class="flex justify-between items-center mb-6">
                            <h3 class="text-xl font-bold text-gray-800">توزيع المهام</h3>
                            <button class="text-blue-600 font-medium">
                                <i class="fas fa-plus mr-1"></i> إضافة مهمة
                            </button>
                        </div>
                        <div class="space-y-4">
                            <div class="flex items-center p-4 bg-gray-50 rounded-lg">
                                <div class="w-10 h-10 bg-blue-100 rounded-full flex items-center justify-center text-blue-600 mr-3">
                                    <i class="fas fa-file-invoice"></i>
                                </div>
                                <div class="flex-1">
                                    <h4 class="font-medium">تقرير الربع السنوي</h4>
                                    <p class="text-sm text-gray-500">إدارة المالية</p>
                                </div>
                                <span class="badge bg-blue-100 text-blue-600">قيد العمل</span>
                            </div>
                            
                            <div class="flex items-center p-4 bg-gray-50 rounded-lg">
                                <div class="w-10 h-10 bg-amber-100 rounded-full flex items-center justify-center text-amber-600 mr-3">
                                    <i class="fas fa-users"></i>
                                </div>
                                <div class="flex-1">
                                    <h4 class="font-medium">اجتماع الإدارة</h4>
                                    <p class="text-sm text-gray-500">إدارة التخطيط</p>
                                </div>
                                <span class="badge bg-amber-100 text-amber-600">معلق</span>
                            </div>
                            
                            <div class="flex items-center p-4 bg-gray-50 rounded-lg">
                                <div class="w-10 h-10 bg-emerald-100 rounded-full flex items-center justify-center text-emerald-600 mr-3">
                                    <i class="fas fa-chart-line"></i>
                                </div>
                                <div class="flex-1">
                                    <h4 class="font-medium">تحليل السوق</h4>
                                    <p class="text-sm text-gray-500">إدارة التسويق</p>
                                </div>
                                <span class="badge bg-emerald-100 text-emerald-600">مكتمل</span>
                            </div>
                            
                            <div class="flex items-center p-4 bg-gray-50 rounded-lg">
                                <div class="w-10 h-10 bg-purple-100 rounded-full flex items-center justify-center text-purple-600 mr-3">
                                    <i class="fas fa-laptop"></i>
                                </div>
                                <div class="flex-1">
                                    <h4 class="font-medium">تحديث النظام</h4>
                                    <p class="text-sm text-gray-500">إدارة التقنية</p>
                                </div>
                                <span class="badge bg-purple-100 text-purple-600">قيد العمل</span>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- الموارد البشرية -->
            <section id="hr" class="page-section hidden">
                <div class="bg-white rounded-lg shadow mb-6">
                    <div class="p-6 border-b">
                        <div class="flex justify-between items-center">
                            <h2 class="text-2xl font-bold text-gray-800">إدارة الموارد البشرية</h2>
                            <button class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700">
                                <i class="fas fa-plus mr-2"></i> إضافة موظف
                            </button>
                        </div>
                    </div>
                    
                    <div class="p-6">
                        <div class="flex justify-between items-center mb-6">
                            <div class="flex space-x-2">
                                <button class="px-4 py-2 bg-blue-100 text-blue-600 rounded-lg font-medium">الكل</button>
                                <button class="px-4 py-2 bg-gray-100 text-gray-600 rounded-lg font-medium">الإدارة</button>
                                <button class="px-4 py-2 bg-gray-100 text-gray-600 rounded-lg font-medium">المشاريع</button>
                                <button class="px-4 py-2 bg-gray-100 text-gray-600 rounded-lg font-medium">الدعم</button>
                            </div>
                            
                            <div class="relative">
                                <input type="text" placeholder="بحث باسم الموظف..." class="border rounded-lg py-2 px-4 pl-10 w-64 focus:outline-none focus:ring-2 focus:ring-blue-200">
                                <i class="fas fa-search absolute left-3 top-3 text-gray-400"></i>
                            </div>
                        </div>
                        
                        <div class="overflow-x-auto">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead class="bg-gray-50">
                                    <tr>
                                        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">الموظف</th>
                                        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">الوظيفة</th>
                                        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">القسم</th>
                                        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">الحالة</th>
                                        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">تاريخ التعيين</th>
                                        <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">الإجراءات</th>
                                    </tr>
                                </thead>
                                <tbody class="bg-white divide-y divide-gray-200">
                                    <tr class="table-row hover:bg-gray-50">
                                        <td class="px-6 py-4 whitespace-nowrap">
                                            <div class="flex items-center">
                                                <div class="bg-gray-200 border-2 border-dashed rounded-xl w-10 h-10" />
                                                <div class="mr-4">
                                                    <div class="text-sm font-medium text-gray-900">أحمد عبد الله</div>
                                                    <div class="text-sm text-gray-500">ahmed@company.com</div>
                                                </div>
                                            </div>
                                        </td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">مدير المشاريع</td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">المشاريع</td>
                                        <td class="px-6 py-4 whitespace-nowrap">
                                            <span class="badge bg-green-100 text-green-800">نشط</span>
                                        </td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">15/03/2020</td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                                            <button class="text-blue-600 hover:text-blue-900 mr-3"><i class="fas fa-edit"></i></button>
                                            <button class="text-red-600 hover:text-red-900"><i class="fas fa-trash"></i></button>
                                        </td>
                                    </tr>
                                    <tr class="table-row hover:bg-gray-50">
                                        <td class="px-6 py-4 whitespace-nowrap">
                                            <div class="flex items-center">
                                                <div class="bg-gray-200 border-2 border-dashed rounded-xl w-10 h-10" />
                                                <div class="mr-4">
                                                    <div class="text-sm font-medium text-gray-900">سارة محمد</div>
                                                    <div class="text-sm text-gray-500">sara@company.com</div>
                                                </div>
                                            </div>
                                        </td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">مصممة UI/UX</td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">التقنية</td>
                                        <td class="px-6 py-4 whitespace-nowrap">
                                            <span class="badge bg-green-100 text-green-800">نشط</span>
                                        </td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">22/07/2021</td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                                            <button class="text-blue-600 hover:text-blue-900 mr-3"><i class="fas fa-edit"></i></button>
                                            <button class="text-red-600 hover:text-red-900"><i class="fas fa-trash"></i></button>
                                        </td>
                                    </tr>
                                    <tr class="table-row hover:bg-gray-50">
                                        <td class="px-6 py-4 whitespace-nowrap">
                                            <div class="flex items-center">
                                                <div class="bg-gray-200 border-2 border-dashed rounded-xl w-10 h-10" />
                                                <div class="mr-4">
                                                    <div class="text-sm font-medium text-gray-900">خالد حسن</div>
                                                    <div class="text-sm text-gray-500">khaled@company.com</div>
                                                </div>
                                            </div>
                                        </td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">مطور برمجيات</td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">التقنية</td>
                                        <td class="px-6 py-4 whitespace-nowrap">
                                            <span class="badge bg-yellow-100 text-yellow-800">إجازة</span>
                                        </td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">10/11/2019</td>
                                        <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                                            <button class="text-blue-600 hover:text-blue-900 mr-3"><i class="fas fa-edit"></i></button>
                                            <button class="text-red-600 hover:text-red-900"><i class="fas fa-trash"></i></button>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- الهيكل التنظيمي -->
            <section id="org-structure" class="page-section hidden">
                <div class="bg-white rounded-lg shadow p-6">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800">الهيكل التنظيمي</h2>
                        <div class="flex space-x-3">
                            <button class="px-4 py-2 bg-gray-100 text-gray-600 rounded-lg font-medium">
                                <i class="fas fa-download mr-2"></i> تصدير
                            </button>
                            <button class="px-4 py-2 bg-blue-600 text-white rounded-lg font-medium hover:bg-blue-700">
                                <i class="fas fa-plus mr-2"></i> إضافة وحدة
                            </button>
                        </div>
                    </div>
                    
                    <div class="org-chart">
                        <div class="level">
                            <div class="node" data-node="1">
                                <div class="font-bold">المدير العام</div>
                                <div class="text-sm">أ. أحمد محمد</div>
                            </div>
                        </div>
                        
                        <div class="level">
                            <div class="children flex justify-center">
                                <div class="node" data-node="2">
                                    <div class="font-bold">نائب المدير العام</div>
                                    <div class="text-sm">أ. سعيد عبد الله</div>
                                </div>
                                <div class="node" data-node="3">
                                    <div class="font-bold">المدير المالي</div>
                                    <div class="text-sm">أ. محمد خالد</div>
                                </div>
                                <div class="node" data-node="4">
                                    <div class="font-bold">مدير العمليات</div>
                                    <div class="text-sm">أ. ليلى أحمد</div>
                                </div>
                            </div>
                        </div>
                        
                        <div class="level">
                            <div class="children flex justify-center">
                                <div class="children-container">
                                    <div class="children">
                                        <div class="node" data-node="5">
                                            <div class="font-bold">رئيس قسم المحاسبة</div>
                                            <div class="text-sm">أ. عمر ناصر</div>
                                        </div>
                                    </div>
                                </div>
                                <div class="children-container">
                                    <div class="children">
                                        <div class="node" data-node="6">
                                            <div class="font-bold">رئيس قسم المشتريات</div>
                                            <div class="text-sm">أ. سمر علي</div>
                                        </div>
                                        <div class="node" data-node="7">
                                            <div class="font-bold">رئيس قسم المبيعات</div>
                                            <div class="text-sm">أ. خالد سعد</div>
                                        </div>
                                    </div>
                                </div>
                                <div class="children-container">
                                    <div class="children">
                                        <div class="node" data-node="8">
                                            <div class="font-bold">رئيس قسم الجودة</div>
                                            <div class="text-sm">أ. نورة حسن</div>
                                        </div>
                                        <div class="node" data-node="9">
                                            <div class="font-bold">رئيس قسم الدعم</div>
                                            <div class="text-sm">أ. فهد محمد</div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
            
            <!-- التقارير -->
            <section id="reports" class="page-section hidden">
                <div class="bg-white rounded-lg shadow">
                    <div class="p-6 border-b">
                        <h2 class="text-2xl font-bold text-gray-800">التقارير والإحصائيات</h2>
                    </div>
                    
                    <div class="p-6">
                        <div class="flex mb-6 border-b">
                            <button class="tab-btn px-4 py-2 font-medium text-gray-600 border-b-2 border-transparent hover:text-blue-600" data-tab="financial">المالية</button>
                            <button class="tab-btn px-4 py-2 font-medium text-gray-600 border-b-2 border-transparent hover:text-blue-600" data-tab="operations">العمليات</button>
                            <button class="tab-btn px-4 py-2 font-medium text-gray-600 border-b-2 border-transparent hover:text-blue-600" data-tab="hr-reports">الموارد البشرية</button>
                            <button class="tab-btn px-4 py-2 font-medium text-gray-600 border-b-2 border-transparent hover:text-blue-600" data-tab="projects">المشاريع</button>
                        </div>
                        
                        <div id="financial" class="tab-content active">
                            <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
                                <div class="bg-gray-50 rounded-lg p-6">
                                    <h3 class="text-lg font-bold text-gray-800 mb-4">الإيرادات والمصروفات</h3>
                                    <div class="chart-container">
                                        <canvas id="revenueChart"></canvas>
                                    </div>
                                </div>
                                <div class="bg-gray-50 rounded-lg p-6">
                                    <h3 class="text-lg font-bold text-gray-800 mb-4">توزيع الميزانية</h3>
                                    <div class="chart-container">
                                        <canvas id="budgetChart"></canvas>
                                    </div>
                                </div>
                            </div>
                            
                            <div class="bg-gray-50 rounded-lg p-6">
                                <h3 class="text-lg font-bold text-gray-800 mb-4">آخر التقارير المالية</h3>
                                <div class="overflow-x-auto">
                                    <table class="min-w-full divide-y divide-gray-200">
                                        <thead class="bg-gray-100">
                                            <tr>
                                                <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">التقرير</th>
                                                <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">الفترة</th>
                                                <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">الحالة</th>
                                                <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">تاريخ الإصدار</th>
                                                <th class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">الإجراءات</th>
                                            </tr>
                                        </thead>
                                        <tbody class="bg-white divide-y divide-gray-200">
                                            <tr class="hover:bg-gray-50">
                                                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">تقرير الربع الأول 2023</td>
                                                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">يناير - مارس 2023</td>
                                                <td class="px-6 py-4 whitespace-nowrap">
                                                    <span class="badge bg-green-100 text-green-800">مكتمل</span>
                                                </td>
                                                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">05/04/2023</td>
                                                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                                                    <button class="text-blue-600 hover:text-blue-900 mr-3"><i class="fas fa-eye"></i> عرض</button>
                                                    <button class="text-gray-600 hover:text-gray-900"><i class="fas fa-download"></i> تحميل</button>
                                                </td>
                                            </tr>
                                            <tr class="hover:bg-gray-50">
                                                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">تقرير الربع الثاني 2023</td>
                                                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">أبريل - يونيو 2023</td>
                                                <td class="px-6 py-4 whitespace-nowrap">
                                                    <span class="badge bg-yellow-100 text-yellow-800">قيد المراجعة</span>
                                                </td>
                                                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">-</td>
                                                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium">
                                                    <button class="text-blue-600 hover:text-blue-900 mr-3"><i class="fas fa-edit"></i> تعديل</button>
                                                </td>
                                            </tr>
                                        </tbody>
                                    </table>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>
        </div>
    </div>

    <!-- Modal Structure -->
    <div id="nodeModal" class="modal hidden">
        <div class="modal-content">
            <span class="close-button">&times;</span>
            <h2 class="text-xl font-bold text-gray-800 mb-4" id="modalTitle">تفاصيل الوحدة التنظيمية</h2>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">اسم الوحدة</label>
                    <input type="text" class="w-full px-3 py-2 border rounded-lg" value="إدارة المشاريع">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">المسؤول</label>
                    <input type="text" class="w-full px-3 py-2 border rounded-lg" value="أ. أحمد محمد">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">عدد الموظفين</label>
                    <input type="text" class="w-full px-3 py-2 border rounded-lg" value="24">
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">تاريخ الإنشاء</label>
                    <input type="text" class="w-full px-3 py-2 border rounded-lg" value="15/03/2020">
                </div>
            </div>
            <div class="mb-4">
                <label class="block text-sm font-medium text-gray-700 mb-1">الوصف</label>
                <textarea class="w-full px-3 py-2 border rounded-lg" rows="3">إدارة وتنفيذ مشاريع المؤسسة وفق المعايير المحددة</textarea>
            </div>
            <div class="flex justify-end space-x-3">
                <button class="px-4 py-2 bg-gray-100 text-gray-700 rounded-lg">إلغاء</button>
                <button class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700">حفظ التغييرات</button>
            </div>
        </div>
    </div>

    <script>
        // Navigation
        document.querySelectorAll('.nav-link').forEach(link => {
            link.addEventListener('click', function(e) {
                e.preventDefault();
                
                // Update active link
                document.querySelectorAll('.nav-link').forEach(el => el.classList.remove('active'));
                this.classList.add('active');
                
                // Show correct section
                const sectionId = this.getAttribute('data-section');
                document.querySelectorAll('.page-section').forEach(section => {
                    section.classList.add('hidden');
                });
                document.getElementById(sectionId).classList.remove('hidden');
                
                // Update page title
                document.getElementById('page-title').textContent = this.querySelector('span').textContent;
            });
        });
        
        // Tab navigation
        document.querySelectorAll('.tab-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                const tabId = this.getAttribute('data-tab');
                
                // Update active tab button
                document.querySelectorAll('.tab-btn').forEach(el => {
                    el.classList.remove('text-blue-600', 'border-blue-600');
                });
                this.classList.add('text-blue-600', 'border-blue-600');
                
                // Show correct tab content
                document.querySelectorAll('.tab-content').forEach(tab => {
                    tab.classList.remove('active');
                });
                document.getElementById(tabId).classList.add('active');
            });
        });
        
        // Org chart node click
        document.querySelectorAll('.node').forEach(node => {
            node.addEventListener('click', function() {
                document.getElementById('nodeModal').classList.remove('hidden');
            });
        });
        
        // Modal close
        document.querySelector('.close-button').addEventListener('click', function() {
            document.getElementById('nodeModal').classList.add('hidden');
        });
        
        // Close modal when clicking outside
        window.addEventListener('click', function(e) {
            const modal = document.getElementById('nodeModal');
            if (e.target === modal) {
                modal.classList.add('hidden');
            }
        });
        
        // Initialize charts after page load
        window.addEventListener('load', function() {
            // Performance Chart
            const perfCtx = document.getElementById('performanceChart').getContext('2d');
            new Chart(perfCtx, {
                type: 'line',
                data: {
                    labels: ['يناير', 'فبراير', 'مارس', 'أبريل', 'مايو', 'يونيو'],
                    datasets: [{
                        label: 'أداء المشاريع',
                        data: [75, 80, 85, 70, 90, 95],
                        borderColor: '#3b82f6',
                        backgroundColor: 'rgba(59, 130, 246, 0.1)',
                        fill: true,
                        tension: 0.3
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            display: false
                        }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            max: 100,
                            ticks: {
                                callback: function(value) {
                                    return value + '%';
                                }
                            }
                        }
                    }
                }
            });
            
            // Revenue Chart
            const revCtx = document.getElementById('revenueChart').getContext('2d');
            new Chart(revCtx, {
                type: 'bar',
                data: {
                    labels: ['يناير', 'فبراير', 'مارس', 'أبريل', 'مايو', 'يونيو'],
                    datasets: [
                        {
                            label: 'الإيرادات',
                            data: [1200000, 1350000, 1400000, 1550000, 1600000, 1850000],
                            backgroundColor: '#10b981'
                        },
                        {
                            label: 'المصروفات',
                            data: [950000, 1000000, 1050000, 1100000, 1150000, 1200000],
                            backgroundColor: '#ef4444'
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        x: {
                            grid: {
                                display: false
                            }
                        },
                        y: {
                            beginAtZero: true,
                            ticks: {
                                callback: function(value) {
                                    return (value / 1000000).toFixed(1) + 'M';
                                }
                            }
                        }
                    }
                }
            });
            
            // Budget Chart
            const budgetCtx = document.getElementById('budgetChart').getContext('2d');
            new Chart(budgetCtx, {
                type: 'doughnut',
                data: {
                    labels: ['التشغيل', 'التسويق', 'التطوير', 'الموارد البشرية', 'الإدارة'],
                    datasets: [{
                        data: [35, 20, 25, 15, 5],
                        backgroundColor: [
                            '#3b82f6',
                            '#10b981',
                            '#f59e0b',
                            '#ef4444',
                            '#8b5cf6'
                        ]
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            position: 'bottom'
                        }
                    }
                }
            });
        });
    </script>
</body>
</html>
```
# surraed
