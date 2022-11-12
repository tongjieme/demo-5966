<template>
  <div class="home">
    <!-- <input type="file" id="fileForUpload" ref="file" v-on:change="change($event)"> -->
    <textarea name="" id="" cols="30" rows="10" v-model="text"></textarea>
    <button type="button" @click="parseCSV(text)">解析</button>
    <table id="customers">
        <thead>
          <tr>
            <td>员工</td>
            <td v-for="day in list[Object.keys(list)[0]]">
              {{day['日期']}}
            </td>
          </tr>
        </thead>
      <tbody>
        <tr v-for="(value, key) in list">
          <td>
            {{key}}
          </td>
          <td v-for="day in value" v-html="getStatus(day)">
          </td>
          
        </tr>
      </tbody>
    </table>
    <span>🔘:休息</span>
    <span>✔️:正常</span>
    <span>⭕️:迟到</span>
    <span>♢:缺卡</span>
    <h1>使用说明:</h1>
    <hr style="height: 10px;background: red">
    <h3 style="color: red">第一步: 在钉钉导出当月考勤表</h3>
    <img src="Snipaste_2021-06-08_23-32-27.jpg" alt="">
    <hr style="height: 10px;background: red">
    <h3 style="color: red">第二步: 如下图红框, 选中后复制, </h3>
    <img src="Snipaste_2021-06-08_22-45-40.jpg" alt="">
    <hr style="height: 10px;background: red">
    <h3 style="color: red">第三步: 粘贴到新的Exel表格文件, 然后另存为CSV 文件</h3>
    <img src="Snipaste_2021-06-08_23-22-49.jpg" alt="">
    <hr style="height: 10px;background: red">
    <img src="Snipaste_2022-03-29_23-12-14.jpg" alt="">
    
    
    
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import * as papa from "papaparse";
import * as moment from "moment";
import * as _ from "lodash";
window._ = _;
export default {
  name: 'Home',
  components: {
    HelloWorld
  },
  data() {
    return {
      text: "",
      list: {}
    }
  },
  methods: {
    getStateIcon(text, text2, xia_ban_shi_jian, obj) {
      if(text == '正常') {
        var hh = xia_ban_shi_jian.split(":")[0]
        var mm = xia_ban_shi_jian.split(":")[1]
        if(parseInt(hh) >= 19) {
          return `<span title="下班时间: ${xia_ban_shi_jian}">★</span>`
        }
        if(parseInt(hh) == 18 && parseInt(mm) >= 30) {
          return `<span title="下班时间: ${xia_ban_shi_jian}">★</span>`
        }
        return `✔️`
      } 
       if(text == '迟到') {
         if(obj) {
           return `<span title="${obj['上班1打卡时间']}">⭕️</span>`
         }
        return `⭕️`
      } 
      if(text == '缺卡') {
        return `♢`
      } 
      if(text2 == '休息') {
        return '🔘'
      }
      return text
    },
    getStatus(obj) {
      var html = "";
      var html1 = this.getStateIcon(obj['上班1打卡结果'], obj['班次'], obj['上班1打卡时间'], obj)
      var html2 = this.getStateIcon(obj['下班1打卡结果'], obj['班次'], obj['下班1打卡时间'])

      if(html1 == html2) {
        return html1
      }


      return html1 + html2;
      // 加班五角星
      return "未知"

      
    },
    parseCSV(str) {
      str = str.split("\n").filter(v => {
        return v.replace(/,/g, "").length != 0
      }).join("\n")
      var temp1 = papa.parse(str, {
        header: true
      }).data;


      var persons = _.groupBy(temp1.filter(v => {
        return v
      }).sort(function (a,b) {
          if(a['日期'] > b['日期']) {
            return 1
          }
          if(a['日期'] < b['日期']) {
            return -1
          }
          return 0
      }), v => v['姓名'])
      console.log(persons);

      this.list = persons
      
      
      
    },
    async change(event) {
      const file = event.target.files.item(0);
      const text = await file.text();
      console.log(text);
      this.parseCSV(text)
    }
  },
  mounted() {
    console.log("sss");
    
  }
}
</script>
<style>

    #customers {
      font-family: Arial, Helvetica, sans-serif;
      border-collapse: collapse;
      width: 100%;
      text-align: center;
    }

    #customers td, #customers th {
      border: 1px solid #ddd;
      padding: 8px;
    }

    #customers tr:nth-child(even){background-color: #f2f2f2;}

    #customers tr:hover {background-color: #ddd;}

    #customers th {
      padding-top: 12px;
      padding-bottom: 12px;
      text-align: left;
      background-color: #04AA6D;
      color: white;
    }
</style>