<template>
  <div>
    <el-table
      :data="cateList"
      border
      row-key="id"
      style="width: 100%"
      default-expand-all
      :tree-props="{ children: 'children' }"
    >
      <el-table-column prop="id" label="分类编号" width="180">
      </el-table-column>
      <el-table-column prop="catename" label="分类名称" width="180">
      </el-table-column>
      <el-table-column label="图片">
        <template slot-scope="item">
          <div v-if="item.row.pid!=0">
            <img  class="img"
              :src=" item.row.img ? $imgUrl + item.row.img
              :'http://i.shouchaobao.vip/d/file/202003/i5pjz34dpy1.jpg'" alt=""   />
          </div>
          <div v-else>

          </div>
        </template>
      </el-table-column>

      <el-table-column label="状态">
        <template slot-scope="item">
          <div>
            <el-tag v-if="item.row.status == 1" type="success">启用</el-tag>
            <el-tag v-else type="danger">禁用</el-tag>
          </div>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template slot-scope="item">
          <div>
            <el-button type="primary" @click="edit(item.row.id)" size="small"
              >编辑</el-button
            >
            <el-button type="danger" @click="del(item.row.id)" size="small"
              >删除</el-button
            >
          </div>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
// 调用辅助函数
import { mapActions, mapGetters } from "vuex";
import { getCateDelete } from "../../util/axios";
export default {
  data() {
    return {};
  },
  mounted() {
    //当列表渲染触发行动
    this.getCateList();
  },
  computed: {
    ...mapGetters({
      cateList: "cate/getCateList",
    }),
  },
  methods: {
    ...mapActions({
      getCateList: "cate/getCateListAction",
    }),
    // 封装编辑事件
    edit(id) {
      // 告诉父组件，要打开编辑弹框 ，并且传一个id
      this.$emit("edit", id);
    },
    // 封装删除事件
    del(id) {
      this.$confirm("确定删除该数据?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
        center: true,
      })
        .then(() => {
          // 调取删除接口
          getCateDelete({
            id,
          }).then((res) => {
            if (res.data.code === 200) {
              this.$message.success(res.data.msg);
              //  当列表删除成功触发刷新
              this.getCateList();
            } else {
              this.$message.error(res.data.msg);
            }
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除",
          });
        });
    },
  },
};
</script>

<style lang="stylus" scoped>
.img {
  width: 120px;
  height: 120px;
}
</style>
