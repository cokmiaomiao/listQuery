<template>
  <div class="hello">
    <div class="table">
      <div>选择年龄段：
        <select v-model="selectItem" @change="selectAge($event)">
          <option v-for="(item,i) in ageOptions" :key="i" :value="item.id">{{ item.val }}</option>
        </select>
        {{selectItem}}
      </div>
      <div>搜索ID：<input type="text" placeholder="请输入姓名" :value="searchName" ref="getName"><button @click="search">搜索</button></div>

    </div>
    <div class="table list title">
      <div class="checkbox"><input type="checkbox"/></div>
      <div class="itemId">ID</div>
      <div class="itemUsername">用户名</div>
      <div class="itemSex">性别</div>
      <div class="itemAge" @click="compareAge">年龄</div>
      <div class="itemCity">城市</div>
      <div class="itemWritting">签名</div>
    </div>
    <div class="table list" v-for="(item,index) in disPlayArr" :key="index">
      <div class="checkbox"><input type="checkbox"/></div>
      <div class="itemId">{{ item.id }}</div>
      <div class="itemUsername">{{ item.username }}</div>
      <div class="itemSex">{{ item.sex }}</div>
      <div class="itemAge">{{ item.age }}</div>
      <div class="itemCity">{{ item.city }}</div>
      <div>{{ item.writting }}</div>
    </div>
    <div class="table list">
      <div class="checkbox" @click="turnUp"> <button>&lt;</button> </div>
      <div class="checkbox" v-for="(item,i) in btns" :key="i" @click="trunTo(i)"><button> {{ item }} </button></div>
      <div v-if="this.pages>5">……</div>
      <div v-if="this.pages>5" class="checkbox"> <button>{{this.pages}}</button> </div>
      <div class="checkbox" @click="turnDown"> <button>&gt;</button> </div>
      <div>到第 <input type="number" :value="nowPage+1" oninput="if(value<1)value=1" ref="getValue">页</div>
      <div><button @click="turnAt">确定</button></div>
      <div>共{{ allArr.length }}条</div>
      <div>
        <select v-model="selectItemAmount" @change="selectItems($event)">
            <option value="5">5条/页</option>
            <option value="10">10条/页</option>
        </select>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      disPlayArr:[],
      allArr:[],
      pages:0,//控制页数
      items:5,//每页显示多少条数据
      nowPage:0,//当前为第几页
      btns:[],//控制按钮数组
      compare:true,//年龄排序  true从小到大
      searchName:'',
      getArr:[],
      ageOptions:[
        {
          id:0,
          val:'---'
        },
        {
          id:1,
          val:'1-20'
        },
        {
          id:2,
          val:'21-40'
        },
        {
          id:3,
          val:'41-60'
        },
        {
          id:4,
          val:'61-80'
        },
        {
          id:5,
          val:'80+'
        }
      ],//年龄筛选框数组
      selectItem:'---',
      selectItemAmount:5
    }
  },
  methods:{
    //将获取到的数组拆分
    sliceArr(arr){
      this.pages = Math.ceil(arr.length/this.items);
      this.btns=[];
      if(this.pages<=5){
        for(let i =1;i<=this.pages;i++){
          this.btns.push(i)
        }
      }else{
        this.btns.push(this.nowPage+1);
        this.btns.push(this.nowPage+2);
        this.btns.push(this.nowPage+3);
      }
    },
    //跳转按钮
    trunTo(i){
      this.disPlayArr=this.allArr.slice(i*this.items,(i*this.items)+this.items)
      this.nowPage=i;
    },
    //跳转上一页按钮
    turnUp(){
      let i = this.nowPage;
      if(this.nowPage==0){
        return
      }
      this.disPlayArr=this.allArr.slice((i-1)*this.items,((i-1)*this.items)+this.items)
      this.nowPage--;
    },
    //跳转下一页按钮
    turnDown(){
      let i = this.nowPage;
      if(this.nowPage==this.pages-1){
        return
      }
      this.disPlayArr=this.allArr.slice((i+1)*this.items,((i+1)*this.items)+this.items)
      this.nowPage++;
    },
    //跳转至...
    turnAt(){
      let num = this.$refs.getValue.value;
      //当value值大于页面数时，取值为最大数
      if(num>this.pages){
        num=(this.pages);
        this.nowPage=this.pages-1;
        this.disPlayArr=this.allArr.slice((num-1)*this.items,(num-1)*this.items+this.items);
        this.nowPage=num-1;
      };
      if(num==(this.nowPage+1)){return}
      this.disPlayArr=this.allArr.slice((num-1)*this.items,(num-1)*this.items+this.items);
      this.nowPage=num-1;
    },
    //比较年龄
    compareAge(){
      let arr =this.allArr;
      if(this.compare){
        arr.sort(function(a,b) {
          return a.age - b.age
        });
        this.compare=false;
      }else{
        arr.sort(function(a,b) {
          return b.age - a.age
        });
        this.compare=true;
      }
      this.allArr=arr;
      this.disPlayArr=this.allArr.slice(0,this.items);
      this.sliceArr(this.allArr);
    },
    //名字搜索框
    search(){
      let name = this.$refs.getName.value;
      let newArr = this.getArr.filter((item)=>{
        item.status = item.username.indexOf(name);
        return item.username.indexOf(name)!==-1
      })
      newArr.sort(function(a,b) {
        return a.status - b.status
      });
      this.allArr=newArr;
      this.disPlayArr=this.allArr.slice(0,this.items)
      this.sliceArr(this.allArr)
    },
    //年龄段选择
    selectAge(e){
      let num =e.target.selectedIndex;
      let a=0,b=0;
      switch(num) {
        case 0:
            a=0;b=999;
            break;
        case 1:
            a=1;b=20;
            break;
        case 2:
            a=21;b=40;
            break;
        case 3:
            a=41;b=60;
            break;
        case 4:
            a=61;b=80;
            break;
        case 5:
            a=81;b=999;
            break;
        default:
            a=0;b=999;
      } 
      let newArr = this.getArr.filter((item)=>{
        return item.age>=a&&item.age<=b
      })
      this.allArr=newArr;
      this.disPlayArr=this.allArr.slice(0,this.items)
      this.sliceArr(this.allArr)
    },
    //
    selectItems(e){
      let num =e.target.value;
      this.items=num;
      this.disPlayArr=this.allArr.slice(0,this.items);
      this.sliceArr(this.allArr);

    }
  },
  created(){
    const arr =[
        {
          id:'10001',
          username:'张三',
          sex:'男',
          age:'21',
          city:'海南',
          writting:'468636465'
        },
        {
          id:'10002',
          username:'不包',
          sex:'女',
          age:'26',
          city:'东莞',
          writting:'爱啥啥更好地保护是'
        },
        {
          id:'10003',
          username:'等等',
          sex:'女',
          age:'61',
          city:'三亚',
          writting:'45343人'
        },
        {
          id:'10004',
          username:'反是',
          sex:'男',
          age:'21',
          city:'瓦罗兰',
          writting:'分身乏术'
        },
        {
          id:'10005',
          username:'沙发上',
          sex:'女',
          age:'31',
          city:'江南',
          writting:'阿斯蒂芬'
        },
        {
          id:'10006',
          username:'所发生的',
          sex:'男',
          age:'41',
          city:'西安',
          writting:'舒服舒服'
        },
        {
          id:'10007',
          username:'是否',
          sex:'女',
          age:'51',
          city:'东升',
          writting:'舒服舒服是否'
        },
        {
          id:'10008',
          username:'水电费',
          sex:'女',
          age:'23',
          city:'按时',
          writting:'或多或少'
        },
        {
          id:'10009',
          username:'是否',
          sex:'男',
          age:'29',
          city:'是否',
          writting:'4舒服舒服'
        },
        {
          id:'10010',
          username:'大概',
          sex:'女',
          age:'101',
          city:'双方三房',
          writting:'大股东还是'
        },
        {
          id:'10011',
          username:'大概',
          sex:'男',
          age:'58',
          city:'日报',
          writting:'的故事根深蒂固大概'
        },
        {
          id:'10012',
          username:'广东省',
          sex:'男',
          age:'54',
          city:'中国',
          writting:'更多时候'
        },
        {
          id:'10013',
          username:'张三丰',
          sex:'男',
          age:'124',
          city:'中国',
          writting:'更多时候'
        },
        {
          id:'10014',
          username:'老张',
          sex:'女',
          age:'61',
          city:'三亚',
          writting:'45343人'
        },
        {
          id:'10015',
          username:'张宇',
          sex:'女',
          age:'61',
          city:'三亚',
          writting:'45343人'
        },
        {
          id:'10016',
          username:'ww张宇',
          sex:'女',
          age:'61',
          city:'三亚',
          writting:'45343人'
        },
        {
          id:'10017',
          username:'张宇',
          sex:'女',
          age:'81',
          city:'三亚',
          writting:'4this.items343人'
        }
      ]
      //初始化
    this.getArr = arr ;
    this.allArr = arr ;
    this.disPlayArr=this.getArr.slice(0,this.items);
    this.sliceArr(this.getArr);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
  .title{
    background-color: #ccc;
  }
  .table{
    display: flex;
  }
  .list{
    display: flex;
    height: 45px;
    line-height: 45px;
    border-bottom:1px solid #ccc;
    width: 1000px;
  }
  .checkbox{
    width: 60px;
  }
  .itemId,.itemUsername,.itemSex,.itemAge,.itemCity{
    width: 95px;
  }
  .itemWritting{
    width: 400px;
    text-align: center;
  }
</style>
