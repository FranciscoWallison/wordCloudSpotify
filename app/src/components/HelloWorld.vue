<template>
  <div class="hello">
    <canvas id="word_cloud" class="word_cloud" width="400" height="400"></canvas> 
  </div>
</template>

<script>
import WordCloud from  'wordcloud'
import axios from 'axios';

export default {
  data () {
    return {   
      htmlElement: document.getElementById('word_cloud'),
      dataInfor: "",
      db:[  
                {"word":"布丁","freq":70},
                {"word":"name","freq":38},
                {"word":"false","freq":38},
                {"word":"var","freq":28},
                {"word":"rstudio","freq":26},
                {"word":"true","freq":22},
                {"word":"留言","freq":20},
                {"word":"url","freq":20},
                {"word":"server","freq":20},
                {"word":"分享","freq":18},
                {"word":"課程","freq":18},
                {"word":"連結","freq":18},
                {"word":"openvz","freq":17},
                {"word":"什麼","freq":16},
                {"word":"wordcloud","freq":15},
                {"word":"virtual","freq":15},
                {"word":"機器","freq":15},
                {"word":"虛擬","freq":15},
                {"word":"target","freq":14},
                {"word":"key","freq":14},
                {"word":"文字","freq":14},
                {"word":"sharemessage","freq":14},
                {"word":"資訊","freq":13},
                {"word":"字形","freq":13},
                {"word":"google","freq":13},
                {"word":"null","freq":12},
                {"word":"閱讀","freq":12},
                {"word":"中文","freq":12},
                {"word":"data","freq":12},
                {"word":"displaymodefull","freq":12},
                {"word":"研究","freq":11},
                {"word":"linux","freq":11},
                {"word":"程式","freq":10},
                {"word":"檔案","freq":10},
                {"word":"系統","freq":10},
                {"word":"服務","freq":10},
                {"word":"function","freq":10},
                {"word":"需要","freq":9},
                {"word":"建立","freq":9},
                {"word":"使用","freq":9},
                {"word":"管理","freq":9},
                {"word":"titlexdx","freq":8},
                {"word":"postid","freq":8},
                {"word":"httpblogpulipuliinfo","freq":8},
                {"word":"posts","freq":8},
                {"word":"twitter","freq":8},
                {"word":"pinterest","freq":8},
                {"word":"blog","freq":8},
                {"word":"環境","freq":8},
                {"word":"facebook","freq":8},
                {"word":"comments","freq":8},
                {"word":"運作","freq":7},
                {"word":"machine","freq":7},
                {"word":"怎麼","freq":6},
                {"word":"chinese","freq":6},
                {"word":"開箱","freq":6},
                {"word":"語言","freq":6},
                {"word":"httpblogpulipuliinforwordcloudwhyrswordclouddrawshtml","freq":5},
                {"word":"standalone","freq":5},
                {"word":"environment","freq":5},
                {"word":"ubuntu","freq":5},
                {"word":"password","freq":5},
                {"word":"rs","freq":5},
                {"word":"ssh","freq":5},
                {"word":"這個","freq":5},
                {"word":"密碼","freq":5},
                {"word":"設定","freq":5},
                {"word":"draws","freq":5},
                {"word":"已經","freq":5},
                {"word":"httpblogpulipuliinforrstudioserveropenvzstandalonerhtml","freq":5},
                {"word":"安裝","freq":4},
                {"word":"如下","freq":4},
                {"word":"sans","freq":4},
                {"word":"學習","freq":4},
                {"word":"網頁","freq":4},
                {"word":"撰寫","freq":4},
                {"word":"架設","freq":4},
                {"word":"mining","freq":4},
                {"word":"教學","freq":4},
                {"word":"stringnoto","freq":4},
                {"word":"作業","freq":4},
                {"word":"一樣","freq":4},
                {"word":"生活","freq":4},
                {"word":"畫面","freq":4},
                {"word":"文章","freq":4}
            ]
    }   
  },
  created () {
    this.dataInfor = axios({
          url: 'https://spotifycharts.com/regional/global/daily/latest/download',
          method: 'GET',
          // responseType: 'blob', // Important
          // headers: {"Access-Control-Allow-Origin": "*"},
          // withCredentials: true,
          
        })
    .then(function(response) {
      return response.data;
    })
    .catch(function() {
      return "";
    })
  },
  methods: {     
    async consultExcel() {
        try {
          let key = await this.dataInfor
          this.processData(key);
        } catch (e) {
          console.log(e);
        }
        
    },
    processData(allText) {
      try{
        let record_num = 5;  // or however many elements there are in each row
        let allTextLines = allText.split(/\r\n|\n/);
        let entries = allTextLines[0].split(',');
        let lines = [];

        let headings = entries.splice(0,record_num);
        while (entries.length>0) {
            let tarr = [];
            for (let j=0; j<record_num; j++) {
                tarr.push(headings[j]+":"+entries.shift());
            }
            lines.push(tarr);
        }
        console.log('entries', entries, allText);
      }catch(e){
         console.log('error!!!processData',e)
        if(e){
          // If fails, Do something else
        }
      }
    },
    updateCanvas() {
      try{
        this.consultExcel();
        let list = [];
        for (var i in this.db) {
          list.push([this.db[i]["word"], this.db[i]["freq"]])
        }

        WordCloud.miniumFontSize = 50;
      
        WordCloud( document.getElementById('word_cloud') , { list: list} );
       
      }catch(e){
        
        if(e){
          // If fails, Do something else
        }
      }
    }
  },
  watch: {
    exampleContent() {
      this.updateCanvas();
    }
  },
  mounted() {
    this.updateCanvas();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
