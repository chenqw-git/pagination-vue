<template>
  <ul class="pagination-box" ref="paginationBox" v-show="hasData">
    <li class="paginate-next">
      <a @click="before($event)" v-if="page>=1" style="width: 72px">上一页</a>
    </li>
    <div class="page-less" v-if="pageSize<=8">
      <li class="paginate-btn" v-for="n in pageSize">
        <a @click="jump(n)" :class="{'active':n==(parseInt(page)+1)}">
          <span :data-page="n">{{n}}</span>
        </a>
      </li>
    </div>
    <div class="page-large" v-if="pageSize>8">
      <li class="paginate-btn">
        <a @click="jump(1)" :class="{'active':1==(parseInt(page)+1)}"><span :data-page="1">1</span></a>
      </li>
      <li class="paginate-btn"  v-for="n in 5" v-if="n>1 && page<4">
        <a @click="jump(n)" :class="{'active':n==(parseInt(page)+1)}"><span :data-page="n">{{n}}</span></a>
      </li>
      <li v-if="page>=4">
        <p class="spot-box">...</p>
      </li>
      <li class="paginate-btn" v-for="n in filterArr(page)" v-if="page<=pageSize-5 && page>=4">
        <a @click="jump(n)" :class="{'active':n==(parseInt(page)+1)}"><span :data-page="n">{{n}}</span></a>
      </li>
      <li class="paginate-btn" v-for="n in selectArr(pageSize)" v-if="page>pageSize-5">
        <a @click="jump(n)" :class="{'active':n==(parseInt(page)+1)}">
                                  <span :data-page="n">
                                    {{n}}
                                  </span>
        </a>
      </li>
      <li v-if="pageSize-4>=5 && page<pageSize-4">
        <p class="spot-box">...</p>
      </li>
      <li class="paginate-btn">
        <a @click="jump(pageSize)" :class="{'active':pageSize==(parseInt(page)+1)}">
          <span :data-page="pageSize">{{pageSize}}</span>
        </a>
      </li>
    </div>
    <li class="paginate-next"><a @click="next()" v-if="page<pageSize-1" style="width: 72px">下一页</a></a></li>
    <li>
      <input type="text" class="toPage" v-model="goNum" @input="isNum" placeholder="页码">
    </li>
    <li class="paginate-next">
      <a @click="go()">转到</a>
    </li>
  </ul>
</template>

<script>
    export default {
        name: "pagination",
        props: {
          recordstotal: {
            type: Number,
            default: 0
          },
          size:{
            type: Number,
            default: 10
          },
          curpage:{
            type: Number,
            default: 0
          }

        },
        data(){
            return {
              page:this.curpage,
              goNum:''
            }
        },
      computed: {
        hasData(){
          if(this.pageSize>1){
            return true
          }else{
            return false
          }
        },
        end: function () {
          var res = (parseInt(this.page) + 1) * parseInt(this.size)
          if (res > this.recordstotal) {
            res = this.recordstotal
          }
          return res
        },
        pageSize: function () {
          var a = parseInt(this.recordstotal / this.size)
          var b = a + 1
          return this.recordstotal % this.size == 0 ? a : b
        },
      },
      methods:{
        selectArr(num) {
          var num=Number(num);
          return [num-4,num-3,num-2,num-1];
        },
        filterArr(num){
          var num=Number(num);
          return [num,num+1,num+2,num+3];
        },
        isNum(e){
          //
          var reg=/(^[1-9])+[0-9]*$/;
          if(reg.test(e.target.value)){
            this.goNum=e.target.value;
          }else{
            this.goNum="";
          }
        },
        go(){
          if(this.goNum!==''){
            if(this.goNum>this.pageSize){
              this.goNum=this.pageSize;
            }
            this.jump(parseInt(this.goNum));
            this.goNum='';
          }
        },
        // 分页
        before: function() {
          // this.jumpNum=this.page+1;
          if (parseInt(this.page) >= 1) {
            // var pageNo= this.jumpNum - 1;
            var pageNo= this.page;
            this.jump(pageNo);
          }
        },
        next: function() {
          // this.jumpNum=this.page+1;
          if (parseInt(this.end) < parseInt(this.recordstotal)) {
            var pageNo= parseInt(this.page) +2;
            this.jump(pageNo);
          }
        },
        jump: function(pageNo) {
          // this.jumpNum = pageNo;
          this.page = pageNo-1;
          if(this.page!=this.curpage){
            this.$emit('change-page', this.page)
          }
        },
        first: function() {
          if (this.pageSize > 0) {
            this.jump(1);
          }
        },
        final: function() {
          if (this.pageSize > 0) {
            this.jump(this.pageSize);
          }
        },
      },
      watch:{
          curpage(newV,oldV){
            if(newV!=oldV){
              this.jump(newV+1)
            }
          }
      },
      mounted(){
      }
    }
</script>
<style scoped>
  /*分页*/
  .pagination-box li {
    display: inline-block;
    vertical-align: top;
  }

  .pagination-box div {
    display: inline-block;
  }
  .pagination-box li a{
    display: inline-block;
    font-size:12px;
    color:#af9673;
    text-align: center;
    cursor: pointer;
  }
  .pagination-box li p{
    font-weight: 700;
    vertical-align: bottom;
    font-size: 14px;
    line-height: 0;
    display: inline-block;
  }
  .pagination-box .paginate-btn a{
    border:1px solid #af9673;
    width:32px;
    height:32px;
    line-height: 32px;
    text-align: center;
    border-radius:100%;
    margin:0 4px;
  }
  .pagination-box .paginate-next a{
    border:1px solid #af9673;
    border-radius:30px;
    line-height: 32px;
    width:60px;
    height:32px;
    font-size: 14px;
  }
  .pagination-box a:hover {
    background:rgba(175,150,115,.5);
    color:#ffffff;
  }
  .pagination-box a.active {
    background:#af9673;
    color:#ffffff;
  }
  .toPage {
    border:1px solid #af9673;
    border-radius:80px;
    width:50px;
    height:32px;
    text-indent: 0.5em;
    font-size: 14px;
  }
  .toPage:focus {
    outline: none;
  }
  input::-webkit-input-placeholder {
    text-align: center;
    color:#af9673 !important;
    opacity: 0.5;
    font-size: 12px;
  }
  :-moz-placeholder {
    text-align: center;
    color:#af9673 !important;
    opacity: 0.5;
    font-size: 14px;
  }
  ::-moz-placeholder {
    text-align: center;
    color:#af9673 !important;
    opacity: 0.5;
    font-size: 14px;
  }
  :-ms-input-placeholder {
    text-align: center;
    color:#af9673 !important;
    opacity: 0.5;
    font-size: 14px;
  }
  .spot-box {
    color:#af9673;
    letter-spacing:2px;
  }
</style>
