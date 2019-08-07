<!--
<template>
   <div class="hello">
    <h1>{{ msg }}</h1>
    <button @click="getPageData(2)">取得該頁資料</button>
    
    <ul v-for="(entry, index) in pageEntry" :key="entry.index">
      <li>
        <a>{{index}}.{{entry.youtubeId}}, {{entry.Object}}, {{entry.filename}}</a>
      </li>
    </ul>
  </div> 
</template>
-->
<template>
  <div id="app" class="align-items-center">
    <br />
    <!-- <div>currentPage: {{currentPage}}</div> -->
    <b-pagination
      size="md"
      :total-rows="totalItems"
      v-model="currentPage"
      :per-page="perPage"
      @input="getPageData(currentPage)"
      align="center"
    ></b-pagination>
    <div class="row">
      <div class="col-md-12">
        <!-- 
            <li v-for="item in posts" :key="item.id">
            {{ item.id }} : {{ item.title }}
            </li> 
        -->
        <ul v-for="(entry, index) in pageEntry" :key="entry.index">
          <li>
            <a>{{index}}.{{entry.youtubeId}}, {{entry.Object}}, {{entry.filename}}</a>
          </li>
        </ul>
      </div>
    </div>
    <br />
    <button @click="downLoadJpg()">取得該圖片資料</button>
    <img :src="imgUrl" />
  </div>
</template>

<script>
//Import axios for REST API calls
import axios from "axios";
import VueAxios from "vue-axios";
//Import bootstrap CSS
import "bootstrap/dist/css/bootstrap.css";
//Import bootstrap vue CSS
import "bootstrap-vue/dist/bootstrap-vue.css";

export default {
  name: "HelloWorld",
  props: {
    msg: String
  },

  data: function() {
    return {
      allEntry: [],

      currentPage: 1,
      perPage: 10,
      pageEntry: [],
      totalItems: 0,
      imgUrl: ""
    };
  },
  methods: {
    getPageData: function(currentPage = 1) {
      let self = this;
      let totalEntry = self.allEntry;
      console.log("In getPageData");
      console.log("perPage =", self.perPage);
      console.log("totalEntry =", totalEntry);
      console.log(totalEntry.slice(0, self.perPage));
      self.pageEntry = totalEntry.slice(
        self.perPage * (currentPage - 1),
        self.perPage * currentPage
      );
      console.log(self.pageEntry);
    },
    downLoadJpg: function() {
      let self = this;
      let url =
        "http://140.96.0.34:50013/filterfun/getYoloImg/04jm7VfInbo/res_00000024.jpg";

      // //this.$http.get("url", {
      // axios
      //   .get(url, {
      //     responseType: "arraybuffer"
      //   })
      //   .then(function(response) {
      //     //将从后台获取的图片流进行转换
      //     return (
      //       "data:image/jpeg;base64," +
      //       btoa(
      //         new Uint8Array(response.data).reduce(
      //           (data, byte) => data + String.fromCharCode(byte),
      //           ""
      //         )
      //       )
      //     );
      //   })
      //   .then(function(data) {
      //     //接收转换后的Base64图片
      //     // obj.imgCodeUrl = data;
      //     console.log(data);
      //   })
      //   .catch(function(err) {});
      console.log("url: ", url);
      //axios.get(url, { responseType: "arraybuffer" }).then(function(response) {
      axios.get(url, { responseType: "blob" }).then(function(response) {
        const content = response.data; //返回的文件流
        console.log("content:", content);
        const cnHeader = response.headers; //返回的headers
        //const blob = new Blob([content], { type: "image/jpeg" }); //指定处理类型
        const blob = new Blob([content], { type: "image/jpeg" });
        //const blob = new Blob([content]);
        //const fileName = "test.jpg"; //下载的文件名称
        self.imgUrl = window.URL.createObjectURL(blob);
        console.log(cnHeader);

        console.log(self.imgUrl);

        // let link = document.createElement("a");

        // link.style.display = "none";
        // link.href = url;
        // link.setAttribute("download", "test.jpg");

        // document.body.appendChild(link);
        // link.click();
      });
    }
  },

  created: function() {
    let self = this;
    axios({
      methods: "get",
      url: "http://140.96.0.34:50013/dataset/queryTrainYoloTag/04jm7VfInbo"
    }).then(response => {
      //self.data = response.data;
      console.log(response.data.data);
      //console.log(response.data.data.length);
      var resdata = response.data.data;
      self.allEntry = resdata;
      self.totalItems = response.data.data.length;
      console.log(self.totalItems);
      console.log(self.allEntry);

      this.getPageData(1); //init first page
    });
  },
  mounted: function() {
    // let self = this;
  }
};
</script>
