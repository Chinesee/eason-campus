<script>
import { getTodayInfo } from './utils'

export default {
  created() {
    /*
     * 平台 api 差异的处理方式:  api 方法统一挂载到 mpvue 名称空间, 平台判断通过 mpvuePlatform 特征字符串
     * 微信：mpvue === wx, mpvuePlatform === 'wx'
     * 头条：mpvue === tt, mpvuePlatform === 'tt'
     * 百度：mpvue === swan, mpvuePlatform === 'swan'
     * 支付宝(蚂蚁)：mpvue === my, mpvuePlatform === 'my'
     */

    const { commit } = this.$store

    // 设置当前周，当前星期几
    const data = getTodayInfo('2019/9/1')
    commit('setTodayInfo', data)

    const user = mpvue.getStorageSync('user')
    const userInfo = mpvue.getStorageSync('info')
    if (user && userInfo) {
      commit('login')
      commit('setUserInfo', userInfo)
    }
  }
}
</script>

<style>
.main {
  font-size: 30rpx;
  background: #f9f7fa;
}
</style>
