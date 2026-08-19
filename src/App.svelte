<script>
  let videos = []; // รายการวิดีโอทั้งหมดที่บันทึกไว้
  let currentVideo = null; // วิดีโอที่กำลังเล่นอยู่
  let searchQuery = ""; // ข้อความค้นหา

  // ฟังก์ชันเพิ่มวิดีโอจากเครื่องมือถือเข้าคลัง
  function handleVideoUpload(event) {
    const files = event.target.files;
    if (files.length > 0) {
      const file = files[0];
      const newVideo = {
        id: Date.now(),
        title: file.name.replace(/\.[^/.]+$/, ""), // ดึงชื่อไฟล์มาเป็นชื่อคลิป
        url: URL.createObjectURL(file),
        date: new Date().toLocaleDateString("th-TH"),
        size: (file.size / (1024 * 1024)).toFixed(1) + " MB"
      };
      
      videos = [newVideo, ...videos]; // เพิ่มไว้หน้าสุด
      if (!currentVideo) currentVideo = newVideo; // ถ้ายังไม่ได้เล่นคลิปไหน ให้เล่นคลิปนี้ทันที
    }
  }

  // เลือกเล่นวิดีโอ
  function selectVideo(video) {
    currentVideo = video;
  }

  // กรองวิดีโอตามคำค้นหา
  $: filteredVideos = videos.filter(v => 
    v.title.toLowerCase().includes(searchQuery.toLowerCase())
  );
</script>

<!-- 1. Header สไตล์ YouTube -->
<header class="navbar">
  <div class="logo">
    <span class="yt-red">▶</span> AZAN
  </div>
  <div class="search-box">
    <input 
      type="text" 
      placeholder="ค้นหาวิดีโอในเครื่อง..." 
      bind:value={searchQuery}
    />
  </div>
  <label for="upload" class="upload-btn">
    ➕ อัปโหลด
  </label>
  <input 
    id="upload" 
    type="file" 
    accept="video/*" 
    on:change={handleVideoUpload} 
  />
</header>

<main class="container">
  <!-- 2. ส่วนเล่นวิดีโอหลัก (Player) -->
  {#if currentVideo}
    <section class="player-section">
      <div class="video-wrapper">
        <video src={currentVideo.url} controls autoplay width="100%">
          <track kind="captions" />
        </video>
      </div>
      <h1 class="video-title">{currentVideo.title}</h1>
      <div class="video-meta">
        <span>วันที่เพิ่ม: {currentVideo.date}</span> • <span>ขนาดไฟล์: {currentVideo.size}</span>
      </div>
    </section>
  {/if}

  <!-- 3. ส่วนรายการวิดีโอ (Video Grid / Feed) -->
  <section class="feed-section">
    <h3>วิดีโอในเครื่องทั้งหมด ({filteredVideos.length})</h3>
    
    {#if filteredVideos.length === 0}
      <div class="empty-state">
        <p>ยังไม่มีวิดีโอ กดปุ่ม **"➕ อัปโหลด"** ด้านบนเพื่อเพิ่มวิดีโอจากเครื่องมือถือ</p>
      </div>
    {:else}
      <div class="video-grid">
        {#each filteredVideos as video}
          <!-- Card วิดีโอแต่ละตัว -->
          <div 
            class="video-card {currentVideo?.id === video.id ? 'active' : ''}"
            on:click={() => selectVideo(video)}
            on:keydown={(e) => e.key === 'Enter' && selectVideo(video)}
            role="button"
            tabindex="0"
          >
            <div class="thumbnail">
              <video src={video.url} preload="metadata"></video>
              <span class="play-icon">▶</span>
            </div>
            <div class="card-info">
              <h4 class="card-title">{video.title}</h4>
              <p class="card-meta">{video.size} • {video.date}</p>
            </div>
          </div>
        {/each}
      </div>
    {/if}
  </section>
</main>

<style>
  :global(body) {
    margin: 0;
    background-color: #0f0f0f; /* ธีมมืดแบบ YouTube Dark Mode */
    color: #f1f1f1;
    font-family: Roboto, Arial, sans-serif;
  }

  /* Header */
  .navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 16px;
    background-color: #0f0f0f;
    border-bottom: 1px solid #272727;
    position: sticky;
    top: 0;
    z-index: 100;
  }

  .logo {
    font-weight: bold;
    font-size: 1.2rem;
    letter-spacing: -0.5px;
  }

  .yt-red {
    color: #007fff;
  }

  .search-box input {
    background-color: #121212;
    border: 1px solid #303030;
    color: white;
    padding: 8px 16px;
    border-radius: 20px;
    width: 140px;
  }

  .upload-btn {
    background-color: #272727;
    padding: 6px 14px;
    border-radius: 18px;
    font-size: 0.85rem;
    cursor: pointer;
    font-weight: 500;
  }

  input[type="file"] {
    display: none;
  }

  /* Layout */
  .container {
    max-width: 1000px;
    margin: 0 auto;
    padding: 16px;
  }

  /* Player */
  .video-wrapper {
    background: #000;
    border-radius: 12px;
    overflow: hidden;
  }

  .video-title {
    font-size: 1.2rem;
    margin: 12px 0 4px 0;
  }

  .video-meta {
    color: #aaa;
    font-size: 0.85rem;
    margin-bottom: 24px;
  }

  /* Video Grid */
  .feed-section h3 {
    font-size: 1rem;
    margin-bottom: 16px;
  }

  .video-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
    gap: 16px;
  }

  .video-card {
    background-color: #181818;
    border-radius: 10px;
    overflow: hidden;
    cursor: pointer;
    border: 1px solid transparent;
  }

  .video-card.active {
    border-color: #ff0000;
  }

  .thumbnail {
    position: relative;
    width: 100%;
    height: 120px;
    background: #272727;
  }

  .thumbnail video {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .play-icon {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: rgba(0, 0, 0, 0.6);
    padding: 6px 12px;
    border-radius: 50%;
    font-size: 0.8rem;
  }

  .card-info {
    padding: 10px;
  }

  .card-title {
    margin: 0 0 6px 0;
    font-size: 0.9rem;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .card-meta {
    margin: 0;
    font-size: 0.75rem;
    color: #aaa;
  }

  .empty-state {
    text-align: center;
    padding: 40px;
    color: #aaa;
    background: #181818;
    border-radius: 12px;
  }
</style>
