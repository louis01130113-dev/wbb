<script setup>
import { ref, computed } from 'vue'

/* =========================================================
   
   ========================================================= */

// 基本資料
const profile = {
  name: '彈珠',
  nameEn: 'Hsiao-Ming Wang',
  title: '陶藝家 / New Media Artist',
  intro:
    '陶藝作為創作媒介，探索植物感知與記憶。',
  avatar: 'images/avatar.jpg', // 放在 public/images/ 裡；沒有圖片會顯示名字縮寫
  email: '1111111111l@gmail.com',
  links: [
    { label: 'Instagram', url: 'https://instagram.com/' },
    { label: 'YouTube', url: 'https://youtube.com/' },
    { label: 'GitHub', url: 'https://github.com/' },
  ],
}

// 學歷（由新到舊）
const education = [
  { period: '2023 – 現在', school: '國立清華大學', dept: '藝術學院學士班', note: '主修科技藝術' },
  { period: '2020 – 2023', school: '○○高級中學', dept: '美術班', note: '' },
]

// 發表、展演、參賽紀錄（type 可填：展覽、表演、比賽、講座）
const records = [
  { year: 2026, type: '比賽', title: '○○數位藝術獎', detail: '入圍 · 互動裝置類' },
  { year: 2026, type: '展覽', title: '「GAME ON」第五屆畢業展', detail: '清華大學藝術中心' },
  { year: 2025, type: '表演', title: '○○聲音實驗演出', detail: '新竹 ○○ 展演空間' },
  { year: 2024, type: '展覽', title: '○○聯展', detail: '台北 ○○ 藝廊' },
]

// 作品（依年份自動分組）
// image：放在 public/images/ 的檔名；沒有就留空字串 ''
// video：YouTube 影片網址（一般網址即可，會自動轉換）；沒有就留空字串 ''
const works = [
  {
    year: 2026,
    title: '光之迴路',
    medium: '互動裝置',
    desc: '觀眾的移動會改變空間中的光線路徑，形成即時生成的光影結構。',
    image: 'images/work-2026-1.jpg',
    video: '',
  },
  {
    year: 2026,
    title: '資料之海',
    medium: '生成影像',
    desc: '將一年份的城市開放資料轉譯為流動的海浪影像。',
    image: '',
    video: 'https://www.youtube.com/watch?v=dQw4w9WgXcQ',
  },
  {
    year: 2025,
    title: '雜訊肖像',
    medium: '聲音裝置',
    desc: '收集環境噪音並轉化為不斷變化的聲音肖像。',
    image: '',
    video: '',
  },
  {
    year: 2024,
    title: '像素記憶',
    medium: '數位版畫',
    desc: '以低解析度像素重新詮釋家族老照片。',
    image: '',
    video: '',
  },
]

/* =========================================================
   以下是網頁運作的程式，不需要修改
   ========================================================= */

// 讓圖片路徑在本機和 GitHub Pages 上都能正常顯示
const asset = (path) => (path ? import.meta.env.BASE_URL + path : '')

// 把 YouTube 一般網址轉成可以嵌入的網址
const toEmbed = (url) => {
  if (!url) return ''
  const m = url.match(/(?:youtu\.be\/|v=|embed\/)([\w-]{11})/)
  return m ? `https://www.youtube.com/embed/${m[1]}` : url
}

const years = computed(() => [...new Set(works.map((w) => w.year))].sort((a, b) => b - a))
const selectedYear = ref('all')
const groupedWorks = computed(() => {
  const list = selectedYear.value === 'all' ? years.value : [selectedYear.value]
  return list.map((y) => ({ year: y, items: works.filter((w) => w.year === y) }))
})

const recordsByYear = computed(() => {
  const ys = [...new Set(records.map((r) => r.year))].sort((a, b) => b - a)
  return ys.map((y) => ({ year: y, items: records.filter((r) => r.year === y) }))
})

const avatarFailed = ref(false)
const failedImages = ref(new Set())
const markFailed = (key) => {
  failedImages.value = new Set([...failedImages.value, key])
}

const menuOpen = ref(false)
const navItems = [
  { id: 'about', label: '關於' },
  { id: 'education', label: '學歷' },
  { id: 'records', label: '經歷' },
  { id: 'works', label: '作品' },
  { id: 'contact', label: '聯絡' },
]
const currentYear = new Date().getFullYear()
</script>

<template>
  <div class="page">
    <!-- 導覽列 -->
    <header class="nav">
      <a href="#top" class="logo">{{ profile.nameEn }}</a>
      <button class="menu-btn" @click="menuOpen = !menuOpen" aria-label="選單">
        <span></span><span></span><span></span>
      </button>
      <nav :class="{ open: menuOpen }">
        <a v-for="n in navItems" :key="n.id" :href="'#' + n.id" @click="menuOpen = false">
          {{ n.label }}
        </a>
      </nav>
    </header>

    <!-- 首頁主視覺 -->
    <section id="top" class="hero">
      <div class="hero-text">
        <p class="eyebrow">PORTFOLIO · {{ currentYear }}</p>
        <h1>{{ profile.name }}</h1>
        <p class="hero-en">{{ profile.nameEn }}</p>
        <p class="hero-title">{{ profile.title }}</p>
        <a href="#works" class="btn">瀏覽作品 →</a>
      </div>
      <div class="hero-visual">
        <div class="ring ring-1"></div>
        <div class="ring ring-2"></div>
        <img
          v-if="profile.avatar && !avatarFailed"
          :src="asset(profile.avatar)"
          :alt="profile.name"
          class="avatar"
          @error="avatarFailed = true"
        />
        <div v-else class="avatar avatar-fallback">{{ profile.name.slice(0, 1) }}</div>
      </div>
    </section>

    <!-- 關於我 -->
    <section id="about" class="section">
      <h2 class="section-title"><span>01</span>關於我</h2>
      <p class="about-text">{{ profile.intro }}</p>
    </section>

    <!-- 學歷 -->
    <section id="education" class="section">
      <h2 class="section-title"><span>02</span>學歷</h2>
      <div class="edu-list">
        <div v-for="e in education" :key="e.school" class="card edu">
          <p class="mono">{{ e.period }}</p>
          <h3>{{ e.school }}</h3>
          <p>{{ e.dept }}</p>
          <p v-if="e.note" class="muted">{{ e.note }}</p>
        </div>
      </div>
    </section>

    <!-- 發表展演參賽紀錄 -->
    <section id="records" class="section">
      <h2 class="section-title"><span>03</span>發表・展演・參賽紀錄</h2>
      <div class="timeline">
        <div v-for="g in recordsByYear" :key="g.year" class="tl-year">
          <div class="tl-label mono">{{ g.year }}</div>
          <ul>
            <li v-for="r in g.items" :key="r.title">
              <span class="tag">{{ r.type }}</span>
              <div>
                <strong>{{ r.title }}</strong>
                <p class="muted">{{ r.detail }}</p>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </section>

    <!-- 作品 -->
    <section id="works" class="section">
      <h2 class="section-title"><span>04</span>年度作品</h2>
      <div class="filters">
        <button :class="{ active: selectedYear === 'all' }" @click="selectedYear = 'all'">全部</button>
        <button
          v-for="y in years"
          :key="y"
          :class="{ active: selectedYear === y }"
          @click="selectedYear = y"
        >
          {{ y }}
        </button>
      </div>

      <div v-for="g in groupedWorks" :key="g.year" class="work-year">
        <h3 class="year-heading mono">{{ g.year }}</h3>
        <div class="work-grid">
          <article v-for="w in g.items" :key="w.title" class="card work">
            <div class="work-media">
              <iframe
                v-if="w.video"
                :src="toEmbed(w.video)"
                :title="w.title"
                allowfullscreen
                loading="lazy"
              ></iframe>
              <img
                v-else-if="w.image && !failedImages.has(w.title)"
                :src="asset(w.image)"
                :alt="w.title"
                loading="lazy"
                @error="markFailed(w.title)"
              />
              <div v-else class="media-placeholder mono">{{ w.medium }}</div>
            </div>
            <div class="work-body">
              <p class="mono accent">{{ w.medium }}</p>
              <h4>{{ w.title }}</h4>
              <p class="muted">{{ w.desc }}</p>
            </div>
          </article>
        </div>
      </div>
    </section>

    <!-- 聯絡 -->
    <section id="contact" class="section contact">
      <h2 class="section-title"><span>05</span>聯絡我</h2>
      <a :href="'mailto:' + profile.email" class="email">{{ profile.email }}</a>
      <div class="links">
        <a v-for="l in profile.links" :key="l.label" :href="l.url" target="_blank" rel="noopener">
          {{ l.label }} ↗
        </a>
      </div>
    </section>

    <footer class="footer mono">© {{ currentYear }} {{ profile.nameEn }}</footer>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700&family=Space+Grotesk:wght@400;600&family=JetBrains+Mono:wght@400&display=swap');

:root {
  --bg: #050b18;
  --bg-2: #0a1530;
  --line: rgba(80, 150, 255, 0.18);
  --text: #e6eeff;
  --muted: #8ea3c7;
  --accent: #3d9bff;
  --accent-2: #6ee7ff;
}

* { box-sizing: border-box; margin: 0; padding: 0; }
html { scroll-behavior: smooth; }
body {
  background: var(--bg);
  color: var(--text);
  font-family: 'Space Grotesk', 'Noto Sans TC', sans-serif;
  line-height: 1.7;
}
a { color: inherit; text-decoration: none; }
.mono { font-family: 'JetBrains Mono', monospace; font-size: 0.85rem; letter-spacing: 0.05em; }
.muted { color: var(--muted); }
.accent { color: var(--accent-2); }

.page {
  background-image:
    linear-gradient(var(--line) 1px, transparent 1px),
    linear-gradient(90deg, var(--line) 1px, transparent 1px);
  background-size: 60px 60px;
  background-attachment: fixed;
}

/* 導覽列 */
.nav {
  position: sticky; top: 0; z-index: 10;
  display: flex; align-items: center; justify-content: space-between;
  padding: 1rem 6vw;
  background: rgba(5, 11, 24, 0.8);
  backdrop-filter: blur(10px);
  border-bottom: 1px solid var(--line);
}
.logo { font-weight: 600; letter-spacing: 0.08em; }
.nav nav { display: flex; gap: 2rem; }
.nav nav a { color: var(--muted); transition: color 0.2s; }
.nav nav a:hover { color: var(--accent-2); }
.menu-btn { display: none; background: none; border: 0; cursor: pointer; }
.menu-btn span { display: block; width: 22px; height: 2px; margin: 5px 0; background: var(--text); }

/* 主視覺 */
.hero {
  min-height: 90vh;
  display: grid; grid-template-columns: 1.2fr 1fr; align-items: center; gap: 3rem;
  padding: 4rem 6vw;
}
.eyebrow { color: var(--accent); font-family: 'JetBrains Mono', monospace; letter-spacing: 0.2em; margin-bottom: 1rem; }
.hero h1 { font-size: clamp(2.8rem, 7vw, 5rem); line-height: 1.1; }
.hero-en { font-size: 1.3rem; color: var(--accent-2); margin: 0.5rem 0 1.5rem; }
.hero-title { color: var(--muted); margin-bottom: 2rem; }
.btn {
  display: inline-block; padding: 0.8rem 1.8rem;
  border: 1px solid var(--accent); color: var(--accent-2);
  transition: all 0.25s;
}
.btn:hover { background: var(--accent); color: var(--bg); box-shadow: 0 0 24px var(--accent); }

.hero-visual { position: relative; display: grid; place-items: center; aspect-ratio: 1; }
.ring { position: absolute; border-radius: 50%; border: 1px solid var(--accent); }
.ring-1 { inset: 5%; opacity: 0.4; animation: spin 20s linear infinite; border-style: dashed; }
.ring-2 { inset: 15%; opacity: 0.7; box-shadow: 0 0 40px rgba(61, 155, 255, 0.4); }
@keyframes spin { to { transform: rotate(360deg); } }
.avatar { width: 60%; aspect-ratio: 1; border-radius: 50%; object-fit: cover; position: relative; }
.avatar-fallback {
  display: grid; place-items: center; font-size: 4rem; font-weight: 700;
  background: radial-gradient(circle, var(--bg-2), var(--bg));
  color: var(--accent-2);
}

/* 區塊 */
.section { padding: 6rem 6vw; max-width: 1200px; margin: 0 auto; }
.section-title { font-size: 2rem; margin-bottom: 2.5rem; display: flex; align-items: baseline; gap: 1rem; }
.section-title span { font-family: 'JetBrains Mono', monospace; font-size: 1rem; color: var(--accent); }
.about-text { font-size: 1.2rem; max-width: 720px; }

.card {
  background: rgba(10, 21, 48, 0.75);
  border: 1px solid var(--line);
  transition: border-color 0.25s, transform 0.25s;
}
.card:hover { border-color: var(--accent); transform: translateY(-3px); }

.edu-list { display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 1.5rem; }
.edu { padding: 1.8rem; }
.edu .mono { color: var(--accent); }
.edu h3 { margin: 0.4rem 0; }

/* 時間軸 */
.timeline { border-left: 1px solid var(--accent); padding-left: 2rem; }
.tl-year { position: relative; margin-bottom: 2.5rem; }
.tl-year::before {
  content: ''; position: absolute; left: calc(-2rem - 6px); top: 0.4rem;
  width: 11px; height: 11px; background: var(--accent); border-radius: 50%;
  box-shadow: 0 0 12px var(--accent);
}
.tl-label { color: var(--accent-2); font-size: 1.1rem; margin-bottom: 1rem; }
.tl-year ul { list-style: none; display: grid; gap: 1rem; }
.tl-year li { display: flex; gap: 1rem; align-items: flex-start; }
.tag {
  flex-shrink: 0; font-size: 0.75rem; padding: 0.15rem 0.6rem;
  border: 1px solid var(--accent); color: var(--accent);
}

/* 作品 */
.filters { display: flex; flex-wrap: wrap; gap: 0.6rem; margin-bottom: 2.5rem; }
.filters button {
  background: none; border: 1px solid var(--line); color: var(--muted);
  padding: 0.4rem 1.1rem; cursor: pointer; font-family: inherit;
}
.filters button.active, .filters button:hover { border-color: var(--accent); color: var(--accent-2); }
.work-year { margin-bottom: 3rem; }
.year-heading { font-size: 1.4rem; color: var(--accent-2); margin-bottom: 1.2rem; }
.work-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 1.5rem; }
.work-media { aspect-ratio: 16 / 9; background: var(--bg-2); overflow: hidden; }
.work-media img, .work-media iframe { width: 100%; height: 100%; object-fit: cover; border: 0; display: block; }
.media-placeholder {
  width: 100%; height: 100%; display: grid; place-items: center; color: var(--accent);
  background: linear-gradient(135deg, var(--bg-2), #102a5c);
}
.work-body { padding: 1.4rem; }
.work-body h4 { font-size: 1.2rem; margin: 0.3rem 0 0.5rem; }

/* 聯絡 */
.contact { text-align: center; }
.contact .section-title { justify-content: center; }
.email { font-size: clamp(1.2rem, 4vw, 2rem); color: var(--accent-2); border-bottom: 1px solid var(--accent); }
.links { display: flex; justify-content: center; flex-wrap: wrap; gap: 2rem; margin-top: 2rem; color: var(--muted); }
.links a:hover { color: var(--accent-2); }
.footer { text-align: center; padding: 2rem; color: var(--muted); border-top: 1px solid var(--line); }

/* 響應式：手機版 */
@media (max-width: 768px) {
  .menu-btn { display: block; }
  .nav nav {
    display: none; position: absolute; top: 100%; left: 0; right: 0;
    flex-direction: column; gap: 0; background: var(--bg); border-bottom: 1px solid var(--line);
  }
  .nav nav.open { display: flex; }
  .nav nav a { padding: 1rem 6vw; }
  .hero { grid-template-columns: 1fr; text-align: center; padding-top: 2rem; }
  .hero-visual { max-width: 280px; margin: 0 auto; order: -1; }
  .section { padding: 4rem 6vw; }
  .work-grid { grid-template-columns: 1fr; }
}
</style>
