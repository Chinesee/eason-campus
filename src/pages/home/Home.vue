<template>
  <div class="main">
    <image
      class="banner-img"
      src="/static/images/img-banner.png"
      mode="widthFix"
    />
    <div class="content p-4">
      <!-- 首要功能 -->
      <div class="card flex justify-around">
        <div
          class="block flex flex-column align-center justify-center px-4 py-1"
          style="border-radius: 15rpx;"
          v-for="(block, i) in blocks"
          :key="i"
          @click="routerTo(block.url)"
        >
          <image
            class="block-icon mb-1"
            mode="widthFix"
            :src="block.icon"
          />
          <div>{{ block.label }}</div>
        </div>
      </div>

      <!-- 今日课程 -->
      <div
        class="card"
        style="padding: 0;"
      >
        <div class="schedule-header flex align-center justify-between px-3 pt-2 pb-1">
          <span>{{ currentDate }}</span>
          <span>{{ days[currentDay] }}课程</span>
          <span class="flex align-center">
            <span
              class="primary mr-1"
              style="font-weight: bold;font-size: 38rpx;"
            >{{ currentWeek }}</span>
            周
          </span>
        </div>

        <div
          class="schedule-content p-3"
          v-if="logged"
        >
          <template v-if="schedules.length > 0">
            <div
              class="schedule-item flex align-center justify-between mb-1 gray"
              v-for="(item, key) in schedules"
              :key="key"
            >
              <span>{{ item.period }}</span>
              <span class="name">{{ item.name }} [{{ item.addr }}]</span>
            </div>
          </template>
          <!-- 周末愉快 -->
          <div
            class="to-login text-center"
            v-else-if="isWeekend"
          >
            <image
              class="to-login__icon mb-2"
              mode="widthFix"
              src="/static/images/img-tip0.svg"
            />
            <div class="gray">周末愉快ヽ( ^∀^)ﾉ</div>
          </div>
          <div
            class="text-center gray"
            v-else
          >今日无课，宜休息</div>
        </div>

        <!-- 去登录 -->
        <div
          v-if="!logged"
          class="to-login text-center p-3"
        >
          <div>
            <image
              class="to-login__icon mb-2"
              mode="widthFix"
              src="/static/images/empty-3.svg"
            />
            <navigator
              class="to-login__btn bg-primary"
              url="../login/main"
            >去登录</navigator>
          </div>
        </div>
      </div>

      <!-- 次要功能 -->
      <div class="card flex justify-around">
        <div
          class="section flex flex-column align-center justify-center py-2 px-3"
          style="border-radius: 10rpx;"
          v-for="(section, i) in sections"
          :key="i"
          @click="routerTo(section.url)"
        >
          <image
            class="section-icon mb-1"
            mode="widthFix"
            :src="section.icon"
          />
          <div class="section-label">{{ section.label }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const blocks = [
  {
    label: '成绩',
    icon: '/static/icons/icon-grade.png',
    url: '../grade/main'
  },
  {
    label: '考勤',
    icon: '/static/icons/icon-attendance.png',
    url: '../attendance/main'
  },
  {
    label: '考试',
    icon: '/static/icons/icon-notice.png',
    url: '../exam-time/main'
  }
]
const sections = [
  {
    label: '华软校历',
    icon: '/static/icons/icon-cal.png',
    url: '../calendar/main'
  },
  {
    label: '平时成绩',
    icon: '/static/icons/icon-pin.png',
    url: '../usual-grade/main'
  },
  {
    label: '待开发',
    icon: '/static/icons/icon-tag.png',
    url: ''
  },
  {
    label: '待开发',
    icon: '/static/icons/icon-flash.png',
    url: ''
  }
]
const days = ['周日', '周一', '周二', '周三', '周四', '周五', '周六']

export default {
  data() {
    return {
      blocks,
      sections,
      days,
      currentWeek: null,
      currentDate: null,
      currentDay: null,
      schedules: []
    }
  },

  mounted() {
    ({
      currentWeek: this.currentWeek,
      currentDate: this.currentDate,
      currentDay: this.currentDay
    } = this.$store.state)
  },

  computed: {
    isWeekend() {
      // 0-周日， 6-周六
      return [0, 6].includes(this.currentDay)
    },

    logged() {
      const logged = this.$store.state.logged
      if (logged && this.currentDay && !this.isWeekend) {
        this.setSchedule(this.currentDay - 1, this.currentWeek)
      }
      return logged // 返回登录状态
    }
  },

  methods: {
    setSchedule(day, week) {
      const schedules = []

      const periods = [
        '09:00 - 10:20',
        '10:40 - 12:00',
        '12:30 - 13:50',
        '14:00 - 15:20',
        '15:30 - 16:50',
        '17:00 - 18:20',
        '19:00 - 20:20',
        '20:30 - 21:50'
      ]

      mpvue.getStorageSync('schedule').forEach((item, i) => {
        const course = item[day].course
        if (course.length > 0) {
          course.forEach(el => {
            if (el.weeks.includes(week)) {
              el.period = periods[i]
              schedules.push(el)
            }
          })
        }
      })

      this.schedules = schedules
    },

    routerTo(url) {
      if (this.logged) {
        if (url.length > 0) {
          mpvue.navigateTo({ url })
        }
      } else {
        mpvue.showModal({
          content: '您还没有绑定 Myscse 账号哦',
          confirmText: '去绑定',
          confirmColor: '#5d97f7',
          success({ confirm }) {
            if (confirm) {
              mpvue.navigateTo({ url: '../login/main' })
            }
          }
        })
      }
    }
  },

  onShareAppMessage() {
    return {
      title: '首页',
      path: '/pages/home/main'
    }
  }
}
</script>

<style lang="scss" scoped>
.main {
  display: flex;
  flex-direction: column;
  height: 100vh;
  overflow: hidden;
}

.banner-img {
  position: relative;
  z-index: 9999;
  width: 100%;
  box-shadow: 0 0 45rpx #f9f7fa;
}

.content {
  flex: 1;
  height: auto;
  overflow-y: scroll;
  overflow-x: hidden;
}

.card {
  box-sizing: border-box;
  margin-bottom: 30rpx;
  padding: 15rpx;
  background: white;
  border-radius: $radius;
}

.name {
  width: 60%;
  text-align: right;
  word-wrap: break-word;
  word-break: break-all;
}

.block:active {
  background: rgba(0, 0, 0, 0.1);
}

.block-icon {
  width: 100rpx;
  height: 100rpx;
}

.section:active {
  background: rgba(0, 0, 0, 0.1);
}

.section-icon {
  width: 60rpx;
  height: 60rpx;
}

.section-label {
  font-size: 25rpx;
}

.schedule-header {
  border-bottom: 1rpx solid #ececec;
}

.schedule-content {
  font-size: 26rpx;
}
.schedule-item:last-child {
  margin-bottom: 0;
}

.to-login {
  box-sizing: border-box;
  width: 100%;
}
.to-login__icon {
  width: 40%;
}
.to-login__btn {
  width: 180rpx;
  height: 60rpx;
  margin: 0 auto;
  padding: 0;
  line-height: 60rpx;
  font-size: 28rpx;
  color: white;
  border-radius: 30rpx;
}
</style>
