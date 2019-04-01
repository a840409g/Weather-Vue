<template>
  <div id="Oneday">
    <table class="table table-bordered table-hover">
      <thead class="table-dark">
        <tr>
          <th style="text-align:center" scope="col">
            <select class="area custom-select custom-select-sm" v-model="area">
              <option selected="selected">全台</option>
              <option>臺北市</option>
              <option>新北市</option>
              <option>桃園市</option>
              <option>臺中市</option>
              <option>臺南市</option>
              <option>高雄市</option>
              <option>基隆市</option>
              <option>新竹縣</option>
              <option>新竹市</option>
              <option>苗栗縣</option>
              <option>彰化縣</option>
              <option>南投縣</option>
              <option>雲林縣</option>
              <option>嘉義縣</option>
              <option>屏東縣</option>
              <option>宜蘭縣</option>
              <option>花蓮縣</option>
              <option>臺東縣</option>
              <option>澎湖縣</option>
              <option>金門縣</option>
              <option>連江縣</option>
            </select>
          </th>
          <th scope="col">時間</th>
          <th scope="col">縣市</th>
          <th scope="col">狀況</th>
          <th scope="col">溫度</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data,id) in datas" v-if="choseOne(data)">
          <th class="idTd" scope="row">{{id+1}}</th>
          <td class="timeTd">
            <span>
              Start:{{data.startTime}}
            </span>
            <br>
            <span>
              End:{{data.endTime}}
            </span>
          </td>
          <td class="nameTd">{{data.locationName}}</td>
          <td>{{data.satus}}</td>
          <td class="tempTd">{{data.lowTemp}}~{{data.hightTemp}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "TaiwanWeather",
  created() {
    this.getweather();
  },
  data() {
    return {
      datas: [],
      area: "全台"
    };
  },
  methods: {
    getweather() {
      let xhr = new XMLHttpRequest();
      let self = this;
      xhr.open(
        "GET",
        "https://data.taipei/opendata/datalist/apiAccess?scope=resourceAquire&rid=e6831708-02b4-4ef8-98fa-4b4ce53459d9"
      );
      xhr.onload = function() {
        let weatherJson = JSON.parse(xhr.responseText);
        let getweather = weatherJson.result.results;
        // console.log(getweather)
        getweather.forEach(item => {
          let weather = item.value;
          self.datas.push({
            locationName: item.locationName,
            startTime: item.startTime,
            endTime: item.endTime,
            satus: item.parameterName1,
            lowTemp: item.parameterName3+'℃',
            hightTemp: item.parameterName2+'℃',
          });
          // console.log(self.datas)
        });
      };
      xhr.send();
    },
    choseOne(item) {
      let self = this;
      if (self.area == "全台") {
        return true;
      } else if (item.locationName == self.area) {
        return true;
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
