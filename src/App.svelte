<script>
  let videos = [];
  let currentVideo = null;

  // ฟังก์ชันรับไฟล์วิดีโอ
  function handleFileUpload(event) {
    const files = Array.from(event.target.files);
    const newVideos = files.map(file => ({
      name: file.name,
      url: URL.createObjectURL(file)
    }));
    videos = [...videos, ...newVideos];
  }

  // ฟังก์ชันเมื่อกดเลือกวิดีโอให้เล่น
  function selectVideo(video) {
    currentVideo = video;
  }
</script>

<main class="container">
  <header>
    <h1>► AZAN</h1>
    <label class="upload-btn">
      + อัปโหลดวิดีโอ
      <input type="file" accept="video/*" multiple on:change={handleFileUpload} hidden />
    </label>
  </header>

  <!-- ส่วนเล่นวิดีโอที่เลือก (จะไม่เล่นอัตโนมัติจนกว่าจะกด Play) -->
  {#if currentVideo}
    <section class="player-section">
      <h2>กำลังเล่น: {currentVideo.name}</h2>
      <video src={currentVideo.url} controls autoplay class="main-player">
        <track kind="captions" />
      </video>
    </section>
  {/if}

  <!-- ส่วนตารางรายการวิดีโอ (Grid Layout) -->
  <section class="grid-section">
    <h3>รายการวิดีโอทั้งหมด ({videos.length})</h3>
    
    {#if videos.length === 0}
      <p class="empty-text">ยังไม่มีวิดีโอ กดปุ่ม "+ อัปโหลดวิดีโอ" เพื่อเพิ่มไฟล์</p>
    {:else}
      <div class="video-grid">
        {#each videos as video}
          <button class="video-card" on:click={() => selectVideo(video)}>
            <div class="thumbnail-placeholder">
              <span class="play-icon">▶</span>
            </div>
            <p class="video-name">{video.name}</p>
          </button>
        {/each}
      </div>
    {/if}
  </section>
</main>

<style>
  .container {
    max-width: 800px;
    margin: 0 auto;
    padding: 16px;
    color: #fff;
    font-family: sans-serif;
  }
  header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
  }
  .upload-btn {
    background: #e50914;
    padding: 8px 16px;
    border-radius: 6px;
    cursor: pointer;
    font-weight: bold;
  }
  .main-player {
    width: 100%;
    max-height: 400px;
    border-radius: 8px;
    background: #000;
  }
  /* การจัดตารางแบบ Grid เลื่อนลง */
  .video-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
    gap: 16px;
    margin-top: 12px;
  }
  .video-card {
    background: #222;
    border: 1px solid #333;
    border-radius: 8px;
    padding: 8px;
    text-align: center;
    cursor: pointer;
    color: #fff;
  }
  .video-card:hover {
    background: #333;
  }
  .thumbnail-placeholder {
    height: 90px;
    background: #111;
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 24px;
    margin-bottom: 8px;
  }
  .video-name {
    font-size: 12px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    margin: 0;
  }
  .empty-text {
    color: #888;
    text-align: center;
    margin-top: 40px;
  }
</style>
