/** 首页列表
* update by 张帅(3120921953@qq.com) 2017/08/06 添加几个显示字段
*/
<template>
  <!-- <div>我是JD发布页面</div> -->
  <div class="publish">
    <div class="publish-con">
      <p class="publish-item"><span>1&nbsp;</span>职位基本信息</p>
      <div class="job-item">
        <p class="left-part">地区／area</p>
        <span class="right-part">{{info.area}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">公司名／companyname</p>
        <span class="right-part">{{info.companyName}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">融资程度／financing</p>
        <span class="right-part">{{info.financing | filter_financing}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">职位类型／jobType</p>
        <span class="right-part">{{info.jobType | filter_jobType}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">发布时间／ctime</p>
        <span class="right-part">{{info.ctime | filter_time}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">更新时间／updateTime</p>
        <span class="right-part">{{info.updateTime | filter_time}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">访问次数／visitCount</p>
        <span class="right-part">{{info.visitCount}}</span>
      </div>
      <p class="publish-item"><span>2&nbsp;</span>职位要求</p>
      <div class="job-item">
        <p class="left-part">经历和学历／exp & edu</p>
        <span class="right-part small-right">{{info.minYear | filter_minYear}}</span>
        <span class="right-part small-right">{{info.academic | filter_academic}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">薪资范围(月薪)／salary</p>
        <span class="right-part small-right">{{info.minWage | toFixedWage}}　　至</span>
        <span class="right-part small-right">{{info.maxWage | toFixedWage}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">关键字／keywords</p>
        <span class="right-part">{{keywords}}</span>
      </div>
      <div class="job-item">
        <p class="left-part">职位描述／requirement</p>
        <div class="textarea">{{info.description}}</div>
      </div>
      <div class="job-item">
        <p class="left-part">一句话广告</p>
        <div class="textarea"><span ref="forAd">{{forAd}}</span><br>
          <button @click="copyToClipBoard">点击可以复制发送本广告到群里</button>
        </div>
      </div>
	  <div class="contact-content">
		<contact-by-QQ :qqNumber="0"></contact-by-QQ>
	  </div>
    </div>
  </div>
</template>
<script>
  import contactByQQ from '@/components/contactByQQ'
  export default {
    name: 'jddetail',
    created () {
      this.loadInfo()
    },
    data () {
      return {
        info: {
          keywords: [],
          area: '',
          minYear: '0',
          academic: '0',
          financing: '0',
          minWage: '0',
          maxWage: '0',
          companyName: '',
          jobType: '1',
          description: ''
        },
        errorMessage: ''
      }
    },
    computed: {
      keywords () {
        return this.info.keywords.join('， ')
      },
      forAd () {
        return `${this.info.area}地区的【${this.info.companyName}】招${this.info.minYear > 0 ? this.info.minYear + '年前端' : '前端（无年限要求）'}，
薪资水平${this.info.minWage}至${this.info.maxWage}，
有意者请查看链接：${window.location.href}`
      }
    },
    methods: {
      formatKeywords () {
        this.info.keywords = this.info.keywords.replace(/[,，]/g, '，')
      },
      // 输入校验
      check () {
        this.errorMessage = ''
        if (this.info.area.length === 0) {
          this.errorMessage = '必须填写地区'
          return false
        }
        if (this.info.companyName.length === 0) {
          this.errorMessage = '必须填写公司名'
          return false
        }
        if (this.info.financing === '0') {
          this.errorMessage = '必须选择融资程度'
          return false
        }
        if (this.info.minWage === '0') {
          this.errorMessage = '必须选择薪资下限'
          return false
        }
        if (this.info.maxWage === '0') {
          this.errorMessage = '必须选择薪资上限'
          return false
        }
        if (this.info.description.length < 50) {
          this.errorMessage = '职位描述不能少于50字，现在有' + this.info.description.length + '字'
          return false
        } else if (this.info.description.length > 2000) {
          this.errorMessage = '职位描述不能大于2000字，现在有' + this.info.description.length + '字'
          return false
        }
        return true
      },
      // 拉取信息
      loadInfo () {
        this.http.getJobInfo({id: this.$route.path.replace('/detail/', '')}).then(result => {
          console.log(result.data)
          if (result.data.code === '200') {
            this.info = result.data.data
          }
        })
      },
      // 复制一句话广告到剪切板
      copyToClipBoard () {
        this.$copy.copy(this.forAd)
      }
    },
    components: {
      contactByQQ
    }
  }
</script>
<style scoped>
  .ml40 {
    margin-left: 40px;
  }

  .publish {
    padding: 20px;
    background: #EEF0F5;
  }

  .publish-con {
    width: 1200px;
    background: #fff;
    margin: 0 auto;
    box-sizing: border-box;
    padding: 30px 200px;
  }

  .publish-item {
    line-height: 20px;
    margin-bottom: 8px;
  }

  .publish-item span {
    color: #00b38a;
    font-size: 24px;
  }

  .job-item {
    min-height: 46px;
    margin-bottom: 12px;
    position: relative;
  }

  .left-part {
    float: left;
    width: 180px;
    line-height: 46px;
    text-align: right;
    margin-right: 20px;
    font-weight: lighter;
  }

  .right-part {
    float: left;
    width: 580px;
    line-height: 46px;
    text-align: left;
    margin-right: 20px;
    font-weight: lighter;
  }

  .small-right {
    width: 100px;
  }

  .textarea {
    width: 600px;
    height: auto;
    padding: 12px;
    line-height: 20px;
    overflow-y: auto;
    box-sizing: border-box;
    display: inline-block;
    resize: none;
    white-space: pre-wrap;
    color: #333;
  }
</style>
