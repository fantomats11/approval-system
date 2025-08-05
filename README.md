<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบอนุมัติรูปภาพสินค้า</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap');
        body { 
            font-family: 'Sarabun', sans-serif; 
            margin: 0;
            padding: 0;
            overflow-x: hidden;
        }
        /* Iframe optimizations */
        .iframe-container {
            width: 100%;
            min-height: 100vh;
            background: linear-gradient(135deg, #f0f9ff 0%, #e0e7ff 100%);
        }
        /* Responsive adjustments for iframe */
        @media (max-width: 768px) {
            .mobile-stack { flex-direction: column !important; }
            .mobile-full { width: 100% !important; }
        }
        /* Text truncation */
        .line-clamp-2 {
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }
    </style>
</head>
<body class="iframe-container">
    <div class="w-full px-2 sm:px-4 py-4">
        <!-- Header -->
        <div class="bg-white rounded-lg shadow-md p-4 mb-6">
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 mobile-stack">
                <div class="mobile-full">
                    <h1 class="text-xl sm:text-2xl font-bold text-gray-800">ระบบอนุมัติรูปภาพสินค้า</h1>
                    <p class="text-gray-600 mt-1 text-sm">จัดการและอนุมัติรูปภาพสินค้าอย่างมีประสิทธิภาพ</p>
                </div>
                <button onclick="shareLink()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg font-medium transition-colors flex items-center gap-2 text-sm mobile-full justify-center">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.684 13.342C8.886 12.938 9 12.482 9 12c0-.482-.114-.938-.316-1.342m0 2.684a3 3 0 110-2.684m0 2.684l6.632 3.316m-6.632-6l6.632-3.316m0 0a3 3 0 105.367-2.684 3 3 0 00-5.367 2.684zm0 9.316a3 3 0 105.367 2.684 3 3 0 00-5.367-2.684z"></path>
                    </svg>
                    แชร์ลิงก์
                </button>
            </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-4 lg:gap-6">
            <!-- Upload Section -->
            <div class="lg:col-span-2">
                <div class="bg-white rounded-lg shadow-md p-4 mb-6">
                    <h2 class="text-lg font-semibold text-gray-800 mb-3">อัปโหลดรูปภาพสินค้า</h2>
                    <div class="border-2 border-dashed border-gray-300 rounded-lg p-4 text-center hover:border-blue-400 transition-colors">
                        <input type="file" id="imageInput" accept="image/*" class="hidden" onchange="handleImageUpload(event)">
                        <div onclick="document.getElementById('imageInput').click()" class="cursor-pointer">
                            <svg class="w-8 h-8 text-gray-400 mx-auto mb-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12"></path>
                            </svg>
                            <p class="text-gray-600 font-medium text-sm">คลิกเพื่ือเลือกรูปภาพ</p>
                            <p class="text-gray-400 text-xs mt-1">รองรับไฟล์ JPG, PNG, GIF</p>
                        </div>
                    </div>
                    
                    <div class="mt-3 grid grid-cols-1 sm:grid-cols-2 gap-3">
                        <input type="text" id="productName" placeholder="ชื่อสินค้า" class="border border-gray-300 rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                        <input type="text" id="productCode" placeholder="รหัสสินค้า" class="border border-gray-300 rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-blue-500 focus:border-transparent">
                    </div>
                    <textarea id="productDescription" placeholder="รายละเอียดสินค้า" class="w-full mt-3 border border-gray-300 rounded-lg px-3 py-2 text-sm focus:ring-2 focus:ring-blue-500 focus:border-transparent" rows="2"></textarea>
                </div>

                <!-- Pending Approval -->
                <div class="bg-white rounded-lg shadow-md p-4">
                    <h2 class="text-lg font-semibold text-gray-800 mb-3">รอการอนุมัติ</h2>
                    <div id="pendingItems" class="space-y-3">
                        <!-- Pending items will be added here -->
                    </div>
                </div>
            </div>

            <!-- History & Stats -->
            <div class="space-y-4">
                <!-- Stats -->
                <div class="bg-white rounded-lg shadow-md p-4">
                    <h2 class="text-lg font-semibold text-gray-800 mb-3">สถิติ</h2>
                    <div class="space-y-3">
                        <div class="flex justify-between items-center p-2 bg-green-50 rounded-lg">
                            <span class="text-green-700 font-medium text-sm">อนุมัติแล้ว</span>
                            <span id="approvedCount" class="text-green-800 font-bold text-lg">0</span>
                        </div>
                        <div class="flex justify-between items-center p-2 bg-red-50 rounded-lg">
                            <span class="text-red-700 font-medium text-sm">ปฏิเสธ</span>
                            <span id="rejectedCount" class="text-red-800 font-bold text-lg">0</span>
                        </div>
                        <div class="flex justify-between items-center p-2 bg-yellow-50 rounded-lg">
                            <span class="text-yellow-700 font-medium text-sm">รอดำเนินการ</span>
                            <span id="pendingCount" class="text-yellow-800 font-bold text-lg">0</span>
                        </div>
                    </div>
                </div>

                <!-- History -->
                <div class="bg-white rounded-lg shadow-md p-4">
                    <h2 class="text-lg font-semibold text-gray-800 mb-3">ประวัติการอนุมัติ</h2>
                    <div id="historyItems" class="space-y-2 max-h-64 overflow-y-auto">
                        <!-- History items will be added here -->
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal for Image Preview -->
    <div id="imageModal" class="fixed inset-0 bg-black bg-opacity-50 hidden items-center justify-center z-50 p-2">
        <div class="bg-white rounded-lg p-4 w-full max-w-2xl max-h-[90vh] overflow-auto">
            <div class="flex justify-between items-center mb-3">
                <h3 id="modalTitle" class="text-lg font-semibold text-gray-800"></h3>
                <button onclick="closeModal()" class="text-gray-500 hover:text-gray-700">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                    </svg>
                </button>
            </div>
            <img id="modalImage" class="w-full max-h-64 object-contain rounded-lg mb-3">
            <div id="modalDetails" class="text-gray-600 mb-4 text-sm"></div>
            <div class="flex gap-3 justify-end">
                <button onclick="approveItem()" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm">
                    อนุมัติ
                </button>
                <button onclick="rejectItem()" class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg font-medium transition-colors text-sm">
                    ปฏิเสธ
                </button>
            </div>
        </div>
    </div>

    <script>
        let currentItemId = null;
        let items = JSON.parse(localStorage.getItem('approvalItems') || '[]');
        let itemIdCounter = parseInt(localStorage.getItem('itemIdCounter') || '1');

        function saveToStorage() {
            localStorage.setItem('approvalItems', JSON.stringify(items));
            localStorage.setItem('itemIdCounter', itemIdCounter.toString());
        }

        function handleImageUpload(event) {
            const file = event.target.files[0];
            if (!file) return;

            const productName = document.getElementById('productName').value || 'ไม่ระบุชื่อ';
            const productCode = document.getElementById('productCode').value || 'ไม่ระบุรหัส';
            const productDescription = document.getElementById('productDescription').value || 'ไม่มีรายละเอียด';

            const reader = new FileReader();
            reader.onload = function(e) {
                const newItem = {
                    id: itemIdCounter++,
                    name: productName,
                    code: productCode,
                    description: productDescription,
                    image: e.target.result,
                    status: 'pending',
                    timestamp: new Date().toLocaleString('th-TH'),
                    approver: null
                };

                items.push(newItem);
                saveToStorage();
                renderPendingItems();
                updateStats();
                
                // Clear form
                document.getElementById('productName').value = '';
                document.getElementById('productCode').value = '';
                document.getElementById('productDescription').value = '';
                event.target.value = '';
            };
            reader.readAsDataURL(file);
        }

        function renderPendingItems() {
            const container = document.getElementById('pendingItems');
            const pendingItems = items.filter(item => item.status === 'pending');
            
            if (pendingItems.length === 0) {
                container.innerHTML = '<p class="text-gray-500 text-center py-8">ไม่มีรายการรอการอนุมัติ</p>';
                return;
            }

            container.innerHTML = pendingItems.map(item => `
                <div class="border border-gray-200 rounded-lg p-3 hover:shadow-md transition-shadow cursor-pointer" onclick="openModal(${item.id})">
                    <div class="flex gap-3">
                        <img src="${item.image}" alt="${item.name}" class="w-16 h-16 object-cover rounded-lg flex-shrink-0">
                        <div class="flex-1 min-w-0">
                            <h3 class="font-semibold text-gray-800 text-sm truncate">${item.name}</h3>
                            <p class="text-gray-600 text-xs">รหัส: ${item.code}</p>
                            <p class="text-gray-500 text-xs mt-1 line-clamp-2">${item.description}</p>
                            <p class="text-gray-400 text-xs mt-1">อัปโหลดเมื่อ: ${item.timestamp}</p>
                        </div>
                        <div class="flex items-center flex-shrink-0">
                            <span class="bg-yellow-100 text-yellow-800 px-2 py-1 rounded-full text-xs font-medium">รอดำเนินการ</span>
                        </div>
                    </div>
                </div>
            `).join('');
        }

        function renderHistory() {
            const container = document.getElementById('historyItems');
            const historyItems = items.filter(item => item.status !== 'pending').reverse();
            
            if (historyItems.length === 0) {
                container.innerHTML = '<p class="text-gray-500 text-center py-4">ยังไม่มีประวัติ</p>';
                return;
            }

            container.innerHTML = historyItems.map(item => `
                <div class="border-l-4 ${item.status === 'approved' ? 'border-green-500' : 'border-red-500'} pl-3 py-2">
                    <div class="flex justify-between items-start">
                        <div class="flex-1 min-w-0">
                            <h4 class="font-medium text-gray-800 text-sm truncate">${item.name}</h4>
                            <p class="text-xs text-gray-600">รหัส: ${item.code}</p>
                            <p class="text-xs text-gray-500 mt-1">${item.timestamp}</p>
                        </div>
                        <span class="px-2 py-1 rounded text-xs font-medium flex-shrink-0 ml-2 ${
                            item.status === 'approved' 
                                ? 'bg-green-100 text-green-800' 
                                : 'bg-red-100 text-red-800'
                        }">
                            ${item.status === 'approved' ? 'อนุมัติ' : 'ปฏิเสธ'}
                        </span>
                    </div>
                </div>
            `).join('');
        }

        function updateStats() {
            const approved = items.filter(item => item.status === 'approved').length;
            const rejected = items.filter(item => item.status === 'rejected').length;
            const pending = items.filter(item => item.status === 'pending').length;

            document.getElementById('approvedCount').textContent = approved;
            document.getElementById('rejectedCount').textContent = rejected;
            document.getElementById('pendingCount').textContent = pending;
        }

        function openModal(itemId) {
            const item = items.find(i => i.id === itemId);
            if (!item) return;

            currentItemId = itemId;
            document.getElementById('modalTitle').textContent = item.name;
            document.getElementById('modalImage').src = item.image;
            document.getElementById('modalDetails').innerHTML = `
                <p><strong>รหัสสินค้า:</strong> ${item.code}</p>
                <p><strong>รายละเอียด:</strong> ${item.description}</p>
                <p><strong>อัปโหลดเมื่อ:</strong> ${item.timestamp}</p>
            `;
            document.getElementById('imageModal').classList.remove('hidden');
            document.getElementById('imageModal').classList.add('flex');
        }

        function closeModal() {
            document.getElementById('imageModal').classList.add('hidden');
            document.getElementById('imageModal').classList.remove('flex');
            currentItemId = null;
        }

        function approveItem() {
            if (!currentItemId) return;
            
            const item = items.find(i => i.id === currentItemId);
            if (item) {
                item.status = 'approved';
                item.approver = 'ผู้อนุมัติ';
                item.approvedAt = new Date().toLocaleString('th-TH');
                saveToStorage();
                renderPendingItems();
                renderHistory();
                updateStats();
                closeModal();
            }
        }

        function rejectItem() {
            if (!currentItemId) return;
            
            const item = items.find(i => i.id === currentItemId);
            if (item) {
                item.status = 'rejected';
                item.approver = 'ผู้อนุมัติ';
                item.rejectedAt = new Date().toLocaleString('th-TH');
                saveToStorage();
                renderPendingItems();
                renderHistory();
                updateStats();
                closeModal();
            }
        }

        function shareLink() {
            const url = window.location.href;
            if (navigator.share) {
                navigator.share({
                    title: 'ระบบอนุมัติรูปภาพสินค้า',
                    text: 'เข้าร่วมระบบอนุมัติรูปภาพสินค้า',
                    url: url
                });
            } else {
                navigator.clipboard.writeText(url).then(() => {
                    alert('คัดลอกลิงก์เรียบร้อยแล้ว!');
                });
            }
        }

        // Initialize
        renderPendingItems();
        renderHistory();
        updateStats();

        // Handle URL parameters for shared links
        const urlParams = new URLSearchParams(window.location.search);
        if (urlParams.get('shared') === 'true') {
            // Show notification that this is a shared session
            const notification = document.createElement('div');
            notification.className = 'fixed top-4 right-4 bg-blue-600 text-white px-6 py-3 rounded-lg shadow-lg z-50';
            notification.textContent = 'คุณเข้าร่วมระบบผ่านลิงก์ที่แชร์';
            document.body.appendChild(notification);
            setTimeout(() => notification.remove(), 5000);
        }
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'96a30ad4a57a99a6',t:'MTc1NDM2MjgwNi4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
