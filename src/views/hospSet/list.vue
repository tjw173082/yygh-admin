<template>
  <div class="app-container">
    <el-form :inline="true" class="demo-form-inline">
       <el-form-item>
          <el-input  v-model="searchObj.hosname" placeholder="医院名称"/>
       </el-form-item>
       <el-form-item>
          <el-input v-model="searchObj.hoscode" placeholder="医院编号"/>
       </el-form-item>
       <el-button type="primary" icon="el-icon-search" @click="getList()">查询</el-button>
    </el-form>
    <!-- 工具条 -->
    <div>
       <el-button type="danger" size="mini" @click="batchRemoveHospSet()">批量删除</el-button>
    </div>
    <el-table :data="list" stripe style="width: 100%" @selection-change="handleSelectionChange">
        <el-table-column type="selection" width="55"></el-table-column>
        <el-table-column type="index" width="50" label="序号"/>
        <el-table-column prop="hosname" label="医院名称"/>
        <el-table-column prop="hoscode" label="医院编号"/>
        <el-table-column prop="apiUrl" label="api基础路径" width="200"/>
        <el-table-column prop="contactsName" label="联系人姓名"/>
        <el-table-column prop="contactsPhone" label="联系人手机"/>
        <el-table-column label="状态" width="80">
            <template slot-scope="scope">
                    {{ scope.row.status === 1 ? '可用' : '不可用' }}
            </template>
        </el-table-column>
        <el-table-column label="操作" width="280" align="center">
           <template slot-scope="scope">
              <el-button type="danger" size="mini" 
                 icon="el-icon-delete" @click="removeDataById(scope.row.id)"> </el-button>
           </template>
        </el-table-column>
    </el-table>
    <el-pagination
      :current-page="current"
      :page-size="limit"
      :total="total"
      style="padding: 30px 0; text-align: center;"
      layout="total, prev, pager, next, jumper"
      @current-change="getList"
    />
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
            list:[],
            total:0,
            multipleSelection: []
        }
    },
    created(){
        this.getList()
    },
    methods:{
        getList(page=1){
            this.current = page
            hospSet.getHospSetList(this.current,this.limit,this.searchObj)
            .then(response=>{
                console.log(response)
                this.list = response.data.records
                this.total = response.data.total
            })
            .catch(error=>{
                console.log(error)
            })
        },
        removeDataById(id){
            this.$confirm('此操作将永久删除医院是设置信息, 是否继续?','提示',{
                confirmButtonText:'确定',
                cancelButtonText:'取消',
                type:'warning'
            }).then(()=>{//确定执行then方法
                hospSet.deleteHospSet(id).then(response=>{
                    this.$message({
                        type:'success',
                        message:'删除成功'
                    })
                })
                this.getList(1)
            })
            
        },
        // 当表格复选框选项发生变化的时候触发
        handleSelectionChange(selection){
            this.multipleSelection = selection
        },
        batchRemoveHospSet(){
            this.$confirm("此操作将永久删除医院是设置信息, 是否继续?",'提示',{
                confirmButtonText:'确定',
                cancelButtonText:'取消',
                type:'warning'
            }).then(()=>{
                var idList = []
                //遍历数组得到每个id值，设置到idList里面
                for (let i = 0; i < this.multipleSelection.length; i++) {
                    const element = this.multipleSelection[i]
                    idList.push(element.id)
                    
                }
                hospSet.batchRemoveHospSet(idList).then(response=>{
                    this.$message({
                        type:'success',
                        message:'删除成功'
                    })
                    //刷新页面
                    this.getList(1)
                })
            })
        }
    }
}
</script>
