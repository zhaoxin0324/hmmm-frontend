<template>
  <div class="dashboard-container">
    <div class="app-container">题库-精选</div>
    <el-card>
      <el-button type="primary">新增试题</el-button>
      <el-button type="primary">批量引进</el-button>
      <el-divider></el-divider>
      <!-- 表单部分 -->
      <el-form inline style="margin-left:50px;width:100%;max-width:100%;overflow:hidden">
        <el-form-item label="学科">
          <el-select v-model="searchData.subjectID" placeholder="请选择">
            <el-option
              v-for="item in subjectList"
              :key="item.id"
              :value="item.id"
              :label="item.subjectName"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="难度" style="margin-left:20px">
          <el-select v-model="searchData.difficulty" placeholder="请选择">
            <el-option
              v-for="item in difficulty"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="试题类型">
          <el-select v-model="searchData.questionType" placeholder="请选择">
            <el-option
              v-for="item in questionType"
              :key="item.value"
              :value="item.value"
              :label="item.label"
            ></el-option>
          </el-select>
        </el-form-item>
        <!-- 第二行 -->
        <el-form-item label="标签">
          <el-select v-model="searchData.city" placeholder="请选择">
            <el-option v-for="item in artList" :key="item.id" :value="item.id" :label="item.tags"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="城市" style="margin-left:20px">
          <el-select @change="changeArea" v-model="searchData.province" placeholder="请选择">
            <el-option
              v-for="(item,index) in provinceList"
              :key="index"
              :value="item"
              :label="item"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="区域" style="margin-left:30px">
          <el-select v-model="value1" placeholder="请选择">
            <el-option v-for="(item,index) in cityList" :key="index" :value="item" :label="item"></el-option>
          </el-select>
        </el-form-item>
        <!-- 第三行 -->
        <el-form-item label="关键字" style="margin-left:-10px">
          <el-input></el-input>
          <!-- <el-select v-model="value1" multiple placeholder="请选择"></el-select> -->
        </el-form-item>
        <el-form-item label="译文备注">
          <el-input></el-input>
          <!-- <el-select v-model="value1" multiple placeholder="请选择"></el-select> -->
        </el-form-item>
        <el-form-item label="企业简称">
          <!-- <el-select v-model="value1" multiple placeholder="请选择"></el-select> -->
          <el-input></el-input>
        </el-form-item>
        <!-- 第 四行 -->
        <el-form-item label="方向">
          <el-select v-model="searchData.direction" placeholder="请选择">
            <el-option v-for="(item,index) in direction" :key="index" :value="index" :label="item"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="录入人" style="margin-left:20px">
          <el-select v-model="searchData.creator" placeholder="请选择">
            <el-option
              v-for="item in artList"
              :key="item.id"
              :value="item.id"
              :label="item.creator"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="二级目录">
          <el-select v-model="searchData.tags" placeholder="请选择">
            <el-option
              v-for="item in artList"
              :key="item.id"
              :value="item.id"
              :label="item.catalogID"
            ></el-option>
          </el-select>
        </el-form-item>
        <!-- 搜寻部分 -->
        <el-row type="flex" justify="center">
          <el-button>清除</el-button>
          <el-button type="primary">搜寻</el-button>
        </el-row>
      </el-form>
      <!-- 状态部分 -->
      <el-radio-group v-model="status" style="margin-bottom: 30px;">
        <el-radio-button label="全部">全部</el-radio-button>
        <el-radio-button label="待发布">待发布</el-radio-button>
        <el-radio-button label="已发布">已发布</el-radio-button>
      </el-radio-group>
      <!-- 文章列表部分 -->
      <el-table style="width: 100%" :data="artList">
        <el-table-column label="序号" prop="id"></el-table-column>
        <el-table-column label="试题编号" prop="number"></el-table-column>
        <el-table-column label="学科" prop="subjectID" :formatter="subjects"></el-table-column>
        <el-table-column label="题型" prop="questionType" :formatter="questionTypes"></el-table-column>
        <el-table-column label="题干"></el-table-column>
        <el-table-column label="录入时间" prop="addDate">
          <template slot-scope="obj">{{obj.row.addDate | parseTimeByString}}</template>
        </el-table-column>
        <el-table-column label="录入人" prop="creator"></el-table-column>
        <el-table-column label="艰难" prop="difficulty" :formatter="fomatDifficulty"></el-table-column>
        <el-table-column label="审核状态" prop="chkState" :formatter="checkState"></el-table-column>
        <el-table-column label="审核意见"></el-table-column>
        <el-table-column label="审核人"></el-table-column>
        <el-table-column label="发布状态" prop="publishState" :formatter="publishStates"></el-table-column>
        <el-table-column label="操作">
          <template slot-scope="obj">
            <el-button type="text" style="margin-left:10px">审核</el-button>
            <el-button type="text">预览</el-button>
            <el-button type="text">上架</el-button>
            <el-button type="text" @click="amend(obj.row.id)">修改</el-button>
            <el-button type="text" @click="deleteItem(obj.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页部分 -->
      <el-row type="flex" justify="center" style="margin:20px 0">
        <el-pagination
          background
          layout="prev, pager, next"
          :total="page.total"
          :current-page="page.currentPage"
          :page-size="page.pageSize"
          @current-change="pageChange"
        ></el-pagination>
      </el-row>
    </el-card>
  </div>
</template>
<script>
import { choice } from "../../api/hmmm/questions";
import {
  difficulty,
  chkType,
  publishType,
  questionType,
  subject,
  direction
} from "../../api/hmmm/constants";
import { list } from "../pages/tags";
import { remove } from "../../api/hmmm/questions";
import { provinces, citys } from "../../api/hmmm/citys";
import { list as articleList, detail } from "../../api/hmmm/subjects";
export default {
  name: "QuestionsChoice",
  data() {
    return {
      status: "全部",
      provinceList: [],
      subjectList: [],
      cityList: [],
      value: "",
      value1: "",
      page: {
        total: 0,
        currentPage: 1,
        pageSize: 10
      },
      searchData: {
        subjectID: "",
        difficulty: "",
        questionType: "",
        tags: "",
        province: "",
        city: "",
        keyword: "",
        remarks: "",
        shortName: "",
        direction: "",
        creatorID: "",
        catalogId: "",
        chkState: "",
        publishState: "",
        creator: ""
      },
      difficulty,
      chkType,
      publishType,
      questionType,
      subject,
      provinces,
      direction,
      // citys,
      artList: [],
      value1: ""
    };
  },
  methods: {
    //删除文章
    deleteItem(artId) {
      this.$confirm("你确定要删除该文章吗？").then(() => {
        remove({ id: artId }).then(() => {
          this.getChoiceData();
        });
      });
    },
    // 修改文章
    amend(id) {
      this.$router.push({ path: "/questions/new", query: { id } });
    },
    // 改变区域事件
    changeArea(item) {
      console.log(item);
      // let pname=this.provinceList[item]
      // console.log(item);
      //  console.log( citys(item));
      debugger;
      this.cityList = citys(item);
    },
    // 分页触发事件
    pageChange(val) {
      alert(val);
    },
    //格式化管理员
    formatConservator(row, column, cellValue, index) {
      let res = this.conservator.filter(item => item.value == cellValue);
      return res.length ? res[0].label : "未知";
    },
    // 格式化学科
    subjects(row, column, cellValue, index) {
      let res = this.subject.filter(item => item.value == cellValue);
      return res.length ? res[0].label : "未知";
    },
    // 格式化难度
    fomatDifficulty(row, column, cellValue, index) {
      let res = this.difficulty.filter(item => item.value == cellValue);
      return res.length ? res[0].label : "未知";
    },
    // 格式化审核状态
    checkState(row, column, cellValue, index) {
      let result = this.chkType.filter(item => {
        return item.value == cellValue;
      });
      return result.length ? result[0].label : "未知";
    },
    // 格式化发布状态
    publishStates(row, column, cellValue, index) {
      let result = this.publishType.filter(item => {
        return item.value == cellValue;
      });
      return result.length ? result[0].label : "未知";
    },
    // 格式化题型
    questionTypes(row, column, cellValue, index) {
      let result = this.questionType.filter(item => {
        return item.value == cellValue;
      });
      // console.log(result);
      return result.length ? result[0].label : "-";
    },
    // 获取精选题目数据
    async getChoiceData() {
      //  alert(1)
      let result = await choice();
      (this.page.total = result.data.counts),
        (this.page.currentPage = result.data.page),
        (this.page.pageSize = result.data.pagesize);
      this.artList = result.data.items;
      console.log(this.artList);
    }
  },
  created() {
    // 调用精选题库数据
    this.getChoiceData();
    // this.fomatDifficulty()
    // 文章列表
    articleList().then(res => {
      this.subjectList = res.data.items;
    });
    // console.log(citys());
    console.log(provinces());
    this.provinceList = provinces();

    // list({}).then(res=>console.log(res))
    // //  console.log();

    //  this.cityList=citys()
  }
};
</script>

<style scoped lang="scss">
// 1. 如果在此处 scoped 当前组件下生效样式
// 2. 在style中的所有选择器 编译的时候会自动带上属性选择器
// 3. .box{} ===> .box[data-v-2c9827a4]{} 交集选择器
// 4. 在当前组件下暴露的标签都会加上 data-v-2c9827a4 属性
// 5. 但是如果是组件，其他组件内部的标签是不会加上这个属性 意味写组件内部的样式是不会生效的
// 6. 其他组件的样式都不会生效
</style>
