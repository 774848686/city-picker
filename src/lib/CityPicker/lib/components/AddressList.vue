<template>
  <div>
    <el-form>
      <el-form-item label-width="150px">
        <el-input
          v-model="keywords"
          size="mini"
          placeholder="请输入城市名"
          style="width: 150px"
        ></el-input>
        <el-button
          type="primary"
          size="mini"
          @click="searchCity"
        >查询</el-button>
        <el-button
          type="primary"
          size="mini"
          @click="checkAll"
          style="marginLeft: 0;"
        >全选</el-button>
        <el-button
          type="primary"
          size="mini"
          @click="checkNone"
          style="marginLeft: 0;"
        >全不选</el-button>
      </el-form-item>
      <el-form-item
        label-width="150px"
        label=" "
        class="postInfo-container-item"
      >
        <div
          class="items"
          style="width: 1000px"
        >
          <div
            class="item"
            :key="index"
            v-for="(item,index) in list"
          >
            <span class="item-area">{{item.reginos}}</span>
            <ul class="item-provinces">
              <li
                v-for="(itemSub,subIndex) in item.provinces"
                :key="subIndex"
              >
                <address-city
                  :itemData="itemSub"
                  :provinceCode="itemSub.provinceCode"
                  :selectData="selectDataFormat"
                  :highlightProvince="highlightProvince"
                  :highlightCity="highlightCity"
                  :importData="importData"
                  @newEvent="parentLisen"
                ></address-city>
              </li>
            </ul>
          </div>
        </div>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
import { provinceCity } from "../../assets/provinceCity";
import AddressCity from "./AddressCity";
/**
 * 区域选择
 */
export default {
  data() {
    return {
      list: [],
      listLoading: false,
      checkData: [],
      keywords: "",
      highlightProvince: "",
      highlightCity: "",
      importData: [],
      selectDataFormat: []
    };
  },
  props: ["selectData", "isEdit"],
  components: {
    AddressCity
  },
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: "success",
        draft: "gray",
        deleted: "danger"
      };
      return statusMap[status];
    }
  },
  created() {
    if (this.selectData) {
      this.selectDataFormat = this.selectData.map(item => {
        return item;
      });
    } else {
      this.selectDataFormat = this.selectData;
    }
    this.list = provinceCity();
  },
  methods: {
    parentLisen(val, code, flag) {
      // 临时添加
      let [...tmplArr] = val;
      this.highlightProvince = "";
      this.highlightCity = "";
      if (flag) {
        tmplArr.push(code);
      }
      let temp = this.checkData.find(e => e.code == code);
      let index = this.checkData.indexOf(temp);
      if (temp) {
        this.checkData[index].data = tmplArr;
      } else {
        this.checkData.push({ code: code, data: tmplArr });
      }
    },
    searchCity() {
      this.highlightProvince = "";
      this.highlightCity = "";
      if (!this.keywords) {
        this.$message.warning("请输入省/市名");
        return;
      }
      if (this.keywords.length < 2) {
        this.$message.warning("请输入至少两个字");
        return;
      }
      loop: for (let i = 0; i < this.list.length; i++) {
        const area = this.list[i];
        for (let j = 0; j < area.provinces.length; j++) {
          const province = area.provinces[j];
          if (province.provinceName.indexOf(this.keywords) > -1) {
            this.highlightProvince = province.provinceCode;
            break loop;
          } else {
            for (let k = 0; k < province.city.length; k++) {
              const city = province.city[k];
              if (city.cityName.indexOf(this.keywords) > -1) {
                this.highlightProvince = province.provinceCode;
                this.highlightCity = city.cityCode;
                break loop;
              }
            }
          }
        }
      }
      if (!this.highlightProvince && !this.highlightCity) {
        this.$message.error("您输入的省/市名有误！");
      }
    },
    openFile() {
      this.$refs.exportFile.click();
    },
    getCitiesCode(array) {
      if (array.length == 0) return;
      const cities = {};
      this.list.forEach(area => {
        area.provinces.forEach(province => {
          const name = province.provinceName;
          if (
            name == "北京市" ||
            name == "天津市" ||
            name == "上海市" ||
            name == "重庆市"
          ) {
            cities[province.provinceName] = {};
            province.city.forEach(city => {
              cities[province.provinceName][city.cityName] = city.cityCode;
            });
          } else {
            province.city.forEach(city => {
              cities[city.cityName] = city.cityCode;
            });
          }
        });
      });
      const obj = {},
        returnValue = [];
      array.forEach(x => {
        if (
          x.city == "北京市" ||
          x.city == "天津市" ||
          x.city == "上海市" ||
          x.city == "重庆市"
        ) {
          for (var key in cities[x.city]) {
            obj[cities[x.city][key]] = true;
          }
        } else {
          if (cities[x.city]) {
            obj[cities[x.city]] = true;
          }
        }
      });
      this.checkData.forEach(x => {
        x.data.forEach(y => {
          obj[y] = true;
        });
      });
      for (var key in obj) {
        returnValue.push(key);
      }
      return returnValue || [];
    },
    /*
     * 选择全部区域
     */
    checkAll() {
      const array = [];
      this.list.forEach(area => {
        area.provinces.forEach(province => {
          province.city.forEach(city => {
            array.push(city.cityCode);
          });
        });
      });
      this.importData = array;
    },
    /**
     * 全不选
     */
    checkNone() {
      this.importData = [];
    }
  }
};
</script>
<style rel="stylesheet/scss" lang="scss" scoped>
.items {
  border: 1px solid #e6e6e6;
  border-bottom: none;

  .item {
    height: 41px;
    border-bottom: 1px solid #e6e6e6;
    padding-left: 15px;
  }

  .item-area {
    float: left;
    padding-right: 10px;
    min-width: 80px;
    text-align: right;
  }

  .item-provinces {
    float: left;

    li {
      float: left;
      min-width: 120px;
      padding-left: 10px;
      position: relative;
    }

    .item-citys {
      position: absolute;
      top: -1px;
      left: 80px;
      background: #fff;
      z-index: 99;
      border: 1px solid #e6e6e6;
      width: 300px;
    }
  }
  ul,
  li {
    margin: 0;
    padding: 0;
    list-style: none;
  }
}
</style>