<template>
  <div class="search_wrap">
    <div style='height:44px;'>
      <div class="search_bar">
        <a class="cancel_search" href="javascript:void(0);" @click='goBack()'>取消</a>
        <div class="search_item">
          <i class="iconfont icon-sousuo"></i>
          <input class="search" v-focus type="search" placeholder="搜索书城" v-model='booktext' @keyup="getSearchBook">
        </div>
      </div>
    </div>
    <div class="search_list" v-show='!booktext.length'>
      <h3 class="search_title">热门搜索</h3>
      <ul class="search_content">
        <router-link tag='li' v-for="sort in sorts" :to="{name:'sortbook',params:{id:sort.tag}}" :key="1">{{sort.tag}}</router-link>
      </ul>
    </div>
    <div class="search_list" v-show='booktext.length'>
      <div v-if='data.length==0'>
        <p class="search_tip">正在搜索中...</p>
      </div>
    </div>

    <div v-infinite-scroll="getMore" infinite-scroll-distance="200" v-show='booktext.length'>
      <search-item v-for='item in data' :searchs='item.books' :key="2"></search-item>
    </div>

  </div>
</template>
<script>
  import Api from '../assets/js/api'
  import searchItem from '../components/searchbook_item'
  import Vue from 'vue';
  //滚动
  import infiniteScroll from 'vue-infinite-scroll'
  Vue.use(infiniteScroll);
  export default {
    name: 'search',
    components: {
      searchItem
    },
    data() {
      return {
        msg: true,
        booktext: '',
        data: [],
        thisNum: 20,
        sorts: [{
            tag: 'JavaScript'
          },
          {
            tag: 'Python'
          },
          {
            tag: 'Php'
          },
          {
            tag: 'React'
          },
          {
            tag: 'Angular'
          },
          {
            tag: '文学'
          },
          {
            tag: '小说'
          }
        
        ]
      }
    },
    directives: {
      focus: {
        inserted: function (el) {
          el.focus()
        }
      }
    },
    methods: {
      getSearchBook() {
        var self = this;
        this.data = [];
        this.$http.jsonp(Api.url + '/v2/book/search?q=' + self.booktext +
          '&apikey=0b2bdeda43b5688921839c8ecb20399b&start=0&count=20').then(function (response) {
          self.data.push(response.body);
          // console.log(self.data[0].books[0].rating)
        });
      },
      getMore() {
        var self = this;
        this.$http.jsonp(Api.url + '/book/search?q=' + self.booktext + '&apikey=0b2bdeda43b5688921839c8ecb20399b&start=' +
          self.thisNum + '&count=20').then(function (response) {
          self.data.push(response.body);
          self.thisNum += 20;

        });
      },
      goBack() {
        window.history.back()
      }
    }
  }

</script>
<style scoped>
  .search_wrap {
    background: #fff;
  }

  .search_tip {
    width: 100%;
    line-height: 4;
    color: #888;
    text-align: center;
  }

  .search_bar {
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 44px;
    background: #fff;
    border-bottom: 1px solid #f1f1f1;
  }

  .cancel_search {
    float: right;
    width: 48px;
    text-align: center;
    font-size: 14px;
    color: #15ef0a;
    height: 44px;
    line-height: 44px;
  }

  .search_item {
    position: relative;
    margin: 5px 50px 5px 10px;
    height: 34px;
    line-height: 34px;
    background: #eaeaea;
    border-radius: 6px;
    overflow: hidden;
  }

  .search_item .iconfont {
    float: left;
    width: 20px;
    color: #aaa;
    padding: 0 10px;
  }

  .search_item .search {
    position: absolute;
    width: 100%;
    left: 40px;
    top: 0;
    right: 0;
    height: 34px;
    line-height: 34px;
  }

  .search_title {
    width: 100%;
    height: 32px;
    line-height: 32px;
    padding: 0 16px;
    color: #ccc;
    box-sizing: border-box;
    background: #f7f7f7;
  }

  .search_content {
    padding: 0 16px;
  }

  .search_content li {
    font-size: 13px;
    color: #888;
    height: 44px;
    line-height: 44px;
    border-bottom: 1px solid #f1f1f1;
  }

</style>
