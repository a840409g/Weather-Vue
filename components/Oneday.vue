<template>
  <div id="Oneday">
    <table class="table table-bordered table-hover">
      <thead class="table-dark">
        <tr>
          <th style="text-align:center" scope="col">
            <select class="area custom-select custom-select-sm" v-model="area"> 
              <option selected="selected">請選擇行政區</option>
              <option>松山區</option>
              <option>信義區</option>
              <option>大安區</option>
              <option>中正區</option>
              <option>大同區</option>
              <option>萬華區</option>
              <option>文山區</option>
              <option>南港區</option>
              <option>內湖區</option>
              <option>士林區</option>
              <option>北投區</option>
            </select>
          </th>
          <th scope="col">時間</th>
          <th scope="col">地區</th>
          <th scope="col">溫度</th>
          <th scope="col">濕度</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(data,id) in datas" v-if="choseOne(data)">
          <th class="idTd" scope="row">{{id+1}}</th>
          <td class="timeTd">{{data.dataTime}}</td>
          <td class="nameTd">{{data.locationName}}</td>
          <td class="tempTd">{{data.攝氏度}}</td>
          <td class="moiTd">{{data.濕度}}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "Oneday",
  created() {
    this.getTemp();
    this.getMoi();
  },
  data() {
    return {
      datas: [],
      area: "請選擇行政區",
    };
  },
  methods: {
    getTemp() {
      let Tempxhr = new XMLHttpRequest();
      let self = this;
      Tempxhr.open(
        "GET",
        "https://data.taipei/opendata/datalist/apiAccess?scope=resourceAquire&rid=1f1aaba5-616a-4a33-867d-878142cac5c4"
      );
      Tempxhr.onload = function() {
        let tempJson = JSON.parse(Tempxhr.responseText);
        let getTemp = tempJson.result.results;
        getTemp.forEach(item => {
          let temp = item.value;
          self.datas.push({
            locationName: item.locationName,
            dataTime: item.dataTime,
            攝氏度: item.value + "℃",
            濕度: null
          });
          // console.log(self.datas)
        });
      };
      Tempxhr.send();
    },
    getMoi() {
      let moixhr = new XMLHttpRequest();
      let self = this;
      moixhr.open(
        "GET",
        "https://data.taipei/opendata/datalist/apiAccess?scope=resourceAquire&rid=c73a9461-91c8-4e5c-b728-86b6feb24ffb"
      );
      moixhr.onload = function() {
        let moiJson = JSON.parse(moixhr.responseText);
        let getmoi = moiJson.result.results;
        for (
          let i = 0, j = 0;
          i < getmoi.length, j < self.datas.length;
          i++, j++
        ) {
          self.datas[i].濕度 = getmoi[i].value + "%";
          console.log(self.datas[i]);
        }
      };
      moixhr.send();
    },
    choseOne(item){
      let self = this;
      if(self.area == "請選擇行政區"){
        return true;
      }else if(item.locationName == self.area){
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
.idTd {
  width: 40px;
  text-align: center;
}
.timeTd {
  width: 230px;
}
.nameTd {
  width: 50px;
}
.tempTd {
  width: 50px;
}
.moiTd {
  width: 200px;
}
</style>
