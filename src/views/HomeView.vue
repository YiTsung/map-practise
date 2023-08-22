<template>
  <div class="home">
    <button type="button" class="toTop-btn rounded-circle  position-fixed bottom-0 end-0 m-3 btn btn-danger"
    :class="isTop ? 'toTop-display-block' : 'toTop-display-none'"
    @click.prevent="topFunction">
    <i class="bi bi-arrow-up-short icon-size"></i>
    </button>
    <div  class="mt-2">
      <div class="row h-100 mx-5">
        <div class="col-md-3 h-100 d-flex flex-column">
          <div class="form-floating mb-2">
            <!--使用雙向綁定(v-model)將關鍵字暫存入cacheSearch
                再透過computed裡的filterSearch去搜尋!-->
            <input type="text" class="form-control" id="search" placeholder="search" v-model="cacheSearch">
            <label for="search">search</label>
          </div>
          <div class="list-group option" >
          <!-- 使用v-for將資料放入表單!-->
            <label class="list-group-item" v-for="(item,key) in filterSearch" :key="item + key" @click="cacheArea = item">
              <input class="form-check-input me-1" type="radio" name="area" :value="item">
              {{ item.Name }}
            </label>
          </div>
        </div>
        <div class="col-md-8 h-100 d-flex flex-column">
          <div class="form-floating">
            <!--watch監控cacheArea 並將瀏覽紀錄存放進browseLog !-->
            <select  id="cacheArea" class="form-select w-50 mb-2" aria-label="select example" v-model="cacheArea">
              <option selected value="" disabled></option>
              <option  :value="log" v-for="(log,key) in browseLog" :key="log + key">{{key+1}}.{{log.Name}}</option>
            </select>
            <label for="cacheArea">瀏覽紀錄</label>
          </div>
          <div class="card overflow-auto" v-if="cacheArea.Name">
            <img :src="cacheArea.Picture1" class="card-img-top" :alt="cacheArea.Name">
            <iframe width="100%" height="300" frameborder="0" scrolling="no" marginheight="0" marginwidth="0"
              :src="`https://maps.google.com.tw/maps?f=q&hl=zh-TW&geocode=&q=${cacheArea.Py},${cacheArea.Px}(${cacheArea.Name})&z=16&output=embed`"></iframe>
            <div class="card-body">
              <h5 class="card-title">{{ cacheArea.Name }}</h5>
              <p>{{ cacheArea.Description }}</p>
            </div>
          </div>
          <div v-else class="card-body">
            <p class="no-title">請選擇場域</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  data () {
    return {
      datastore: [], // 抓取資料存放位置
      cacheArea: '', // 滑鼠點擊單筆資料暫存區
      cacheSearch: '', // 使用computed 收尋關鍵字
      browseLog: [], // 歷史LOG
      isTop: false
    }
  },
  computed: {
    filterSearch () {
      return this.datastore.filter(item => {
        return item.Name.match(this.cacheSearch)
      })
    }
  },
  methods: {
    // 取得遠端資料
    getAPI () {
      const apiUrl = 'https://raw.githubusercontent.com/hexschool/KCGTravel/master/datastore_search.json'
      this.$http.get(apiUrl)
        .then((res) => {
          this.datastore = res.data.result.records
          console.log(this.datastore)
        })
        .catch(err => console.log(err.respones))
    },
    topFunction () {
      document.body.scrollTop = 0
      document.documentElement.scrollTop = 0
    }
  },
  created () {
    this.getAPI()
    const that = this
    window.addEventListener('scroll', () => {
      const scrollPos = window.scrollY
      if (scrollPos >= 100) {
        that.isTop = true
      } else {
        that.isTop = false
      }
    })
  },
  watch: {
    cacheArea () {
      if (this.browseLog.length < 10) {
        this.browseLog.unshift(this.cacheArea)
        // console.log(this.browseLog);
      } else {
        this.browseLog.pop()
        this.browseLog.unshift(this.cacheArea)
      }
    }
  }
}
</script>
<style>
body{
  background-color: #b6e6ed;
}
.card-title{
  text-align: center;
  font-weight: bold;
}
.card-body p{
  text-indent: 20px;
  line-height: 1.5rem;
}
.no-title{
  font-size: 50px;
  font-weight: bold;
  color: brown;
  margin-top: 50px;
  text-align: center;
}
.toTop-btn{
    width: 45px;
    height: 45px;
  }
  .toTop-display-block{
    display: block !important;
  }
  .toTop-display-none{
    display: none !important;
  }
.icon-size{
  font-size: 30px;
  position:absolute;
  top: 0px;
  right: 6px;
}
</style>
