<template>
  <div class="app-container">
    医院设置列表
  </div>
</template>
<script>
import hospSet from '@/api/hospSet'

export default{
    data(){
        return {
            current:1,
            limit:3,
            searchObj:{},
            list:[]
        }
    },
    created(){
        this.getList()
    },
    methods:{
        getList(){
            hospSet.getHospSetList(this.current,this.limit,this.searchObj)
            .then(Response=>{
                console.log(Response)
            })
            .catch(error=>{
                console.log(error)
            })
        }
    }
}
</script>
