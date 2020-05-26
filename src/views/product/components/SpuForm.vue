<template>
  <el-card>
    <el-form
      :label-position="labelPosition"
      label-width="80px"
      v-show="visible"
    >
      <el-form-item label="SPU名称">
        <el-input v-model="spuInfo.spuName"></el-input>
      </el-form-item>
      <el-form-item label="品牌">
        <el-select v-model="spuInfo.tmId">
          <el-option
            :label="tm.tmName"
            :value="tm.id"
            v-for="tm in trademarkList"
            :key="tm.id"
          ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="SPU描述">
        <el-input
          type="textarea"
          rows="4"
          v-model="spuInfo.description"
        ></el-input>
      </el-form-item>

      <el-form-item label="SPU图片">
        <el-upload
          action="https://jsonplaceholder.typicode.com/posts/"
          list-type="picture-card"
          :on-preview="handlePictureCardPreview"
          :on-remove="handleRemove"
          :file-list="spuImageList"
        >
          <i class="el-icon-plus"></i>
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl" alt="" />
        </el-dialog>
      </el-form-item>

      <el-form-item label="销售属性">
        <el-select value="">
          <el-option
            :label="attr.name"
            :value="attr.id"
            v-for="attr in saleAttrList"
            :key="attr.id"
          ></el-option>
        </el-select>
        <el-button type="primary" icon="el-icon-plus">添加销售属性</el-button>

        <el-table border style="margin:20px 0">
          <el-table-column label="序号" type="index" width="80" align="center">
          </el-table-column>
          <el-table-column label="属性名"> </el-table-column>
          <el-table-column label="属性值名称"> </el-table-column>
          <el-table-column label="操作">
            <template slot-scope="{ row, $index }">
              <HintButton
                title="删除"
                type="danger"
                icon="el-icon-delete"
                size="mini"
              ></HintButton>
            </template>
          </el-table-column>
        </el-table>

        <el-button type="primary">保存</el-button>
        <el-button @click="back">返回</el-button>
      </el-form-item>
    </el-form>
  </el-card>
</template>

<script>
export default {
  name: "SpuForm",

  props: {
    visible: Boolean
  },

  data() {
    return {
      labelPosition: "right", //表单文字右对齐
      dialogImageUrl: "", //上传照片大图显示地址
      dialogVisible: false,

      spuId: "",
      //spu详情
      spuInfo: {
        spuName: "", //spu名字
        description: "", //spu描述
        tmId: null, //品牌id
        spuSaleAttrList: [], //销售属性列表
        spuImageList: [] //spu图片列表
      },

      spuImageList: [], //spu图片列表
      saleAttrList: [], //销售属性列表
      trademarkList: [] //所有品牌列表
    };
  },

  methods: {
    //图片删除后回调
    handleRemove(file, fileList) {
      console.log(file, fileList);
    },
    //显示大图回调
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },

    //修改界面初始数据获取
    initLoadUpdateData(id) {
      this.spuId = id;
      this.getSpuInfo();
      this.getSpuImageList();
      this.getTrademarkList();
      this.getSaleList();
    },

    //获取指定id的SPU信息
    async getSpuInfo() {
      const result = await this.$API.spu.get(this.spuId);
      if (result.code === 200) {
        this.spuInfo = result.data;
      }
    },

    //获取指定SPU的id对应的图片列表
    async getSpuImageList() {
      const result = await this.$API.sku.getSpuImageList(this.spuId);
      if (result.code === 200) {
        const spuImageList = result.data;
        //整理显示图片需要的数据
        spuImageList.forEach(item => {
          item.name = item.imgName;
          item.url = item.imgUrl;
        });

        this.spuImageList = spuImageList;
      }
    },

    //获取所有品牌的列表
    async getTrademarkList() {
      const result = await this.$API.trademark.getList();
      if (result.code === 200) {
        this.trademarkList = result.data;
      }
    },

    //获取所有销售属性列表
    async getSaleList() {
      const result = await this.$API.spu.getSaleList();
      if (result.code === 200) {
        this.saleAttrList = result.data;
      }
    },

    //点击返回
    back() {
      this.$emit("update:visible", false);
    }
  }
};
</script>

<style lang="less" scoped></style>
