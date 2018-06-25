<template>
  <div class="hello">
      <h1>{{msg}}</h1>

      <!-- 游戏内容 -->
      <div class="wrap" v-if="!status">
        <!-- 这是战士画面 -->
        <div class="box">
          <div class="wrap-vol" :style="'width:'+zvolumn+'%'"></div>
          <div class="volume" >{{zvolumn}}</div>
          <p>战士</p>
        </div>
        <!-- 这是BOOS的血条 -->
        <div class="box">
             <div class="wrap-vol" :style="'width:'+bvolumn+'%'"></div>
            <div class="volume" >{{bvolumn}}</div>
            <p>BOOS</p>
        </div>
      </div>
      <!-- 这是攻击按钮 -->
      <div class="btn start-layout" v-if="!status">
          <!-- <button>帮助</button> -->
          <div v-if="flag">
            <button @click="resetStart">重新开始</button>
            <button @click="outgame">退出</button>
          </div>
          <div v-else>
              <div v-if="suspend">
                <button  @click="handleStart(false)">暂停游戏</button>
                <button @click="FastAttack">自动攻击</button>
                <button @click="attack">攻击</button>
                <button @click="Recovery">恢复血量</button>
              </div>
              <button v-else @click="handleStart(true)">继续游戏</button>
          </div>
      </div>
      <!-- 这是操作按钮 -->
      <div class="btn" v-if="status">
          <button @click="StartGame">开始游戏</button>
      </div>

      <!-- 这是攻击记录 -->
      <div class="log" v-if="!status">
        <p v-for="(item,index) in logs" :key="index" :class="['rm-volumn',{'add-volumn':item.status}]">
          <span v-if="!item.status">战斗记录: {{item.name}}失去{{item.expend}}血&nbsp;&nbsp;&nbsp;还剩余{{item.surplus}}</span>
          <span v-else>战斗记录: {{item.name}}恢复{{item.expend}}血&nbsp;&nbsp;&nbsp;还剩余{{item.surplus}}</span>
        </p>
      </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: '打小怪兽',
      flag: false,
      status:true,
      zvolumn: 100,
      bvolumn: 100,
      logs:[],
      timer:null,
      suspend: true,
    }
  },
  mounted(){
    console.log(this.flag)
  },
  methods:{
    // 开始游戏
    StartGame(){
      this.status = false;
    },
    // 攻击
    attack(){
      if( this.flag ) {
        alert('游戏已经结束')
        return
      }
      // 获取一个取值范围，在这个范围内处理伤害
      let Monster = this.Random(8,12); // 战士所伤害怪物的血量
      let Rnum = this.Random(6,16);  // 怪物伤害战士的血量

      this.zvolumn = this.zvolumn - Rnum <= 0 ? 0 :this.zvolumn - Rnum
      this.bvolumn = this.bvolumn - Monster <= 0 ? 0 :this.bvolumn - Monster
      
      if(this.zvolumn == 0 && this.bvolumn == 0){
         this.msg = '平局';
         this.flag = true
          this.clearTime()
         return 
      }
      if(this.zvolumn <= 0 || this.bvolumn <= 0 ){
        this.msg = this.zvolumn <= 0 ? '战士获胜': '怪兽获胜';
        this.flag = true
        this.clearTime()
        return
      }
      if( this.zvolumn < 8 || this.bvolumn < 6){
        this.msg = this.zvolumn > 8 && this.bvolumn < 6 ? '战士获胜': '怪兽获胜';
        this.flag = true
        this.clearTime()
        return
      }
      this.handleLogs(Monster,Rnum)
    },
    Random(min,max){
      let Range = max - min;
      let Rand = Math.random();
      return min + Math.round(Rand*Range); // 四舍五入
    },

    // 恢复血量
    Recovery(){
      // 如果dayu90就不恢复
      if( this.zvolumn > 90 || this.flag){
        alert('当前状态不能恢复血量！')
        return;
      }
      this.zvolumn += 10 ;
      let war = {
          name: '战士',
          surplus: this.zvolumn, // 剩余
          expend: 10,// 消耗
          status: true
      }
       this.logs.unshift(war)
    },
    // 往logs日志里放入攻击消息
    handleLogs(Monster,Rnum){
      // 定义两个对象
      let war = {
          name: '战士',
          surplus: this.zvolumn, // 剩余
          expend: Rnum,// 消耗
          status: false
      }

      let boos = {
          name: '怪兽',
          surplus: this.bvolumn, // 剩余
          expend: Monster,// 消耗
          status: false
      }

      this.logs.unshift(war,boos)
    },
    // 自动攻击
    FastAttack(){
      clearInterval(this.timer)
      this.timer = setInterval(() => {
        this.attack()
      }, 500);
    },
        // 如果有一方获胜，则任务结束，清除时间函数
    clearTime(){
      if( this.flag ){
        clearInterval(this.timer)
      }
    },
    // 重开始
    resetStart(){
      this.flag = false
      this.logs = []
      this.zvolumn = 100
      this.bvolumn = 100
      this.msg = '打小怪兽'
    },
    outgame(){
      this.status = true
      this.msg = '打小怪兽'
    },
    // 暂停恢复
    handleStart(val){
      this.suspend = val;
      clearInterval(this.timer)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .wrap{
    height: 100px;
    margin: 10px auto;
  }
  .wrap .box{
    float: left;
    width: 45%;
    height: 60px;
    position: relative;
  }
   .wrap .box:nth-child(2){
    float: right;
  }
  .wrap-vol{
      width: 100%;
      height: 100%;
      background: #f72626;
  }
   .wrap .box:nth-child(1) .wrap-vol{
    float: left;
  }
  .wrap .box:nth-child(2) .wrap-vol{
    float: right;
  }

  .wrap .box .volume{
    width: 100%;
    height: 100%;
    line-height: 60px;
    color: #fff;
    background: #459090;
  }
  .btn{
    width: 100%;
  }
  .btn button{
    display: block;
    border: none;
    outline: none;
    width: 150px;
    padding: 20px 40px;
    text-align: center;
    margin: 10px auto;
    cursor: pointer;
    color: #fff;
    font-weight: 700;
    background-color: #459090;
  }
  .btn button:nth-child(1){
    background-color: #41b883;
  }
   .btn button:nth-child(2){
    background-color: #d8850f;
  }
   .btn button:nth-child(3){
    background-color: #f72626;
  }
  .start-layout{
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: center;
  }
  .log{
    width: 300px;
    height: 200px;
    margin: 0 auto;
    text-align: left;
    font-size: 14px;
    border: 1px solid #ddd;
    padding: 10px;
    overflow-y: auto
  }
  .log p{
    margin: 0;
    line-height: 22px;
  }
  .rm-volumn{
    color: #f72626;
  }
  .add-volumn{
    color:#41b883;
  }
</style>
