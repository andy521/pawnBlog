<template>
  <div class="BDraft">
    <div class="allBlogContent">
      <div class="error-wrapper" v-show="!articleList.length">
      	<v-Loading v-show="loading"></v-Loading>        
        <v-ErrorCom v-show="!loading"></v-ErrorCom>
      </div>
      <v-article :articleList="articleList" :toEdit="true" :isDraft="true" @lastPage="lastPage" @firstPage="firstPage" @showMessage="showMessage"></v-article>
    </div>
  </div>
</template>

<script>
import Article from '@/components/common/article/article'
import ErrorCom from '@/components/common/error/error'
import Loading from '@/components/common/loading/loading'
import { queryDrafteList, currentUser } from '@/bmob'
export default {
  data () {
    return {
      articleList: [],
      userInfo: {},
      loading: true
    }
  },
  mounted () {
    this._initUserInfo()
  },
  methods: {
    _initUserInfo () {
      currentUser().then((result) => {
        this.userInfo = {
          'avatar': result.get('avatar'),
          'showName': result.get('showName'),
          'singName': result.get('singName'),
          'userId': result.id
        }
        this._getArticleList()
      }, (res) => {
        //没有session，未登录（未按步骤操作）
        this.$router.push({ path: '/login' })
      })
    },
    _getArticleList () {
      queryDrafteList({ 'userId': this.userInfo.userId }).then((res) => {
        this.articleList = res
        this.loading = false
      }, function (res) {
        this.emit('showMessage', res)
        this.loading = false        
      })
    },
    lastPage () {
      this.$emit('showMessage', '已经是尾页了')
    },
    firstPage () {
      this.$emit('showMessage', '已经是首页了')
    },
    showMessage (msg) {
      this.$emit('showMessage', msg)
    }
  },
  components: {
    'v-article': Article,
    'v-ErrorCom': ErrorCom,
    'v-Loading': Loading
  }
}
</script>

<style lang="less" scoped>
.BDraft {
  position: fixed;
  top: 50px;
  bottom: 0;
  left: 200px;
  right: 0;
  background-color: #fff;
  overflow-y: scroll; // overflow-x: hidden;
  padding: 50px 15% 0 8%;
  .article {
    width: 1000px;
  }
}
@media (max-width: 1000px) {
  .BDraft {
    position: absolute;
    top: 0;
    left: 0;
    bottom: 50px;
    padding: 0;
    .article {
      width: 100%;
      .info {
        display: none;
      }
    }
  }
}
</style>
