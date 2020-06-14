<template>
  <el-tree
    :data="menus"
    :props="defaultProps"
    node-key="catId"
    :expand-on-click-node="false"
    :show-checkbox="true"
    :default-expanded-keys="expandedKey"
  >
    <span class="custom-tree-node" slot-scope="{ node, data }">
      <span>{{ node.label }}</span>
      <span>
        <el-button
          v-if="data.catLevel <= 2"
          type="text"
          size="mini"
          @click="() => append(data)"
        >Append</el-button>
        <el-button
          v-if="node.childNodes.length == 0"
          type="text"
          size="mini"
          @click="() => remove(node, data)"
        >Delete</el-button>
      </span>
    </span>
  </el-tree>
</template>

<script>
export default {
  data() {
    return {
      expandedKey: [],
      menus: [],
      defaultProps: {
        children: "children",
        label: "name"
      }
    };
  },
  methods: {
    getMenus() {
      this.$http({
        url: this.$http.adornUrl("/product/category/list/tree"),
        method: "get"
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.menus = data.categoryList;
        } else {
        }
      });
    },
    append(data) {
      console.log("append", data);
    },
    //删除菜单
    remove(node, data) {
      var ids = [data.catId];
      this.$confirm(`此操作将永久删除【${data.name}】菜单, 是否继续?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/product/category/delete"),
            method: "post",
            data: this.$http.adornData(ids, false)
          }).then(({ data }) => {
            //刷新菜单
            this.getMenus();
            //展开父节点
            this.expandedKey=[node.parent.data.catId]
            this.$message({
              type: "success",
              message: "删除成功!"
            });
          });
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "已取消删除"
          });
        });
    }
  },
  created() {
    this.getMenus();
  }
};
</script>

<style>
</style>