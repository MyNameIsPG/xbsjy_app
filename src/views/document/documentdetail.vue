<template>
  <a-el-dialog :visible.sync="visible" title="文件记录" :beforeClose="beforeClose">
    <div style="height: 600px;">
      <vtable
        style="width: 100%"
        ref="vTable"
        :dataArray="dataArray"
        :pageSize.sync="pageSize"
        :pageNum.sync="pageNum"
        :columns="columns"
        :total="total"
        :isOrder="true"
        :nodePage="false"
        v-loading="tableLoading"
        @getArticle="query"
      ></vtable>
    </div>
    <template slot="footer">
      <el-button type="default" size="small" @click="beforeClose">取消</el-button>
    </template>
  </a-el-dialog>
</template>

<script>
import Table from "../../components/table.vue";
import AElDialog from "../../components/Dialog/AElDialog.vue";
import { resDdcDocumentdetailQueryBydocumentId } from "../../api/ddc";
import { IS_OK, UPLOAD_FILE_DOWNLOAD } from "../../api/path";
export default {
  name: "Log",
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    uid: {
      type: String,
      default: ""
    }
  },
  components: {
    AElDialog,
    vtable: Table
  },
  data() {
    return {
      pageSize: 20, // 页数
      pageNum: 1, // 页码
      total: 0, // 总页数
      dataArray: [],
      columns: [
        {
          prop: "fileName",
          align: "center",
          label: "文件名称"
        },
        {
          prop: "createTime",
          align: "center",
          label: "更新时间"
        },
        {
          prop: "createUserId",
          align: "center",
          label: "更新人"
        },
        {
          prop: "description",
          align: "center",
          label: "检入描述"
        },
        {
          prop: "",
          align: "center",
          label: "操作",
          render: (h, param) => {
            return (
              <div>
                <el-button
                  type="text"
                  icon="el-icon-download"
                  onClick={this.download.bind(this, param.row.fileId)}
                >
                  下载
                </el-button>
              </div>
            );
          }
        }
      ],
      tableLoading: false
    };
  },
  created() {
    this.query();
  },
  methods: {
    async query() {
      this.tableLoading = true;
      if (this.uid) {
        const res = await resDdcDocumentdetailQueryBydocumentId(this.uid);
        if (res.data.code === IS_OK) {
          this.dataArray = res.data.data;
          this.tableLoading = false;
        }
      }
    },
    beforeClose() {
      this.$emit("update:visible", false);
    },
    download(fileId) {
      window.location.href = UPLOAD_FILE_DOWNLOAD + fileId;
      this.$message({
        type: "success",
        message: "下载成功"
      });
    }
  },
  watch: {
    uid() {
      this.query();
    }
  }
};
</script>
