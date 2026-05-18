<template>
    <div class="home">

        <div class="homeScroll">
            <div class="trend">
                <h1>Trending</h1>
                <p>지금 가장 많이 본 뉴스</p>
            </div>

            <div class="trendTags">
                <span @click="keyWordChange('정치')" :class="{ active: keyword === '정치' }">정치</span>
                <span @click="keyWordChange('사회')" :class="{ active: keyword === '사회' }">사회</span>
                <span @click="keyWordChange('경제')" :class="{ active: keyword === '경제' }">경제</span>
                <span @click="keyWordChange('연예')" :class="{ active: keyword === '연예' }">연예</span>
                <span @click="keyWordChange('스포츠')" :class="{ active: keyword === '스포츠' }">스포츠</span>
            </div>

            <!-- TOP 뉴스 카드 (핵심 영역) -->
            <div class="topTrend">
                <div class="bigCard" v-if="topNews">

                    <img :src="topNews.image || '/imgs/noimage.jpg'" />

                    <div class="overlay">

                        <span class="hotBadge">
                            HOT
                        </span>

                        <h2 v-html="topNews.title"></h2>

                        <div class="meta">
                            <span>👁 {{ topNews.views.toLocaleString() }}</span>
                            <span class="up">▲ {{ topNews.rate }}%</span>
                        </div>

                    </div>

                </div>

                <div class="smallList">

                    <div class="miniCard" v-for="(item, i) in topThree" :key="i">

                        <!-- 이미지 -->
                        <img :src="item.image || '/imgs/noimage.jpg'" class="miniThumb" />

                        <!-- 텍스트 -->
                        <div class="miniContent">

                            <h4 v-html="item.title"></h4>

                            <p>
                                {{ item.description }}
                            </p>

                            <div class="miniMeta">

                                <span>
                                    👁 {{ item.views.toLocaleString() }}
                                </span>

                                <span class="up">
                                    ▲ {{ item.rate }}%
                                </span>

                            </div>

                        </div>

                    </div>

                </div>
            </div>

            <!-- 리스트형 트렌드 뉴스 -->
            <div class="trendList">

                <div class="trendItem" v-for="(item, i) in trendList" :key="i">

                    <span class="rank">
                        {{ i + 4 }}
                    </span>

                    <div class="content">

                        <h4 v-html="item.title"></h4>

                        <div class="meta">

                            <span>
                                👁 {{ item.views.toLocaleString() }}
                            </span>

                            <span class="up">
                                ▲ {{ item.rate }}%
                            </span>

                        </div>

                    </div>

                </div>

            </div>




        </div>
            <!-- 하단 탭바 -->
            <div class="tabBar">

                <button class="tab active">
                    <img src="/imgs/home.svg" />
                    <span>홈</span>
                </button>

                <button class="tab" @click="$router.push('/trend')">
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
</template>

<script>
export default {
    name: "TrendView",

    data() {
        return {
            data: [],

        }
    },

    computed: {

        topNews() {
            return this.data[0] || null;
        },

        topThree() {
            return this.data.slice(1, 3);
        },

        trendList() {
            return this.data.slice(3, 10);
        }

    },

    mounted() {

        this.fetchTrend();

        this.interval = setInterval(() => {

            this.data = this.data.map(item => ({
                ...item,

                views: item.views + Math.floor(Math.random() * 3000),

                rate: Math.floor(Math.random() * 90) + 10
            }));

            this.data.sort((a, b) => b.views - a.views);

        }, 5000);
    },

    methods: {
        fetchTrend() {
            fetch(`https://react-todolist-wine.vercel.app/news?keyword=${this.keyword}`)
                .then(res => res.json())
                .then(res => {
                    //  조회수 + 상승률 생성
                    const trendData = res.map(item => ({
                        ...item,

                        // 조회수
                        views: Math.floor(Math.random() * 90000) + 10000,

                        // 상승률
                        rate: Math.floor(Math.random() * 90) + 10
                    }));

                    //  조회수 높은 순 정렬
                    trendData.sort((a, b) => b.views - a.views);

                    this.data = trendData;
                });
        },
        keyWordChange(keyword) {
            this.keyword = this.keyword === keyword ? '' : keyword;
            this.fetchTrend();
        }
    }


}
</script>

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

// 트렌드 태그
.trendTags {
    display: flex;
    overflow-x: auto;
    gap: 8px;
}

.trendTags span {
    padding: 6px 12px;
    border-radius: 20px;
    border: 1px solid #d3c081;
    font-size: 12px;
    white-space: nowrap;
}

/* active 상태 */
.trendTags span.active {
    background: #d37449;
    color: #fff;
    border: 1px solid #CEB564;
}

// 카드 스타일
.bigCard {
    position: relative;
    height: 240px;
    border-radius: 24px;
    overflow: hidden;
    margin-top: 20px;

    img {
        width: 100%;
        height: 100%;
        object-fit: cover;
    }
}

.overlay {
    position: absolute;
    inset: 0;

    background: linear-gradient(transparent,
            rgba(0, 0, 0, 0.8));

    display: flex;
    flex-direction: column;
    justify-content: flex-end;

    padding: 20px;
}

.bigCard h2 {
    color: white;

    font-size: 24px;
    line-height: 1.3;

    margin-top: 12px;

    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;

    overflow: hidden;
}

.bigCard img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}

// 랭킹 리스트
.trendItem {
    display: flex;
    gap: 14px;

    padding: 14px;

    background: rgba(255,255,255,0.7);

    border-radius: 18px;

    margin-bottom: 12px;

    backdrop-filter: blur(10px);

    border: 1px solid rgba(255,255,255,0.4);
}

.rank {
    min-width: 34px;
    height: 34px;

    border-radius: 50%;

    background: #CEB564;
    color: white;

    display: flex;
    align-items: center;
    justify-content: center;

    font-size: 15px;
    font-weight: 700;
}

.hotBadge {
    width: fit-content;

    background: #ff5b36;
    color: white;

    padding: 5px 10px;

    border-radius: 999px;

    font-size: 12px;
    font-weight: bold;

    top: 0;
    //margin-bottom: 10px;
}

.smallList {
    margin-top: 18px;

    display: flex;
    flex-direction: column;
    gap: 14px;
}

.miniCard {
    display: flex;
    gap: 14px;

    background: #fff;

    border-radius: 18px;

    padding: 10px;

    box-shadow:
        0 4px 12px rgba(0, 0, 0, 0.08);

    transition: .25s ease;

    &:hover {
        transform: translateY(-2px);
        box-shadow:
            0 8px 20px rgba(0, 0, 0, 0.12);
    }
}

/* 썸네일 */
.miniThumb {
    width: 110px;
    height: 110px;

    border-radius: 14px;

    object-fit: cover;

    flex-shrink: 0;
}

/* 텍스트 영역 */
.miniContent {
    flex: 1;

    display: flex;
    flex-direction: column;
    justify-content: center;
}

.miniContent h4 {
    font-size: 18px;
    line-height: 1.25;

    font-weight: 700;

    margin-bottom: 8px;

    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;

    overflow: hidden;
}

.miniContent p {
    font-size: 13px;
    color: #666;

    line-height: 1.4;

    margin-bottom: 10px;

    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;

    overflow: hidden;
}

.miniMeta {
    display: flex;
    gap: 10px;

    font-size: 12px;

    color: #888;
}
</style>