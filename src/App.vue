<template>
  <div id="app">
    <div class="property">
            <div class="property-item">
                code: <input v-model="code" type="text">  name: <input v-model="name" type="text">
                type: <select v-model="type">
                        <option value="updata">更新</option>
                        <option value="insert">插入</option>
                    </select>
                   <button class="btn btn-primary" @click="query">查询</button> 
            </div>
            <div class="property-item">
                <span class="word-spaceing">行:</span><input v-model.number="rowNums" type="number"> <span class="word-spaceing">列:</span><input v-model.number="colNums" type="number">
            </div>
            <div class="property-item">
                <span class="word-spaceing" style="width: 35px;">x:</span> <input type="number" ref="x"> <span class="word-spaceing" style="width: 35px;">y:</span> <input type="number" ref="y"> <span>rowspan:</span> <input type="number" ref="rowspanNum"> <span>colspan:</span> <input type="number" ref="colspanNum">
                <button class="btn btn-primary" @click="merge">合并</button>
            </div>
            
        </div>
        <div style="margin-top: 10px;">
            <table border="1" cellspacing="0" ref="table">
                <tr v-for="(row,rowIndex) in rowNums">
                    <th v-for="item in cols[rowIndex]" :rowspan="item.rowspan" :colspan="item.colspan">
                        <input type="text">
                    </th>
                </tr>
            </table>
            <div v-show="showMessage" style="text-align: center;">不存在</div>
        </div>
        <div class="btn-wrapper">
            <button class="btn btn-primary" @click="send">提交</button>
        </div>
    
  </div>
</template>

<script>
import Hello from './components/Hello'

export default {
  name: 'app',
  data() {
    return {
      code: '',
      name: '',
      type: '',
      rowNums: 0,
      colNums: 0,
      mergeData: [],
      showMessage: false
    }
  },
  computed: {
    cols: function(){
      var res = [];
      for(var i=0;i<this.rowNums;i++){
        var tempArr = []
        for(var j=0;j<this.colNums;j++){
          var temp = {};
          temp.rowspan = 0;
          temp.colspan = 0;
          tempArr.push(temp);
        }
        res.push(tempArr);
      }
      for(var j=0;j<this.mergeData.length;j++){
        if(this.mergeData[j].rowspan>1){
          res[this.mergeData[j].x][this.mergeData[j].y].rowspan = this.mergeData[j].rowspan;
          for(var k=1;k<this.mergeData[j].rowspan;k++){
            res[parseInt(this.mergeData[j].x)+k].splice(parseInt(this.mergeData[j]),1);
          }
        }else{
          res[this.mergeData[j].x][this.mergeData[j].y].colspan = this.mergeData[j].colspan;
          var spliceIndex = parseInt(this.mergeData[j].y) + 1;
          var spliceNum = parseInt(this.mergeData[j].colspan) - 1; 
          res[parseInt(this.mergeData[j].x)].splice(spliceIndex,spliceNum);             
        }
      }
      return res;
    }
  },
  methods: {
    merge: function(){
            var temp = {};
            temp.x = this.$refs.x.value;
            temp.y = this.$refs.y.value;
            temp.rowspan = this.$refs.rowspanNum.value;
            temp.colspan = this.$refs.colspanNum.value;
            this.mergeData.push(temp);
    },
    send: function(){
            var data = {};
            data.headers = [];
            data.type = this.type;
            data.report_name = this.name;
            data.report_code = this.code;
            data.maxrow = this.rowNums;
            data.maxcol = this.colNums;
                /*console.log(data);
                console.log($(this.$refs.table).find('tr'));*/
            var _this =this;
            $(this.$refs.table).find('tr').each(function(x,elem){
              $(this).find('input').each(function(y,elem){
                data.headers.push({
                  'value': $(this).val(),
                  'row': x,
                  'col': y,
                  'rowspan': _this.cols[x][y].rowspan,
                  'colspan': _this.cols[x][y].colspan
                })
              })
            })
            console.log(data);
            /*this.$http.post('http://localhost/leqeedata/RecordAgent/DiyHeaderRecord/insertOrUpdateHeaders', data).then(response => {
              response = response.body;
              console.log(response);
              if(response.message === "success"){
                alert(response.message)
              }
            })*/

            $.ajax({
              url: "http://localhost/leqeedata/RecordAgent/DiyHeaderRecord/insertOrUpdateHeaders",
              type: "post",
              dataType: "json",
              data: data,
              success: function(data){
                        console.log(data);
                        if(data.message === "success"){
                            alert(data.message);
                        }
                      }
            })
    },
    query: function(){
            var _this = this;
            _this.rowNums = 0;
            _this.colNums = 0;
            _this.mergeData = [];
            /*this.$http.post('http://localhost/leqeedata/RecordAgent/DiyHeaderRecord/getHeadersByCode', {'report_code':this.code}).then(response => {
              var data = response.body;
              console.log(data);
              if(!data.error_message){
                _this.rowNums = parseInt(data[0].maxrow);
                _this.colNums = parseInt(data[0].maxcol);
                var tempArr = [];
                for(var i=0;i<data.length;i++){
                  var temp = {};
                  temp.x = parseInt(data[i].row);
                  temp.y = parseInt(data[i].col);
                  temp.rowspan = parseInt(data[i].rowspan);
                  temp.colspan = parseInt(data[i].colspan);
                  tempArr.push(temp);

                }
                _this.mergeData = tempArr;
                console.log(_this.rowNums);
                console.log(_this.colNums);
                console.log(_this.mergeData);
                Vue.nextTick(function(){
                  $(_this.$refs.table).find('input').each(function(index,elem){
                    console.log(data[parseInt(index)].value);
                    $(this).val(data[parseInt(index)].value);
                                  })
                })
                _this.showMessage = false;
              }else {
                _this.showMessage = true;
              }

            })*/
            $.ajax({
                url: "http://localhost/leqeedata/RecordAgent/DiyHeaderRecord/getHeadersByCode",
                type: "post",
                dataType: "json",
                data: {
                    'report_code': this.code
                },
                success: function(data){
                          console.log(data);
                          if(!data.error_message){
                              _this.rowNums = parseInt(data[0].maxrow);
                              _this.colNums = parseInt(data[0].maxcol);
                              var tempArr = [];
                              for(var i=0;i<data.length;i++){
                                  var temp = {};
                                  temp.x = parseInt(data[i].row);
                                  temp.y = parseInt(data[i].col);
                                  temp.rowspan = parseInt(data[i].rowspan);
                                  temp.colspan = parseInt(data[i].colspan);
                                  tempArr.push(temp);

                              }
                              _this.mergeData = tempArr;
                              console.log(_this.rowNums);
                              console.log(_this.colNums);
                              console.log(_this.mergeData);
                              _this.$nextTick(function(){
                                  $(_this.$refs.table).find('input').each(function(index,elem){
                                      console.log(data[parseInt(index)].value);
                                      $(this).val(data[parseInt(index)].value);
                                  })
                              })
                              _this.showMessage = false;
                          }else {
                              _this.showMessage = true;
                          }
                        }
            })
    }
  },
  components: {
    
  }
}
</script>

<style>
        #app{
            margin-top: 30px;
        }
        .property{
            margin-left: 20px;
            margin-top: 30px;
        }
        table input{
            width: 100%;
            height: 40px;
            border: 0;
            text-align: center;
        }
        .property input{
           
        }
        .property-item{
            margin-top: 10px;
        }
        .word-spaceing{
            display: inline-block;
            width: 40px;
            padding-right: 6px;
            text-align: right;
        }
        .btn-wrapper{
            margin-top: 30px;
            text-align: center;
        }
        .btn-wrapper .btn{
            width: 200px;
        }
</style>
