<template>
  <div id="Taipei">
    <!-- {{latlng}} -->
    <select class="custom-select custom-select-md" v-model="start">
      <option v-for="data in cata">{{data}}</option>
    </select>
    <table class="table table-bordered table-hover">
      <thead class="head table-dark">
        <tr>
          <th scope="col">景點</th>
          <th scope="col">分類</th>
          <th scope="col">捷運</th>
          <th scope="col">地址</th>
          <th scope="col">營業資訊</th>
          <th scope="col">交通</th>
        </tr>
        <GmapMap
          class="gmap"
          @mouseout="hideFunction()"
          :center="{lat: latlng.lat, lng: latlng.lng}"
          :zoom="18"
          map-type-id="terrain"
          style="width: 800px; height: 450px"
        >
          <GmapMarker
            :position="{lat: latlng.lat, lng: latlng.lng}"
            :clickable="true"
            :draggable="true"
            @click="center='{lat: latlng.lat, lng: latlng.lng}'"
          />
        </GmapMap>
        <!-- 將地圖放在列表外以免載入時一次跑大筆地圖資料造成效能低落 -->
      </thead>
      <tbody>
        <tr v-for="data in datas" v-if="choseOne(data)">
          <th class="view" scope="row">{{data.view}}</th>
          <td class="cata">{{data.cata}}</td>
          <td class="mrt">{{data.MRT}}</td>
          <td
            class="add"
            @mouseover="showFunction(),latlng.lat = data.lat , latlng.lng = data.long"
          >
            <!-- 透過mouseover抓當下執行對象的經緯度並改變data裡的laglng數值 -->
            <!-- 透過數值改變投射到gmap來抓位置 -->
            <div class="map" :class="data.id"></div>
            <a href="#">{{data.add}}</a>
          </td>
          <td class="info">{{data.info}}</td>
          <td class="com">{{data.communication}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
export default {
  name: "TaipeiTravel",
  created() {
    this.getview();
  },
  data() {
    return {
      datas: [],
      latlng: {
        lat: 25.137077,
        lng: 121.508447
      },
      // 給地圖一個初始起點
      cata: [
        "景點類別",
        "養生溫泉",
        "藍色公路",
        "	歷史建築",
        "藝文館所",
        "單車遊蹤",
        "戶外踏青",
        "宗教信仰",
        "其　　他"
      ],
      start: "景點類別"
    };
  },
  methods: {
    getview() {
      let xhr = new XMLHttpRequest();
      let self = this;
      xhr.open(
        "GET",
        "https://data.taipei/opendata/datalist/apiAccess?scope=resourceAquire&rid=36847f3f-deff-4183-a5bb-800737591de5"
      );
      xhr.onload = function() {
        let viewJson = JSON.parse(xhr.responseText);
        let getview = viewJson.result.results;
        // console.log(getview);
        getview.forEach(item => {
          self.datas.push({
            view: item.stitle,
            cata: item.CAT2,
            MRT: item.MRT,
            add: item.address,
            info: item.MEMO_TIME,
            communication: item.info,
            long: Number(item.longitude),
            lat: Number(item.latitude),
            id: "map" + item._id
          });
          // 將資料推到data裡的datas以便拿取資料
        });
      };
      xhr.send();
    },
    choseOne(item) {
      let self = this;
      if (self.start == "景點類別") {
        return true;
      } else if (item.cata == self.start) {
        return true;
      }
    },
    showFunction(item) {
      let self = this;
      $(".gmap").css("display", "block");
      console.log("It's showing");
    },
    // 顯示地圖的func
    hideFunction() {
      $(".gmap").css("display", "none");
    }
    // 隱藏地圖的func
  }
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
tbody {
  /* border: solid 5px blue; */
  width: 100%;
}
th,
td {
  height: 100%;
}
.head {
  position: relative;
}
.gmap {
  position: fixed;
  z-index: 2;
  top: 130px;
  left: 400px;
  display: none;
  padding: 0px;
  margin: 0px;
}
.view {
  width: 130px;
}
.cata {
  width: 90px;
}
.mrt {
  width: 75px;
}
.add {
  position: relative;
}
.map {
  position: absolute;
  /* background-color:black; */
  left: 50px;
  border: 1px black solid;
  width: 800px;
  height: 500px;
  display: none;
  /* display: block; */
}
</style>
