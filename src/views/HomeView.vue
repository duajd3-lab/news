<template>
  <div class="home">

    <div class="homeScroll">

      <h1>Today's Insight</h1>

      <!-- 검색 -->
      <form @submit.prevent="newList">

        <div class="search">

          <input type="text" v-model="keyword" placeholder="검색어를 입력해주세요." />

          <img src="/imgs/b-search.svg" @click="newList" />

        </div>

      </form>

      <!-- 키워드 -->
      <div class="keywordBtnAll">

        <button @click="keyWordChange('정치')" :class="{ active: keyword === '정치' }" class="keywordBtn">
          정치
        </button>

        <button @click="keyWordChange('사회')" :class="{ active: keyword === '사회' }" class="keywordBtn">
          사회
        </button>

        <button @click="keyWordChange('경제')" :class="{ active: keyword === '경제' }" class="keywordBtn">
          경제
        </button>

        <button @click="keyWordChange('연예')" :class="{ active: keyword === '연예' }" class="keywordBtn">
          연예
        </button>

        <button @click="keyWordChange('스포츠')" :class="{ active: keyword === '스포츠' }" class="keywordBtn">
          스포츠
        </button>

      </div>

      <!-- 카드 뉴스 -->
      <h2 class="sectionTitle">
        실시간 뉴스 카드
      </h2>

      <div class="cardNews" ref="cardSlider">
        <a v-for="(item, idx) in cardNews" :key="idx" :href="item.link" target="_blank" class="cardItem">
          <img :src="item.image || '/imgs/noimage.jpg'" />
          <div class="overlay">
            <p class="cardTitle" v-html="item.title"></p>
          </div>
        </a>
      </div>
      <!-- 메인 뉴스 -->
      <h2 class="sectionTitle">
        주요 뉴스
      </h2>

      <div class="mainNews" v-if="mainNews">
        <img :src="mainNews.image" />

        <div class="mainText">
          <h3 v-html="mainNews.title"></h3>
          <p v-html="mainNews.description"></p>
        </div>
      </div>

    </div>

    <!-- 하단 탭바 -->
    <div class="tabBar">

      <button class="tab active" @click="$router.push('/home')">
        <img src="/imgs/home.svg" />
        <span>홈</span>
      </button>

      <button class="tab" @click="$router.push('/trend')" :class="{ active: $route.path === '/trend' }">
        <img src="/imgs/trend.svg" />
        <span>트렌드</span>
      </button>

      <button class="tab">
        <img src="/imgs/scarp.svg" />
        <span>스크랩</span>
      </button>

      <button class="tab">
        <img src="/imgs/profile.svg" />
        <span>프로필</span>
      </button>

    </div>

  </div>
  <HelloWorld />
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";

export default {
  data() {
    return {
      data: [],
      keyword: '최신뉴스',
      interval: null
    }
  },

  methods: {
    keyWordChange(keyword) {
      this.keyword = this.keyword === keyword ? '' : keyword;
      this.newList();
    },

    newList() {
      fetch(`https://react-todolist-wine.vercel.app/news?keyword=${this.keyword}`)
        .then(res => res.json())
        .then(res => {

          // 중복 제거
          const seen = new Set();

          this.data = res.filter(item => {
            const key = item.title + item.link;

            if (seen.has(key)) return false;

            seen.add(key);
            return true;
          });

          // 랜덤 메인 뉴스
          const idx = Math.floor(Math.random() * this.data.length);

          this.mainNews = this.data[idx];
        });
    },

    startAutoSlide() {
      const slider = this.$refs.cardSlider;

      if (!slider) return;

      this.interval = setInterval(() => {
        const maxScroll = slider.scrollWidth - slider.clientWidth;

        if (slider.scrollLeft >= maxScroll) {
          slider.scrollTo({ left: 0, behavior: 'smooth' });
        } else {
          slider.scrollBy({ left: 180, behavior: 'smooth' });
        }
      }, 2500);
    },

    stopAutoSlide() {
      clearInterval(this.interval);
      this.interval = null;
    }
  },

  mounted() {
    this.newList();

    // DOM 렌더 후 실행
    this.$nextTick(() => {
      this.startAutoSlide();
    });
  },
  computed: {
    uniqueData() {
      const seen = new Set();

      return this.data.filter(item => {
        // title + link 같이 비교 (중복 제거 강화)
        const key = item.title + item.link;

        if (seen.has(key)) return false;

        seen.add(key);
        return true;
      });
    },

    mainNews() {
      if (this.uniqueData.length === 0) return null;

      const idx = Math.floor(Math.random() * this.uniqueData.length);
      return this.uniqueData[idx];
    },

    cardNews() {
      return this.data
        .filter(item => item.link !== this.mainNews?.link)
        .slice(0, 4);
    }
  },


  beforeUnmount() {
    this.stopAutoSlide();
  },

  name: "HomeView",
  components: {
    HelloWorld,
  },
};
</script>



<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
body {
  display: flex;
  justify-content: center;
  align-items: center;

  min-height: 100vh;

  font-family: -apple-system, BlinkMacSystemFont, sans-serif;

  background:
    linear-gradient(rgba(0, 0, 0, 0.35),
      rgba(0, 0, 0, 0.35)),

    url("../../public/imgs/news.jpg");

  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;

  overflow: hidden;
}

body::after {
  content: "";

  position: fixed;

  width: 500px;
  height: 500px;

  border-radius: 50%;

  background: rgba(255, 255, 255, 0.08);

  filter: blur(120px);

  z-index: -1;
}

h3,
p,
.cardTitle {
  word-break: keep-all;
  overflow-wrap: break-word;
}

.homeScroll {
  width: 100%;
  height: 100%;

  overflow-y: auto;
  overflow-x: hidden;

  padding-right: 2px;

  -webkit-overflow-scrolling: touch;

  &::-webkit-scrollbar {
    width: 4px;
  }

  &::-webkit-scrollbar-thumb {
    background: rgba(0, 0, 0, 0.2);
    border-radius: 999px;
  }

  &::-webkit-scrollbar-thumb:hover {
    background: rgba(0, 0, 0, 0.35);
  }

  &::-webkit-scrollbar {
    display: none;
  }
}

.home {
  margin: 0 auto;
  position: relative;

  width: 390px;
  height: 844px;
  overflow: hidden;

  -webkit-overflow-scrolling: touch;
  /* 모바일 부드러운 스크롤 */

  background:
    linear-gradient(180deg,
      #F8F6F1 0%,
      #ECE7DA 100%);

  border: 14px solid #111;
  border-radius: 55px;

  padding: 60px 24px 40px;

  box-sizing: border-box;

  box-shadow:
    0 30px 60px rgba(0, 0, 0, 0.25),
    inset 0 0 0 1px rgba(255, 255, 255, 0.1);
}

/* Dynamic Island */
.home::before {
  content: "";

  position: absolute;

  top: 14px;
  left: 50%;

  transform: translateX(-50%);

  width: 126px;
  height: 34px;

  background: #000;

  border-radius: 30px;

  z-index: 10;
}

/* 홈 인디케이터 */
.home::after {
  content: "";

  position: absolute;

  bottom: 10px;
  left: 50%;

  transform: translateX(-50%);

  width: 140px;
  height: 5px;

  background: #000;

  border-radius: 999px;

  opacity: 0.8;
}

h1,

h3 {
  font-family: 'Playfair Display', serif;
  color: #f77824;
}

// 키워드 필터
.keywordBtnAll {
  display: flex;
  gap: 8px;
  margin-bottom: 25px;
}

.keywordBtn {
  padding: 6px 14px;
  border-radius: 20px;
  border: 1px solid #dabf66;
  //background: #fff;
  font-size: 12px;
  cursor: pointer;
  transition: 0.3s;
}

/* active 상태 */
.keywordBtn.active {
  background: #d87e54;
  color: #fff;
  border: 1px solid #CEB564;
}

.search {
  position: relative;
  margin-bottom: 15px;

  input {
    width: 100%;
    height: 42px;

    border-radius: 20px;
    border: 1px solid #ccc;

    padding: 0 45px 0 15px;
    box-sizing: border-box;
  }

  img {
    position: absolute;
    right: 15px;
    top: 50%;

    transform: translateY(-50%);

    width: 20px;
    cursor: pointer;
  }
}

/* 키워드 */
.keywordBtnAll {
  display: flex;
  gap: 8px;
  margin-bottom: 25px;
}

.keywordBtn {
  border: 1px solid #d3c081;
  background: #fff;

  border-radius: 20px;

  padding: 5px 12px;

  font-size: 12px;

  cursor: pointer;
}

/* 섹션 타이틀 */
.sectionTitle {
  font-size: 20px;
  margin-bottom: 15px;
  text-align: left;
}

/* 카드뉴스 */
.cardNews {
  display: flex;
  gap: 14px;

  overflow-x: auto;
  padding: 4px 2px 12px;

  scroll-snap-type: x mandatory;

  &::-webkit-scrollbar {
    display: none;
  }
}

.cardItem {
  flex: 0 0 auto;

  width: 160px;
  height: 230px;

  border-radius: 10px;
  overflow: hidden;

  position: relative;

  background: #fff;

  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.12);

  scroll-snap-align: start;

  transition: transform 0.25s ease, box-shadow 0.25s ease;

  &:hover {
    transform: translateY(-4px);
    box-shadow: 0 12px 30px rgba(0, 0, 0, 0.18);
  }

  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }
}

.overlay {
  position: absolute;
  inset: 0;

  background: linear-gradient(to top,
      rgba(0, 0, 0, 0.75),
      transparent 60%);

  display: flex;
  align-items: flex-end;

  padding: 12px;
}

.cardTitle {
  color: #fff;
  font-size: 13px;
  font-weight: 600;
  line-height: 1.3;

  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* 메인 뉴스 */
.mainNews {

  img {
    width: 100%;
    height: 170px;

    object-fit: cover;

    border-radius: 20px;
  }

  .mainText {
    margin-top: 15px;
  }

  h3 {
    font-size: 28px;
    line-height: 1.2;

    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;

    overflow: hidden;
  }


  p {
    color: #666;
    font-size: 14px;
    line-height: 1.5;

    display: -webkit-box;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
    max-height: 100px;
    overflow: hidden;
  }
}

/* 하단 탭바 */
.tabBar {
  position: absolute;

  left: 50%;
  bottom: 22px;

  transform: translateX(-50%);

  width: calc(100% - 32px);
  height: 72px;

  background: rgba(255, 255, 255, 0.78);

  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);

  border-radius: 28px;

  display: flex;
  justify-content: space-around;
  align-items: center;

  padding: 0 10px;

  box-sizing: border-box;

  box-shadow:
    0 8px 30px rgba(0, 0, 0, 0.08),
    inset 0 1px 0 rgba(255, 255, 255, 0.9);

  border: 1px solid rgba(255, 255, 255, 0.5);

  z-index: 30;
}

/* Glass iOS TabBar */
.tabBar {
  position: absolute;

  left: 50%;
  bottom: 22px;

  transform: translateX(-50%);

  width: calc(100% - 34px);
  height: 74px;

  display: flex;
  justify-content: space-around;
  align-items: center;

  padding: 0 12px;

  box-sizing: border-box;

  border-radius: 30px;

  /* 핵심 */
  background: rgba(255, 255, 255, 0.14);

  backdrop-filter: blur(30px) saturate(180%);
  -webkit-backdrop-filter: blur(30px) saturate(180%);

  border: 1px solid rgba(255, 255, 255, 0.22);

  box-shadow:
    0 10px 40px rgba(0, 0, 0, 0.12),
    inset 0 1px 1px rgba(255, 255, 255, 0.35),
    inset 0 -1px 1px rgba(255, 255, 255, 0.08);

  z-index: 50;

  overflow: hidden;
}

/* 유리 반사광 */
.tabBar::before {
  content: "";

  position: absolute;

  top: 0;
  left: 0;

  width: 100%;
  height: 50%;

  background: linear-gradient(to bottom,
      rgba(255, 255, 255, 0.28),
      transparent);

  pointer-events: none;
}

/* 탭 버튼 */
.tab {
  position: relative;

  border: 0;
  background: transparent;

  width: 56px;
  height: 56px;

  border-radius: 18px;

  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  gap: 4px;

  cursor: pointer;

  transition: .28s cubic-bezier(.4, 0, .2, 1);

  color: black;

  img {
    width: 22px;
    height: 22px;

    opacity: .75;

    transition: .28s;
  }

  span {
    font-size: 10px;
    font-weight: 500;

    letter-spacing: -0.2px;
  }

  &:hover {
    transform: translateY(-2px);
  }
}

/* active */
.tab.active {
  background: rgba(255, 255, 255, 0.12);

  box-shadow:
    inset 0 1px 1px rgba(255, 255, 255, 0.18),
    0 4px 10px rgba(0, 0, 0, 0.08);

  color: #c28910;

  img {
    filter: brightness(1.2);
  }
}

/* 중앙 플로팅 버튼 */
.centerBtn {
  width: 62px;
  height: 62px;

  border-radius: 50%;

  background:
    linear-gradient(135deg,
      rgba(255, 255, 255, 0.9),
      rgba(255, 255, 255, 0.15));

  backdrop-filter: blur(20px);

  border: 1px solid rgba(255, 255, 255, 0.3);

  margin-top: -34px;

  box-shadow:
    0 10px 30px rgba(0, 0, 0, 0.16),
    inset 0 1px 2px rgba(255, 255, 255, 0.5);

  img {
    width: 24px;
    height: 24px;

    opacity: 1;
  }

  &:hover {
    transform: translateY(-4px) scale(1.04);
  }
}

.home::selection {
  background: transparent;
}

.home::after {
  content: "";

  position: absolute;

  width: 240px;
  height: 240px;

  background: rgba(255, 255, 255, 0.22);

  filter: blur(80px);

  bottom: -80px;
  left: 50%;

  transform: translateX(-50%);

  z-index: 0;
}
</style>
