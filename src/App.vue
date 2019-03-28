<template>
  <div id="app">
    <div class="col-3" v-if="isLoad">
      <h3>Draggable 1</h3>
      <draggable
        class="dragArea list-group"
        :list="list1"
        :group="{ name: 'people', pull: 'clone', put: false }"
        :clone="cloneList1"
        @start="onStart"
        @end="onEnd"
        @change="list1Change">
        <div class="list-group-item"
             :class="[element.level==1?'level-1':(element.level==2?'level-2':'level-3')]"
             v-for="element in list1"
             :key="element.c_id">
          {{ element.name }}
        </div>
      </draggable>
    </div>

    <div class="col-3" v-if="isLoad">
      <h3>Draggable 2</h3>
      <draggable
        class="dragArea list-group"
        :list="list2"
        group="people"
        @change="list2Change">
        <div class="list-group-item"
             :class="[element.level==1?'level-1':(element.level==2?'level-2':'level-3')]"
             v-for="element in list2"
             :key="element.c_id">
          {{ element.name }}
        </div>
      </draggable>
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
          { name: "1.3 学习三", c_id: 4, c_p_id:1, type:1},
          { name: "章节二", c_id: 5, c_p_id:0, type:0},
          { name: "2.1 复习一", c_id: 6, c_p_id:5, type:1},
          { name: "2.2 复习二", c_id: 7, c_p_id:5, type:0},
          { name: "2.2.1 复习二小一", c_id: 8, c_p_id:7, type:1},
          { name: "2.2.2 复习二小二", c_id: 9, c_p_id:7, type:1},
          { name: "章节三", c_id: 10, c_p_id:0, type:1},
        ],
        list2: [],
        isLoad:false,
        startList1:[],
        currentList2:[]
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
            if(list[i].c_p_id==0){
              j = i;
              this.$set(list[i],'level',1);
            }else if(i>0&&list[i].c_p_id == list[j].c_id&&list[j].c_p_id==0){
              this.$set(list[i],'level',2);
            }else {
              this.$set(list[i],'level',3);
            }
          }
          this.isLoad = true;
          this.startList1 = this.startList1.concat(list);
        }
      },
      cloneList1(item){
        if(!this.list2.length&&item.level!=1){
          console.log("目录结构错误");
          return;
        }else if(this.list2.indexOf(item)==-1){
          this.currentList2 = this.list2.concat([]);
          return item;
        }
        console.log("本课时已存在！！");
      },
      onStart(){
        // this.currentList2 = this.list2;
        // console.log("开始");
        // console.log(a);
      },
      onEnd(){
        // console.log()
        // if(this.list2 == this.currentList2){
        //   console.log("同一个列")
        //   return;
        // }
      },
      list1Change(obj){
        console.log(obj);
        if(obj.newIndex != obj.oldIndex){
          this.list1 = this.startList1.concat([]);
        }
      },
      list2Change(obj){
        let objItem = obj.added;
        console.log(objItem);
        if(objItem.element.level && this.list2.length>1){
          if(objItem.element.level==1 && this.list2[objItem.newIndex+1].level==3){
            this.list2 = this.currentList2.concat([]);
          }else if(objItem.element.level == 3 && this.list2[objItem.newIndex-1].level == 1){
            this.list2 = this.currentList2.concat([]);
          }
        }
      }
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
.col-3{
  display: inline-block;
  width: 45%;
  height: 90%;
  overflow-x: hidden;
  overflow-y: auto;
  outline: #2c3e50 2px solid;
  &:last-child{margin-left: 3%;}
  .list-group{
    height: 100%;
    padding:0 3%;
  }
  .list-group-item{
    height: 5%;
    border-bottom: 1px #2c3e50 solid;
    display: flex;
    align-items: center;
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
}
</style>
