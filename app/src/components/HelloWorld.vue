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
      db:[]
    }   
  },
  created () {
    this.dataInfor = axios({
          url: 'https://spotifycharts.com/regional/br/daily/latest/download',
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
    this.$watch('db', () => {
      console.log('Comments changed!')
    })
  },
  methods: {     
    async consultExcel() {
        try {
          let key = await this.dataInfor;
          let data = await this.processData(key);
          this.db = data;
         
          return data;
        } catch (e) {
          console.log(e);
          return [];
        }
        
    },
    async consultandoBandaAndMusic(allText){
      var allTextLines = allText.split(/\r\n|\n/);
      var headers = allTextLines[0].split(',');
      var lines = [];
      // var titleAndMusic = [];

      for (var k=1; k<allTextLines.length; k++) {
          var data = allTextLines[k].split(',');
          if (data.length == headers.length) {

              var tarr = [];
              for (var j=0; j<headers.length; j++) {                
                tarr.push(headers[j]+""+data[j]);
              }
              
              lines.push(tarr);
          }
      }
      let letras = "";
      for (var i=1; i<lines.length; i++) {
        var artist = lines[i][2].replace('"', "").replace('"', "");
        var song   = lines[i][1].replace('"', "").replace(" - Ao Vivo", "").replace('"', ""); 

        letras = letras +" "+ await  this.axios({
          url: 'https://api.vagalume.com.br/search.php?art='+artist.replace(' ', "-")+'&mus='+song.replace(' ', "-"),
          method: 'GET',          
        })
        .then(function(response) {
          // console.log("consultandoBandaAndMusic", response.data.mus[0].text);
          return response.data.mus[0].text;

        })
        .catch(function() {
          
          // console.log( "ERROR-consultandoBandaAndMusic", "");
          return " ";
        });
      // console.log( "consultandoBandaAndMusic", letras);
      }
      return letras;
    },
    async processData(allText) {
      try{
        // let record_num = 5;  // or however many elements there are in each row
        let arry_consulta = await this.consultandoBandaAndMusic(allText);
        arry_consulta = arry_consulta.normalize('NFD')
        .replace(/([\u0300-\u036f]|[^0-9a-zA-Z\s])/g, '')
        .replace(/\n|\r/g, " ")
        .replace(/\s{2,}/g, ' ');

        var res = arry_consulta.split(" ");
        res.sort();
        let array_elements = res;

        array_elements.sort();

        var current = null;
        var cnt = 0;
        var word_freq = [];
        for (var i = 0; i < array_elements.length; i++) {
            if (array_elements[i] != current) {
                if (cnt > 0) {
                    // console.log(current + ' comes --> ' + cnt + ' times<br>');
                  word_freq.push({"word":current,"freq":cnt});
                }
                current = array_elements[i];
                cnt = 1;
            } else {
                cnt++;
            }
        }
        this.db = word_freq;
        return word_freq;
      }catch(e){
          console.log('error!!!processData',e)
          
        if(e){
          // If fails, Do something else
        }
        return [];
      }
    },
    async updateCanvas() {
      try{
        await this.consultExcel();
        let list = [];
         console.log('consultExcel', this.db);
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
