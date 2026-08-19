<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Video Library</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Prompt:wght@300;400;600&display=swap');
        body { font-family: 'Prompt', sans-serif; }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">

    <nav class="bg-indigo-600 text-white shadow-lg">
        <div class="max-w-7xl mx-auto px-4 py-4 flex items-center justify-between">
            <h1 class="text-2xl font-bold"><i class="fas fa-video mr-2"></i> Video Library</h1>
        </div>
    </nav>

    <div class="max-w-7xl mx-auto px-4 py-8">
        <div class="flex flex-col lg:flex-row gap-8">

            <div class="w-full lg:w-1/3">
                <div class="bg-white p-6 rounded-xl shadow-md sticky top-6">
                    <h2 class="text-xl font-semibold mb-4 text-gray-800 border-b pb-2">เพิ่มวิดีโอใหม่</h2>
                    <form id="videoForm" class="space-y-4">
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">ชื่อวิดีโอ (Title)</label>
                            <input type="text" id="title" required placeholder="เช่น สอนเขียนโปรแกรมเบื้องต้น"
                                class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition">
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">หมวดหมู่ (Category)</label>
                            <select id="category" required
                                class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition">
                                <option value="การศึกษา">การศึกษา</option>
                                <option value="บันเทิง">บันเทิง</option>
                                <option value="กีฬา">กีฬา</option>
                                <option value="เพลง">เพลง</option>
                                <option value="เกม">เกม</option>
                                <option value="อื่นๆ">อื่นๆ</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-sm font-medium text-gray-700 mb-1">ลิงก์วิดีโอ (URL)</label>
                            <input type="url" id="url" required placeholder="ลิงก์ YouTube หรือ .mp4"
                                class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition">
                        </div>
                        <button type="submit"
                            class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2 px-4 rounded-lg transition duration-200">
                            <i class="fas fa-plus-circle mr-1"></i> บันทึกวิดีโอ
                        </button>
                    </form>
                </div>
            </div>

            <div class="w-full lg:w-2/3">

                <div class="bg-white p-4 rounded-xl shadow-md mb-6 flex flex-col sm:flex-row gap-4 justify-between items-center">
                    <div class="w-full sm:w-1/2 relative">
                        <i class="fas fa-search absolute left-3 top-3 text-gray-400"></i>
                        <input type="text" id="searchInput" placeholder="ค้นหาชื่อวิดีโอ..."
                            class="w-full pl-10 pr-4 py-2 border rounded-lg focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                    <div class="w-full sm:w-1/3">
                        <select id="filterCategory"
                            class="w-full px-4 py-2 border rounded-lg focus:ring-2 focus:ring-indigo-500 outline-none">
                            <option value="All">ทุกหมวดหมู่</option>
                            <option value="การศึกษา">การศึกษา</option>
                            <option value="บันเทิง">บันเทิง</option>
                            <option value="กีฬา">กีฬา</option>
                            <option value="เพลง">เพลง</option>
                            <option value="เกม">เกม</option>
                            <option value="อื่นๆ">อื่นๆ</option>
                        </select>
                    </div>
                </div>

                <div id="videoGrid" class="grid grid-cols-1 md:grid-cols-2 gap-6"></div>

            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            let videos = JSON.parse(localStorage.getItem('videoLibraryData')) || [];

            const form = document.getElementById('videoForm');
            const grid = document.getElementById('videoGrid');
            const searchInput = document.getElementById('searchInput');
            const filterCategory = document.getElementById('filterCategory');

            function getEmbedUrl(url) {
                if (url.includes('youtube.com/watch?v=')) {
                    return url.replace('watch?v=', 'embed/').split('&')[0];
                } else if (url.includes('youtu.be/')) {
                    return url.replace('youtu.be/', 'youtube.com/embed/');
                }
                return url;
            }

            function renderVideos() {
                const searchText = searchInput.value.toLowerCase();
                const categoryText = filterCategory.value;

                grid.innerHTML = '';

                const filteredVideos = videos.filter(v => {
                    const matchTitle = v.title.toLowerCase().includes(searchText);
                    const matchCategory = categoryText === 'All' || v.category === categoryText;
                    return matchTitle && matchCategory;
                });

                if (filteredVideos.length === 0) {
                    grid.innerHTML = `
                        <div class="col-span-full bg-white p-8 rounded-xl shadow-sm text-center">
                            <i class="fas fa-film text-4xl text-gray-300 mb-3"></i>
                            <p class="text-gray-500">ไม่พบวิดีโอ ค้นหาใหม่หรือเพิ่มวิดีโอของคุณเลย</p>
                        </div>`;
                    return;
                }

                filteredVideos.forEach(v => {
                    const embedUrl = getEmbedUrl(v.url);
                    const isYoutube = embedUrl.includes('youtube') || embedUrl.includes('embed');

                    let mediaElement = '';
                    if (isYoutube) {
                        mediaElement = `<iframe class="w-full h-48 object-cover rounded-t-xl" src="${embedUrl}" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>`;
                    } else {
                        mediaElement = `<video class="w-full h-48 object-cover rounded-t-xl bg-black" controls src="${embedUrl}"></video>`;
                    }

                    const card = document.createElement('div');
                    card.className = 'bg-white rounded-xl shadow-md hover:shadow-xl transition duration-300 flex flex-col';
                    card.innerHTML = `
                        ${mediaElement}
                        <div class="p-4 flex-grow flex flex-col justify-between">
                            <div>
                                <span class="inline-block px-3 py-1 bg-indigo-100 text-indigo-800 text-xs font-semibold rounded-full mb-2">
                                    ${v.category}
                                </span>
                                <h3 class="text-lg font-bold text-gray-800 mb-2 line-clamp-2" title="${v.title}">${v.title}</h3>
                            </div>
                            <button data-id="${v.id}" class="deleteBtn mt-4 flex items-center justify-center gap-1 text-red-500 hover:text-red-700 bg-red-50 hover:bg-red-100 px-3 py-2 rounded-lg text-sm font-medium transition">
                                <i class="fas fa-trash-alt"></i> ลบวิดีโอ
                            </button>
                        </div>
                    `;
                    grid.appendChild(card);
                });

                document.querySelectorAll('.deleteBtn').forEach(btn => {
                    btn.addEventListener('click', () => deleteVideo(btn.dataset.id));
                });
            }

            form.addEventListener('submit', (e) => {
                e.preventDefault();

                const title = document.getElementById('title').value;
                const category = document.getElementById('category').value;
                const url = document.getElementById('url').value;

                const newVideo = {
                    id: Date.now().toString(),
                    title: title,
                    category: category,
                    url: url
                };

                videos.unshift(newVideo);
                localStorage.setItem('videoLibraryData', JSON.stringify(videos));

                form.reset();
                renderVideos();
            });

            function deleteVideo(id) {
                if (confirm('คุณแน่ใจหรือไม่ว่าต้องการลบวิดีโอนี้?')) {
                    videos = videos.filter(v => v.id !== id);
                    localStorage.setItem('videoLibraryData', JSON.stringify(videos));
                    renderVideos();
                }
            }

            searchInput.addEventListener('input', renderVideos);
            filterCategory.addEventListener('change', renderVideos);

            renderVideos();
        });
    </script>
</body>
</html>