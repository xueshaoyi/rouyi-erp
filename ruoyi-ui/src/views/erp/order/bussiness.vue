<template>
  <div class="app-container">
    <el-row :gutter="10" class="mb8">
      <el-col :span="1.5">
        <el-button
          type="primary"
          plain
          icon="el-icon-plus"
          size="mini"
          @click="handleAdd"
          v-hasPermi="['erp:order:add']"
        >新增</el-button>
      </el-col>
      <right-toolbar :showSearch.sync="showSearch" @queryTable="getList"></right-toolbar>
    </el-row>
    
    <el-table v-loading="loading" :data="orderList">
      <el-table-column type="selection" width="55" align="center" />
      <!-- <el-table-column label="销售订单ID" align="center" prop="id" /> -->
      <el-table-column label="订单名称" align="center" prop="orderName" />
      <!-- <el-table-column label="库存流水ID" align="center" prop="detailId" />
      <el-table-column label="开票信息ID" align="center" prop="taxInfoId" /> -->
      <el-table-column label="客户姓名" align="center" prop="clientName">
        <template slot-scope="scope">{{scope.row.clientInfo.clientNickname}}</template>
      </el-table-column>
      <el-table-column label="客户手机" align="center" prop="clientPhone">
        <template slot-scope="scope">{{scope.row.clientInfo.clientPhone}}</template>
      </el-table-column>
      <el-table-column label="送货地址" align="center" prop="shippingAddress" width="300">
        <template slot-scope="scope">{{scope.row.clientInfo.shippingAddress}}</template>
      </el-table-column>
      <el-table-column label="经手人姓名" align="center" prop="handlerName" />
      <el-table-column label="备注" align="center" prop="remark" />
      <el-table-column label="订单金额" align="center" prop="totalPrice" >
        <template slot-scope="scope">{{scope.row.totalPrice | toFixed(2) }}</template>
      </el-table-column>
      <el-table-column label="实付金额" align="center" prop="actualPayment" >
        <template slot-scope="scope">{{scope.row.actualPayment | toFixed(2) }}</template>
      </el-table-column>
      <!-- <el-table-column label="客户ID" align="center" prop="clientId" /> -->
      <el-table-column label="操作" align="center" class-name="small-padding fixed-width">
        <template slot-scope="scope">
          <el-button size="mini" type="text" @click="handleCheckInfoClick(scope.row)">查看</el-button>
          <!-- <el-button
            size="mini"
            type="text"
            icon="el-icon-edit"
            @click="handleUpdate(scope.row)"
            v-hasPermi="['erp:order:edit']"
          >修改</el-button>
          <el-button
            size="mini"
            type="text"
            icon="el-icon-delete"
            @click="handleDelete(scope.row)"
            v-hasPermi="['erp:order:remove']"
          >删除</el-button> -->
        </template>
      </el-table-column>
    </el-table>

    <order-dialog v-bind:visible="open" @close="cancel" :title="title" :order-id="orderID" :mode="mode"></order-dialog>
    
  </div>
</template>

<script>
import { listOrder, getOrder, delOrder, addOrder, updateOrder, exportOrder } from "@/api/erp/order";
import orderDialog from "./orderDialog.vue"
export default {
    name:"Bussiness",
    components: {
      orderDialog
    },

    data() {
      return {
        // 库存销售订单表格数据
        orderList: [],
        // 是否显示弹出层
        open: false,
        // 订单详情 dialog 模式 创建:0 查看:1
        mode: 0,
        // 订单ID
        orderID: 0
      }
    },

    created() {
      this.getList();
    },

    methods: {
      // 取消按钮
      cancel() {
        this.open = false;
        this.reset();
      },
      // 表单重置
      reset() {
        this.form = {
          id: null,
          detailId: null,
          createTime: null,
          taxInfoId: null,
          totalPrice: null,
          actualPayment: null,
          handlerName: null,
          clientId: null,
          remark: null
        };
        this.resetForm("form");
      },
      /** 查询库存销售订单列表 */
      getList() {
        this.loading = true;
        listOrder(this.queryParams).then(response => {
          this.orderList = response.rows;
          this.total = response.total;
          this.loading = false;
        });
      },
      /** 新增按钮操作 */
      handleAdd() {
        // this.reset();
        this.open = true;
        this.title = "添加库存销售订单";
        this.mode = 0
      },

      handleCheckInfoClick(e) {
        this.open = true;
        this.title = "订单详情";
        this.orderID = e.id
        this.mode = 1
      }
    }
}
</script>

<style>

</style>