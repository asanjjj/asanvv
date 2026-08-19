<script>
  let videos = [];
  let selectedVideo = null;

  // ฟังก์ชันรับไฟล์วิดีโอ
  function handleFileUpload(event) {
    const files = Array.from(event.target.files);
    const newVideos = files.map(file => ({
      name: file.name,
      url: URL.createObjectURL(file)
    }));
    videos = [...videos, ...newVideos];
  }

  // ฟังก์ชันกดเลือกวิดีโอเพื่อเล่น
  function openVideo(video) {
    selectedVideo = video;
  }

  // ฟังก์ชันปิดตัวเล่นวิดีโอ
  function closeVideo() {
    selectedVideo = null;
  }
</script>

<main class="container">
  <!-- หัวข้อหลัก -->
  <header class="header">
    <div class="menu-icon">≡</div>
    <h1 class="title">AZAN</h1>
    <label class="upload-btn">
      + อัปโหลด
      <input type="file" accept="video/*" multiple on:change={handleFileUpload} hidden />
    </label>
  </header>

  <!-- หน้าต่างเล่นวิดีโอ (จะแสดงเมื่อกดคลิกเลือกวิดีโอในตาราง) -->
  {#if selectedVideo}
    <div class="modal-backdrop" on:click={closeVideo}>
      <div class="modal-content" on:click|stopPropagation>
        <div class="modal-header">
          <h3>{selectedVideo.name}</h3>
          <button class="close-btn" on:click={closeVideo}>✕</button>
        </div>
        <video src={selectedVideo.url} controls class="video-player">
          <track kind="captions" />
        </video>
      </div>
    </div>
  {/if}

  <!-- ตาราง 2 คอลัมน์ตามรูปวาด -->
  <div class="grid-container">
    {#if videos.length === 0}
      <div class="empty-box">ยังไม่มีวิดีโอ กดปุ่ม "+ อัปโหลด" ด้านบน</div>
    {:else}
      {#each videos as video, index}
        <button class="video-card" on:click={() => openVideo(video)}>
          <div class="thumbnail">
            <span class="play-btn">▶</span>
          </div>
          <p class="video-label">วิดีโอ {index + 1}: {video.name}</p>
        </button>
      {/each}
    {/if}
  </div>
</main>

<style>
  .container {
    max-width: 600px;
    margin: 0 auto;
    padding: 12px;
    background: #121212;
    min-height: 100vh;
    color: #fff;
    font-family: sans-serif;
  }

  /* ส่วน Header ด้านบน */
  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-bottom: 2px solid #333;
    padding-bottom: 12px;
    margin-bottom: 16px;
  }
  .menu-icon {
    font-size: 28px;
    font-weight: bold;
  }
  .title {
    font-size: 22px;
    margin: 0;
  }
  .upload-btn {
    background: #e50914;
    padding: 6px 14px;
    border-radius: 6px;
    font-size: 14px;
    font-weight: bold;
    cursor: pointer;
  }

  /* ตาราง 2 คอลัมน์ (Grid 2 Columns) */
  .grid-container {
    display: grid;
    grid-template-columns: 1fr 1fr; /* แบ่ง 2 ฝั่งเท่ากัน */
    gap: 12px;
  }

  .video-card {
    background: #1e1e1e;
    border: 1px solid #333;
    border-radius: 8px;
    padding: 8px;
    text-align: left;
    cursor: pointer;
    color: white;
  }
  .video-card:active {
    background: #333;
  }

  .thumbnail {
    width: 100%;
    height: 100px;
    background: #000;
    border-radius: 6px;
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 8px;
  }
  .play-btn {
    font-size: 24px;
    color: #fff;
    opacity: 0.8;
  }

  .video-label {
    font-size: 13px;
    margin: 0;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .empty-box {
    grid-column: span 2;
    text-align: center;
    color: #777;
    padding: 40px 0;
  }

  /* Pop-up เวลาสแกน/กดคลิกดูวิดีโอ */
  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: rgba(0, 0, 0, 0.85);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 999;
    padding: 16px;
  }
  .modal-content {
    background: #222;
    width: 100%;
    max-width: 500px;
    border-radius: 10px;
    overflow: hidden;
  }
  .modal-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 16px;
    background: #111;
  }
  .modal-header h3 {
    margin: 0;
    font-size: 14px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  .close-btn {
    background: none;
    border: none;
    color: #fff;
    font-size: 18px;
    cursor: pointer;
  }
  .video-player {
    width: 100%;
    display: block;
  }
</style>
