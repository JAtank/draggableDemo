<template>
  <div id="app">
<!--    <div class="col-3" v-if="isLoad">-->
<!--      <h3>Draggable 1</h3>-->
<!--      <draggable-->
<!--        class="dragArea list-group"-->
<!--        :list="list1"-->
<!--        :group="{ name: 'people', pull: 'clone', put: false }"-->
<!--        :sort="false"-->
<!--        :filter="'.added'",-->
<!--        :clone="cloneList1">-->
<!--        <div class="list-group-item"-->
<!--             :class="[element.level==1?'level-1':(element.level==2?'level-2':'level-3'),element.added?'added':'']"-->
<!--             v-for="element in list1"-->
<!--             :key="element.c_id">-->
<!--          {{ element.name }}-->
<!--        </div>-->
<!--      </draggable>-->
<!--    </div>-->

<!--    <div class="col-3" v-if="isLoad">-->
<!--      <h3>Draggable 2</h3>-->
<!--      <draggable-->
<!--        class="dragArea list-group"-->
<!--        :list="list2"-->
<!--        group="people"-->
<!--        @start="onStartList2"-->
<!--        @change="list2Change">-->
<!--        <div class="list-group-item"-->
<!--             :class="[element.level==1?'level-1':(element.level==2?'level-2':'level-3')]"-->
<!--             v-for="element in list2"-->
<!--             :key="element.c_id">-->
<!--          {{ element.name }}-->
<!--        </div>-->
<!--      </draggable>-->
<!--    </div>-->
    <div class="select-content">
      <div class="left">
        <a class="item"
           :class="[item.level==1?'level-1':(item.level==2?'level-2':'level-3')]"
           v-for="item,index in list1"
           :key="item.c_id">
          <p :class="{'added':item.added}">{{item.name}}</p>
          <a class="add-logo" v-if="!item.added && type == 1" @click="addToList2(item,index)">+</a>
          <a class="add-logo" v-if="!item.added && type == 2 && item.level==1" @click="addType2ToList2(item,index)">+</a>
        </a>
      </div>
    </div>
    <div class="select-content">
      <div class="right">
        <a class="item"
           :class="[item.level==1?'level-1':(item.level==2?'level-2':'level-3')]"
           v-for="item,index in list2"
           :key="item.c_id+';'+index">
          <p>{{item.name}}</p>
          <a class="minus-logo"  v-if="type == 1" @click="cancleItem(item,index)">-</a>
          <a class="minus-logo" v-if="type == 2 && item.level==1" @click="cancleType2Item(item,index)">-</a>
        </a>
      </div>
    </div>
  </div>
</template>

<script>
  import draggable from 'vuedraggable';
  export default {
    name: 'app',
    data () {
      return {
        list1: [
          { name: "章节一", c_id: 1, c_p_id:0, type:0},
          { name: "1.1 学习一", c_id: 2, c_p_id:1, type:1},
          { name: "1.2 学习二", c_id: 3, c_p_id:1, type:1},
          { name: "1.3 学习三", c_id: 4, c_p_id:1, type:0},
          { name: "章节二", c_id: 5, c_p_id:0, type:0},
          { name: "2.1 复习一", c_id: 6, c_p_id:5, type:1},
          { name: "2.2 复习二", c_id: 7, c_p_id:5, type:0},
          { name: "2.2.1 复习二小一", c_id: 8, c_p_id:7, type:1},
          { name: "2.2.2 复习二小二", c_id: 9, c_p_id:7, type:1},
          { name: "章节三", c_id: 10, c_p_id:0, type:1},
        ],
        list2: [],
        isLoad:false,
        levelIndexList:[],
        list2LevelIndexList:[],
        // startList1:[],
        // currentList2:[],
        // currentList1Item:{},
        // moveList2:[],
        // moveNum:0
        type:2,//1:按节；2:按章
      }
    },
    created(){
      this.setItemLevel(this.list1);
    },
    components: {
      draggable
    },
    methods:{
      setItemLevel(list){
        if(!list.length){
          return;
        }else {
          let j;
          for(let i = 0;i<list.length;i++){
            this.$set(list[i],'added',false);
            // this.$set(list[i],'oldIndex',i);
            if(list[i].c_p_id==0){
              j = i;
              this.$set(list[i],'level',1);
              this.levelIndexList.push(i);
            }else if(i>0&&list[i].c_p_id == list[j].c_id&&list[j].c_p_id==0){
              this.$set(list[i],'level',2);
            }else {
              this.$set(list[i],'level',3);
            }
          }
          this.isLoad = true;
          // this.startList1 = this.startList1.concat(list);
        }
      },
      addToList2(item,index){
        if(this.list2.indexOf(item)!=-1){
          console.log("该条目录已经存在");
          return ;
        }
        if(item.level==1){ //一级目录
          this.list2.push(item);
          this.$set(item,'added',true);
        }else if(item.level==2){ //二级目录
          // let isHasLevel_1;
          // this.list2.forEach((list2Itme)=>{
          //   if(list2Itme.level==1){
          //     if(list2Itme.type==0){
          //       return isHasLevel_1 = true;
          //     }
          //   }
          // })
          let isHasLevel_1 = this.isHas(this.list2,1,0);
          if(isHasLevel_1){
            this.list2.push(item);
            this.$set(item,'added',true);
          }else {
            console.log("没有一级目录");
            let moveList = this.list1.slice(0,index);
            let moveItem = moveList.reverse().find((moveItem)=>{
              return moveItem.level==1;
            });
            this.list2.push(moveItem);
            this.list2.push(item);
            this.$set(moveItem,'added',true);
            this.$set(item,'added',true);
          }
        }else { //三级目录
          let moveList = this.list1.slice(0,index);
          let moveItem_1 = moveList.reverse().find((moveItem)=>{
            return moveItem.level==1;
          });
          let moveItem_2 = moveList.find((moveItem)=>{
            return moveItem.level ==2;
          });
          if(this.list2.length==0){
            this.list2.push(moveItem_1);
            this.list2.push(moveItem_2);
            this.list2.push(item);
            this.$set(moveItem_1,'added',true);
            this.$set(moveItem_2,'added',true);
            this.$set(item,'added',true);
            return;
          }
          let list_watch = this.list2.concat([]).reverse();
          if(list_watch[0].level ==1 && list_watch[0].type == 1){
            return;
          }
          // let isHasLevel_2 = false;
          // console.log(list_watch);
          // list_watch.forEach((watchItem)=>{
          //   if(watchItem.level == 2){
          //     if(watchItem.type == 0){
          //       return isHasLevel_2 = true;
          //     }
          //     return;
          //   }
          // });
          let isHasLevel_2 = this.isHas(list_watch,2,0);
          if(isHasLevel_2){
            this.list2.push(item);
            this.$set(item,'added',true);
          }else{
            console.log("2222");
            this.list2.push(moveItem_2);
            this.list2.push(item);
            this.$set(moveItem_2,'added',true);
            this.$set(item,'added',true);
            console.log("没有为节点的二级目录");
          }
        }
      },
      cancleItem(item,index){
        if(item.level==1){ //一级目录
          if(item.type==1){
            this.list2.splice(index,1);
            this.$set(this.lookItem(item.c_id),'added',false);
          }else{
            let ls_list = this.list2.slice(index+1);
            if(ls_list){
              let ls_item = ls_list.find((item)=>{
                return item.level==1;
              });
              if(!ls_item){
                let list = this.list2.slice(index);
                list.forEach((listItem)=>{
                  this.$set(this.lookItem(listItem.c_id),'added',false);
                  this.list2.splice(index,list.length);
                });
                return;
              }
              let ls_2_list = this.list2.slice(index,this.list2.indexOf(ls_item));
              ls_2_list.forEach((ls2Item)=>{
                this.$set(this.lookItem(ls2Item.c_id),'added',false);
              });
              this.list2.splice(index,ls_2_list.length);
            }else {
              this.$set(this.lookItem(item.c_id),'added',false);
              this.list2.splice(index,1);
            }
          }
        }else if(item.level==2){ //二级目录
          if(item.type==1){
            this.list2.splice(index,1);
            this.$set(this.lookItem(item.c_id),'added',false);
          }else {
            let ls_list = this.list2.slice(index+1);
            if(ls_list){
              let ls_item = ls_list.find((item)=>{
                return item.level==1||item.level==2;
              });
              if(!ls_item){
                let list = this.list2.slice(index);
                list.forEach((listItem)=>{
                  this.$set(this.lookItem(listItem.c_id),'added',false);
                  this.list2.splice(index,list.length);
                });
                return;
              }
              let ls_2_list = this.list2.slice(index,this.list2.indexOf(ls_item));
              ls_2_list.forEach((ls2Item)=>{
                this.$set(this.lookItem(ls2Item.c_id),'added',false);
              });
              this.list2.splice(index,ls_2_list.length);
            }else {
              this.$set(this.lookItem(item.c_id),'added',false);
              this.list2.splice(index,1);
            }
          }
        }else{ //三级目录
          this.list2.splice(index,1);
          this.$set(this.lookItem(item.c_id),'added',false);
        }
      },
      //类型2
      addType2ToList2(item,index){
       let currentIndex =  this.levelIndexList.indexOf(index);
       let moveList = [];
       if(currentIndex!=this.levelIndexList.length-1){ //不是最后一个
         let nextIndex = currentIndex+1;
         moveList = this.list1.slice(index,this.levelIndexList[nextIndex]);
         this.setAdded(this.list1,index,this.levelIndexList[nextIndex]);
       }else { //最后一个
         moveList = this.list1.slice(index);
         this.setAdded(this.list1,index,this.list1.length);
       }
       this.list2LevelIndexList.push(this.list2.length);
       this.list2 = this.list2.concat(moveList);
      },
      //类型2
      cancleType2Item(item,index){
        let currentIndex =  this.list2LevelIndexList.indexOf(index);
        if(currentIndex!=this.list2LevelIndexList.length-1){ //不是最后一个
          let nextIndex = currentIndex+1;
          this.list2.splice(index,this.list2LevelIndexList[nextIndex]-index);
          let num = this.list2LevelIndexList[nextIndex]-this.list2LevelIndexList[currentIndex];
          for (let i=nextIndex;i<this.list2LevelIndexList.length;i++){
            let newIndex = this.list2LevelIndexList[i]- num;
            this.$set(this.list2LevelIndexList,i,newIndex);
          }
        }else { //最后一个
          console.log(index+";"+this.list2.length-index);
          this.list2.splice(index,this.list2.length-index);
          // this.setAdded(this.list1,index,this.list1.length);
        }
        this.list2LevelIndexList.splice(currentIndex,1);
        console.log(this.list2LevelIndexList);
      },
      lookItem(c_id){
       let item =  this.list1.find((item)=>{
          return item.c_id == c_id;
        })
       return item;
      },
      isHas(list,level,type){
        for(let i=0;i<list.length;i++){
          if(list[i].level == level){
            if(list[i].type == type){
              return true;
            }
            return false;
          }
        }
      },
      setAdded(list,startIndex,endIndex){
        for (let i=startIndex;i<endIndex;i++){
          this.$set(list[i],"added",true);
        };
      }
      // cloneList1(item){
      //   if(!this.list2.length&&item.level!=1){
      //     console.log("目录结构错误");
      //     return;
      //   }else if(this.list2.indexOf(item)==-1){
      //     this.currentList2 = this.list2.concat([]);
      //     this.currentList1Item = item;
      //     return item;
      //   }
      //   console.log("本课时已存在！！");
      // },
      // onStartList2(item){
      //   this.currentList2 = this.list2.concat([]);
      //   let index = item.oldIndex;
      //   if(index>=0){
      //     let obj = this.list2[index];
      //     if(obj.level==1){
      //       for(let i = index+1;i<this.list2.length;i++){
      //         if(this.list2[i].level==1){
      //           // this.moveList2 = this.list2.slice(index,i);
      //           // this.moveList2Start = this.list2.slice(0,index-1);
      //           // this.moveList2End = this.list2.slice(i+1,this.list2.length-1);
      //           // console.log(this.moveList2);
      //           console.log(i+"==========");
      //           this.moveNum = i-index+1;
      //           return;
      //         }
      //       }
      //     }else if(obj.level==2){
      //       for(let i = index+1;i<this.list2.length;i++){
      //         if(this.list2[i].level==1||this.list2[i].level==2){
      //           // this.moveList2 = this.list2.slice(index,i);
      //           // this.moveList2Start = this.list2.slice(0,index-1);
      //           // this.moveList2End = this.list2.slice(i+1,this.list2.length-1);
      //           // console.log(this.moveList2);
      //           console.log(i+"***************");
      //           this.moveNum = i-index+1;
      //           return;
      //         }
      //       }
      //     }
      //   }
      // },
      // list2Change(obj){
      //   let objItem;
      //   if(obj.added){
      //     objItem = obj.added;
      //     if(objItem.element.level && this.list2.length>1){
      //       if(objItem.element.level == 1 && objItem.newIndex < this.list2.length-1
      //         && this.list2[objItem.newIndex+1].level == 3){
      //         this.list2 = this.currentList2.concat([]);
      //         this.currentList1Item={};
      //       }else if(objItem.element.level == 3 && this.list2[objItem.newIndex-1].level == 1){
      //         this.list2 = this.currentList2.concat([]);
      //         this.currentList1Item={};
      //       }
      //     }
      //     this.$set(this.currentList1Item,'added',true);
      //   }else if(obj.moved){
      //     objItem = obj.moved;
      //     if(objItem.element.level!=1 && objItem.newIndex == 0){
      //       this.list2 = this.currentList2.concat([]);
      //     }else if(objItem.element.level == 3 && this.list2[objItem.newIndex-1].level == 1){
      //       this.list2 = this.currentList2.concat([]);
      //     }else if(objItem.element.level == 1 && objItem.newIndex < this.list2.length-1
      //       && this.list2[objItem.newIndex+1].level == 3){
      //       this.list2 = this.currentList2.concat([]);
      //     }
      //     if(objItem.element.level == 1){
      //       // this.list2 =this.moveList2Start.concat(this.moveList2).concat(this.moveList2End);
      //       let arr1 = this.currentList2.slice(0,objItem.oldIndex);
      //
      //       let arr2 = this.currentList2.slice(objItem.oldIndex,objItem.oldIndex+this.moveNum);
      //
      //       let arr3 = this.currentList2.slice(objItem.oldIndex+this.moveNum);
      //
      //     }else if(objItem.element.level == 2){
      //       // this.list2 =this.moveList2Start.concat(this.moveList2).concat(this.moveList2End);
      //       let arr1 = this.currentList2.slice(0,objItem.oldIndex);
      //       let arr2 = this.currentList2.slice(objItem.oldIndex,objItem.oldIndex+this.moveNum);
      //       let arr3 = this.currentList2.slice(objItem.oldIndex+this.moveNum);
      //       this.list2 = arr1.concat(arr2).concat(arr3);
      //     }
      //   }
      // }
    },
  }
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
#app {
  position: fixed;
  display: block;
  width: 100%;
  height: 100%;
  left: 0;
  top: 0;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.select-content{
    display: inline-block;
    width: 45%;
    height: 90%;
    overflow-x: hidden;
    overflow-y: auto;
    outline: #2c3e50 2px solid;
    box-sizing: border-box;
    padding: 0 2%;
  &:last-child{
    margin-left: 3%;
  }
  .left{
    display: inline-block;
    width: 100%;
    height: 100%;
  }
  .right{
    display: inline-block;
    width: 100%;
    height: 100%;
  }
  .item{
    position: relative;
    display: flex;
    align-items: center;
    width: 100%;
    height: 5%;
    border-bottom: 1px #2c3e50 solid;
    box-sizing: border-box;
    &.level-1{
      padding-left: 2%;
    }
    &.level-2{
      padding-left: 4%;
    }
    &.level-3{
      padding-left: 7%;
    }
  }
  .add-logo{
    position: absolute;
    font-size: 26px;
    right: 20px;
  }
  .added{
    color: #999;
  }
  .minus-logo{
    position: absolute;
    font-size: 26px;
    right: 20px;
  }
}
/*.col-3{*/
/*  display: inline-block;*/
/*  width: 45%;*/
/*  height: 90%;*/
/*  overflow-x: hidden;*/
/*  overflow-y: auto;*/
/*  outline: #2c3e50 2px solid;*/
/*  &:last-child{margin-left: 3%;}*/
/*  .list-group{*/
/*    height: 100%;*/
/*    padding:0 3%;*/
/*  }*/
/*  .list-group-item{*/
/*    height: 5%;*/
/*    border-bottom: 1px #2c3e50 solid;*/
/*    display: flex;*/
/*    align-items: center;*/
/*    &.level-1{*/
/*      padding-left: 2%;*/
/*    }*/
/*    &.level-2{*/
/*      padding-left: 4%;*/
/*    }*/
/*    &.level-3{*/
/*      padding-left: 7%;*/
/*    }*/
/*  }*/
/*  .added{*/
/*    color: #999;*/
/*  }*/
/*}*/
</style>
