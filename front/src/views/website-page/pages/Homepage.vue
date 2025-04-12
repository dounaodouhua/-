<template>
  <div class="Homepage">
    <!-- 顶部banner -->
    <el-carousel class="box" height="660px">
      <el-carousel-item v-for="item in bannerList" :key="item.id" :style="`background: url(${item.firstImg}) no-repeat;background-size: 100% 660px;cursor:pointer`" @click.native="toDetail(item, 1)" />
    </el-carousel>
    <div class="content">
      <!-- 滚动新闻 -->
      <TitleAndMore style="margin:50px 0 40px" :title="currentType" @viewMore="viewMore('/website/newsCenter', activityIndex)">
        <el-popover slot="title-right" v-model="popper" popper-class="index-popover" placement="right" trigger="click">
          <ul class="list">
            <li v-for="(item, index) in typeList" :key="index" class="list-item" @click="changeType(index)">{{ item.title }}</li>
          </ul>
          <span slot="reference" class="show-popover" title="点击切换"><i class="el-icon-arrow-down" /></span>
        </el-popover>
      </TitleAndMore>
      <div class="first-list">
        <div v-for="item in scrollNews.slice(0, 6)" :key="item.id" class="news-item" @click="toDetail(item, 1)">
          <img v-if="item.firstImg" :src="item.firstImg" alt="加载失败" />
          <div v-else class="default-img"><i class="el-icon-picture-outline" /><span>暂无图片 加载失败</span></div>
          <div class="news-content">
            <div class="news-title" :title="item.channelCName">{{ item.channelCName }}</div>
            <div class="news-time">{{ item.channelCTime | filterDateTime }}</div>
            <div class="news-remarks">{{ removeHtmlStyle(item.channelCText) }}</div>
          </div>
        </div>
      </div>
      <!-- 通知公告 -->
      <TitleAndMore style="margin:50px 0 40px" title="通知公告" @viewMore="viewMore('/website/notice')" />
      <div class="second-list">
        <div v-for="item in notices.slice(0, 4)" :key="item.id" class="notice-item" @click="toDetail(item, 2)">
          <div class="notice-top">
            <span class="notice-title" :title="item.channelCName">{{ item.channelCName }}</span>
            <span class="notice-time">{{ item.channelCTime | filterDateTime }}</span>
          </div>
          <div class="notice-content">{{ removeHtmlStyle(item.channelCText) }}</div>
        </div>
      </div>
    </div>
    <!-- 组织机构 -->
    <div class="organization-bg">
      <div class="organization-content">
        <TitleAndMore style="margin:39px 0 39px" title="组织机构" @viewMore="viewMore('/website/organization')" />
        <div class="organization-list">
          <div v-for="item in organizationList.slice(0, 8)" :key="item.id" class="organization-item" @click="toDetail(item, 3)">
            <div class="circle">
              <img :src="organizationLogo" alt="加载失败" class="logo" />
            </div>
            <span class="organization-name" :title="item.channelCName">{{ item.channelCName }}</span>
          </div>
        </div>
      </div>
    </div>
    <!-- 竞赛专栏 -->
    <div class="competition">
      <div class="competition-content">
        <TitleAndMore style="margin:50px 0 40px" title="竞赛专栏" @viewMore="viewMore('/website/competition')" />
        <div v-for="item in raceList.slice(0, 4)" :key="item.id" class="competition-item" @click="toDetail(item, 4)">
          <span class="competition-title" :title="item.channelCName">{{ item.channelCName }}</span>
          <span class="competition-time">{{ item.channelCTime | filterDateTime }}</span>
          <span class="competition-remarks">{{ removeHtmlStyle(item.channelCText) }}</span>
        </div>
      </div>
    </div>    
    <Footer />
  </div>
</template>
<script>
import moment from 'moment'
import webAPI from '@/api/website'
import { removeHtmlStyle } from '@/utils/common'
import organizationLogo from '@/assets/img/organizationLogo.png'
import TitleAndMore from '../components/TitleAndMore.vue'
import Footer from '../website-layout/Footer.vue'
export default {
  components: { TitleAndMore, Footer },
  filters: {
    filterDateTime(val) {
      return moment(val).format('YYYY-MM-DD HH:mm')
    }
  },
  data() {
    return {
      bannerList: [],
      resp: {},
      scrollNews: [],
      notices: [],
      organizationList: [],
      raceList: [],
      activityIndex: 0,
      organizationLogo,
      typeList: [],
      currentType: '',
      popper: false,
      members: [],
      introduce: ''
    }
  },
  watch: {
    currentType(newVal, oldVal) {
      if (this.resp[newVal]) {
        this.scrollNews = this.resp[newVal]
      }
    }
  },
  created() {
    this.typeList = this.$store.state.webSite.routeList.filter(e => e.name === 'NewsCenter')[0].meta.children
    this.currentType = this.typeList[0].title
    this.getData()
  },
  methods: {
    removeHtmlStyle(html) {
      return removeHtmlStyle(html)
    },
    async getData() {
      const res = await webAPI.homePage()
      this.resp = res
      this.scrollNews = res.滚动新闻
      this.notices = res.通知公告
      this.organizationList = res.组织机构
      this.raceList = res.竞赛专栏
      this.members = res.单位会员
      this.bannerList = res.sliders
    },
    toDetail(item, type) {
      this.$router.push({
        path: '/website/commonDetail',
        query: {
          row: JSON.stringify(item),
          type
        }
      })
    },
    viewMore(path, activityIndex) {      
      this.$router.push(activityIndex > -1 ? { path: path, query: { index: this.activityIndex } } : path)
    },
    changeType(index) {
      this.activityIndex = index
      this.currentType = this.typeList[index].title
      this.popper = false
    }
  }
}
</script>

<style lang="scss" scoped>
@media screen and (max-width: 765px) {
   .box {
    display: none;
  }
}

::v-deep.Homepage {
  font-family: PingFang SC;
  .el-carousel__item{
    background-size: cover !important;
  }
  /* .banner {
    width: 100%;
    height: 660px;
    background: url("~@/assets/img/banner.png") no-repeat;
    background-size: 100% 660px;
    overflow: hidden;
    cursor: pointer;
    .title {
      font-size: 60px;
      letter-spacing: 2px;
      font-family: FZLanTingHei-EB-GBK;
      color: #424242;
      font-weight: 700;
      margin: 207px 0 0 calc((100vw - 1380px) / 2);
      position: relative;
      &::before {
        content: "";
        position: absolute;
        top: -32px;
        left: 2px;
        width: 60px;
        height: 8px;
        border-radius: 3px;
        background-color: #3776ff;
      }
    }
    .sub-title {
      font-size: 28px;
      color: #354a78;
      margin: 38px 0 0 calc((100vw - 1380px) / 2);
    }
    .btn {
      background-color: #3776ff;
      border-radius: 4px;
      width: 140px;
      height: 48px;
      line-height: 48px;
      text-align: center;
      margin: 70px 0 0 calc((100vw - 1380px) / 2);
      font-size: 24px;
      color: #fff;
    }
  } */
  .content {
    width: 1400px;
    margin-left: 50%;
    transform: translateX(-50%);
    .show-popover {
      background-color: #3776ff;
      color: #fff;
      width: 20px;
      height: 20px;
      border-radius: 50%;
      line-height: 20px;
      text-align: center;
      position: absolute;
      top: 10px;
      left: 170px;
      cursor: pointer;
    }
    .first-list {
      display: flex;
      justify-content: space-between;
      flex-wrap: wrap;
      .news-item:nth-child(n + 3) {
        margin-top: 30px;
      }
      .news-item {
        width: 686px;
        height: 160px;
        box-sizing: border-box;
        border: 1px solid #ededed;
        border-radius: 4px;
        background-color: #fafdff;
        display: flex;
        cursor: pointer;
        transition: 0.3s;
        &:hover {
          font-variant: JIS83;
          box-shadow: 0 1px 16px rgba(0,0,0,.1);
        }
        img {
          width: 260px;
          height: 100%;
          border-radius: 4px;
          object-fit: cover;
        }
        .news-content {
          width: 375px;
          height: 112px;
          margin-top: 24px;
          margin-left: 30px;
          .news-title {
            font-size: 16px;
            font-weight: 700;
            color: #333;
            width: 340px;
            word-break: break-all;
            text-overflow: ellipsis;
            overflow: hidden;
            display: -webkit-box;
            -webkit-line-clamp: 1;
            -webkit-box-orient: vertical;
          }
          .news-time {
            font-size: 12px;
            color: #808080;
            margin-top: 11px;
          }
          .news-remarks {
            margin-top: 10px;
            font-size: 14px;
            color: #666666;
            line-height: 21px;
            word-break: break-all;
            text-overflow: ellipsis;
            overflow: hidden;
            display: -webkit-box;
            -webkit-line-clamp: 3;
            -webkit-box-orient: vertical;
          }
        }
      }
    }
    .second-list {
      display: flex;
      .notice-item {
        width: 320px;
        height: 220px;
        border: 1px solid #ededed;
        box-sizing: border-box;
        border-radius: 4px;
        cursor: pointer;
        transition: 0.3s;
        &:hover {
          font-variant: JIS83;
          box-shadow: 0 1px 16px rgba(0,0,0,.1);
        }
        .notice-top {
          width: 100%;
          height: 80px;
          border-bottom: 1px solid #ededed;
          box-sizing: border-box;
          border-radius: 4px;
          background-color: #fafdff;
          overflow: hidden;
          .notice-title {
            width: 284px;
            margin: 19px 0 13px 21px;
            color: #333333;
            font-size: 16px;
            font-weight: 700;
            word-break: break-all;
            text-overflow: ellipsis;
            overflow: hidden;
            display: -webkit-box;
            -webkit-line-clamp: 1;
            -webkit-box-orient: vertical;
          }
          .notice-time {
            margin-left: 21px;
            color: #666666;
            font-size: 14px;
          }
        }
        .notice-content {
          width: 280px;
          height: 110px;
          margin-left: 50%;
          transform: translateX(-50%);
          margin-top: 14px;
          font-size: 14px;
          color: #666666;
          line-height: 22px;
          word-break: break-all;
          text-overflow: ellipsis;
          overflow: hidden;
          display: -webkit-box;
          -webkit-line-clamp: 5;
          -webkit-box-orient: vertical;
        }
      }
      .notice-item + .notice-item {
        margin-left: 40px;
      }
    }
  }
  .organization-bg {
    width: 100%;
    height: 418px;
    background: url("~@/assets/img/organization-bg.png") no-repeat;
    background-size: 100% 418px;
    margin-top: 51px;
    overflow: hidden;
    .organization-content {
      width: 1400px;
      margin-left: 50%;
      transform: translateX(-50%);
      .organization-list {
        height: 270px;
        width: 100%;
        display: grid;
        grid-template-columns: repeat(4, 335px);
        grid-template-rows: repeat(2, 120px);
        gap: 30px 20px;
        .organization-item {
          background-color: #e6eeff;
          border-radius: 60px 0 0 60px;
          display: flex;
          align-items: center;
          .circle {
            background-color: #fafdff;
            min-width: 120px;
            width: 120px;
            height: 120px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            .logo {
              width: 81px;
              height: 80px;
            }
          }
          .organization-name {
            width: 162px;
            cursor: pointer;
            color: #0066be;
            line-height: 28px;
            font-size: 18px;
            font-weight: 700;
            margin-left: 24px;
            word-break: break-all;
            text-overflow: ellipsis;
            overflow: hidden;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            -webkit-box-orient: vertical;
          }
        }
      }
    }
  }
  .competition {
    margin-bottom: 50px;
    .competition-content {
      width: 1400px;
      margin-left: 50%;
      transform: translateX(-50%);
      .competition-item {
        background-color: #fafdff;
        border: 1px solid #ededed;
        border-radius: 4px;
        height: 145px;
        width: 100%;
        box-sizing: border-box;
        padding: 24px 0 0 20px;
        overflow: hidden;
        cursor: pointer;
        transition: 0.3s;
        &:hover {
          font-variant: JIS83;
          box-shadow: 0 1px 16px rgba(0,0,0,.1);
        }
        .competition-title {
          width: 1300px;
          font-size: 16px;
          font-weight: 700;
          color: #333333;
          word-break: break-all;
          text-overflow: ellipsis;
          overflow: hidden;
          display: -webkit-box;
          -webkit-line-clamp: 1;
          -webkit-box-orient: vertical;
          margin-bottom: 8px;
        }
        .competition-time {
          font-size: 12px;
          color: #808080;
        }
        .competition-remarks {
          width: 1357px;
          font-size: 14px;
          color: #666666;
          line-height: 21px;
          word-break: break-all;
          text-overflow: ellipsis;
          overflow: hidden;
          display: -webkit-box;
          -webkit-line-clamp: 2;
          -webkit-box-orient: vertical;
          margin-top: 15px;
        }
      }
      .competition-item + .competition-item {
        margin-top: 11px;
      }
    }
  }
  .members {
    width: 100%;
    height: 460px;
    background: url("~@/assets/img/members.png") no-repeat;
    background-size: 100% 580px;
    margin-top: 50px;
    overflow: hidden;
    .title-and-more {
      width: 1400px;
      margin-left: 50%;
      transform: translateX(-50%);
    }
    .members-list {
      width: 1600px;
      background-color: #fff;
      border-radius: 4px;
      margin-left: 50%;
      transform: translateX(-50%);
      padding-left: 100px;
      box-sizing: border-box;
      overflow: hidden;
      box-shadow: 2px 8px 10px 0px rgba(18, 69, 172, 0.1);
      .list {
        display: flex;
        flex-wrap: wrap;
        box-sizing: border-box;
        margin: 30px 0;
        .members-item {
          width: 234px;
          height: 121px;
          box-sizing: border-box;
          border: 1px solid #ededed;
          display: flex;
          align-items: center;
          justify-content: center;
          cursor: pointer;
          .member-logo {
            height: 70%;
            transition: all 0.3s;
          }
          &:hover {
            .member-logo {
              height: 75%;
            }
          }
        }
        .members-item + .members-item {
          border-left: 0;
        }
        .members-item:nth-child(7n) {
          border-left: 1px solid #ededed;
        }
        .members-item:nth-child(n + 7) {
          border-top: 0;
        }
      }
      /* .support {
        font-size: 18px;
        font-weight: 700;
        color: #333333;
      }
      .introduce {
        font-size: 14px;
        color: #333333;
        line-height: 20px;
        margin: 15px 100px 40px 0;
      } */
    }
  }
}
</style>
<style lang="scss">
.index-popover.el-popover {
  min-width: unset !important;
  margin: 0 !important;
  padding: 0 !important;
  .list {
    padding: 0;
    margin: 0;
    .list-item {
      padding: 8px 10px !important;
      list-style: none;
      margin: 0;
      cursor: pointer;
      &:hover {
        color: #4575ff;
        background-color: mix(#4575ff, #fff, 3%);
      }
    }
  }
}
</style>
