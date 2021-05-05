## vue父组件数据更新子组件相关内容未改变
父组件
在父组件中，根据后台给的数据（数组），v-for生成子组件
```javascript
	<div class="exist">
            
             
    	<existProject :itemprop="item" v-for="(item, index) in getData" :key="index" :index="index" @click="sendIdTogetData(index)" v-show="true"></existProject>
            
	</div>
```
子组件（existProject）

```javascript
<template>
 <!-- <transition name="el-zoom-in-center"> -->
  <div class="existProjectBox" v-show="show2">
      <div class="existContentBox">
          <div class="existContent">
              <div class="existTitle">{{itemprop.title}}</div>
              <div class="stateBox">
                  <span class="state">{{ status_tit }}</span>
                  <span class="number" v-if="itemprop.status==2">收集份数：{{itemprop.asyncCount}}份</span>
              </div>
              <div class="tiemBox">
                  {{createtime}}
              </div>
          </div>
      </div>
      <div class="existButton">
        <li v-if="itemprop.status==0" @click="turnEdit(itemprop.qid)">
            <i class="icon icon-edit"></i>
            <span>编辑</span>
        </li>
        <li v-if="itemprop.status==0" @click="turnSend(itemprop.qid)">
            <i class="icon icon-send"></i>
            <span>发布</span>
        </li>
        <li v-if="itemprop.status==2 ">
            <i class="icon icon-data"></i>
            <span>数据</span>
        </li>
        <!-- <li v-if="itemprop.status==2 ">
            <i class="icon icon-data"></i>
            <span>暂停</span>
        </li>
        <li v-if="itemprop.status==2 ">
            <i class="icon icon-data"></i>
            <span>终止</span>
        </li> -->
        <li @click="delItem(itemprop.qid)">
            <i class="icon icon-other"></i>
            <span>删除</span>
        </li>
      </div>
  </div>
 <!-- </transition> -->
</template>

<script>
import axios from 'axios'
export default {
    data(){w
        return{
            createtime:'',
            status_tit:'',
            show2:true
        }
    },
    props:['itemprop'],
    methods:{
        turnEdit(id){
            debugger;
            console.log(id)
            axios.defaults.headers.common['token'] = JSON.parse(window.localStorage.getItem('token'))
            axios.get('/question/'+id)
            .then(response => {
                console.log(response);
                var obj = response.data.data;
                var contents = obj.contents;
                for(let i = 0; i < contents.length; i++){
                    obj.contents[i].component = this.$options.methods.initType(obj.contents[i].type)
                }
                console.log(obj)
                window.localStorage.setItem('workInfoList', JSON.stringify(obj));
                this.$router.push({
                    path: '/edit',
                    query: {
                        id: id
                    }
                })
                window.location.reload()
            })
            .catch(error => {
                console.log(error)
            })
        },
        turnSend(id){
            debugger;
            console.log(id)
            axios.defaults.headers.common['token'] = JSON.parse(window.localStorage.getItem('token'))
            axios.get('/question/'+id)
            .then(response => {
                console.log(response);
                var obj = response.data.data;
                console.log(obj)
                window.localStorage.setItem('workInfoList', JSON.stringify(obj));
                this.$router.push({
                    path: '/sendProject',
                    query: {
                        id: id
                    }
                })
                window.location.reload()
            })
            .catch(error => {
                console.log(error)
            })
        },
        delItem(id){
          this.$confirm('此操作将删除该文件进入草稿箱, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          })
          .then(() => {
            
            axios.defaults.headers.common['token'] = JSON.parse(window.localStorage.getItem('token'))
            axios.delete('/question/'+id)
              .then(response => {
                console.log(response)
                // this.show2 = false
                this.$parent.getPage();
              })
          })
          .catch(error => {
              console.log(error)
          })
        },
        initType(str){
          switch(str){
              case 1:return 'Radio';
              case 2:return 'check';
              case 3:return 'gapFill';
              case 4:return 'level';
              case 5:return 'photoInput';
              case 6:return 'Rate';
              case 7:return 'remark';
              case 8:return 'selectChoice';
              case 9:return 'sort';
              case 10:return 'tableNumber';
              case 11:return 'temp';
          }
        },
        
    },
    mounted(){
        // console.log(this.itemprop.createTime)
        var transformTime = new Date(this.itemprop.createTime)
        this.createtime = transformTime.toLocaleString();
        console.log(this.createtime)
    },

}
</script>

```
因为有多条数据，所以有分页处理，在第一页中数据显示正常，但是在获得第二页数据并赋值给父组件的data后，子组件的信息保留的还是第一页的信息

解决方法，使用watch深度监听

```javascript
    watch:{
        itemprop:{
            handler(n,o){ 
                console.log(this.itemprop);
                var status = this.itemprop.status;
                var showCondition = this.itemprop.showCondition;
                // debugger;
                this.status_tit = (function(status,showCondition) {
                    if(status==0) {
                        
                        return '未发布';
                    }
                    if(status==2 && showCondition==1)
                    {
                        // 已发布
                        return  '收集中';
                    }

                    if(status==2 &&showCondition==0)
                    {
                        // 暂停
                        return '已暂停';
                    }

                    if(status==2 &&showCondition==-1) {
                        // 终止
                        return '已终止';
                    }

                    if(status==2 &&showCondition==2) {
                        // 问卷发布结束
                        return '已结束';
                    }
                })(status,showCondition)
            },
            deep:true,
            immediate:true,
        }

    }
```
watch可以监听子组件的数据变化，数组或者对象要用深度监听，字符串什么的不用深度监听，这样就可以在分页切换数据后，就不会保留原有的信息，而是新的信息了
