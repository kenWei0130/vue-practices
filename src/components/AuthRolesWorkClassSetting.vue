<template>
  <div class="container setting text-start">
    <div class="row">
      <!-- title -->
      <h2>權限管理</h2>
    </div>
    <div class="row">
      <!-- 控制的資訊 -->
      <div class="col col-lg-6 col-6">
        <label class="form-label">帳號</label>
        <input
          type="text"
          class="form-control"
          value="1111111"
          disabled
          readonly
        />
      </div>
      <div class="col col-lg-6 col-6">
        <label class="form-label">角色</label>
        <select
          class="form-control"
          v-model="selectedRole"
          @change="changeRole"
        >
          <option v-for="item in select1" :key="item" :value="item.value">
            {{ item.text }}
          </option>
        </select>
      </div>
    </div>
    <!-- 權限內容 -->
    <div class="row">
      <div class="col border my-1 mx-2">
        <div class="">
          <label>權限</label>
          <div v-for="category in categories.children" :key="category">
            <input
              type="checkbox"
              :id="'chk_' + category.name"
              class="me-2"
              v-model="category.isChecked"
              @change="checkedCategory(category)"
            />
            <label class="col me-2">{{ category.name }}</label>
            <button type="button" class="btn btn-primary btn-sm m-1">
              管理區域
            </button>
            <div v-if="category.children?.length > 0" class="col">
              <div
                v-for="(child, pIndex) in category.children"
                :key="child"
                class="row"
              >
                <div class="col col-9 ms-2">
                  <label style="width: 3%">
                    <span v-if="pIndex == 0">└</span>
                    <span v-else>&nbsp;</span>
                  </label>
                  <input
                    type="checkbox"
                    id="chk_{{child.name}}"
                    class="mx-2"
                    v-model="child.isChecked"
                    @change="checkedchild(category)"
                  />
                  <span class="pl-2">{{ child.name }}</span>
                </div>
              </div>
              <div class="col col-9 ms-2">
                <label>
                  負責區域：
                  <span
                    v-for="(region, index) in category.regions"
                    :key="region"
                  >
                    <span>{{ region }}</span>
                    <span v-if="index + 1 < category.regions.length">, </span>
                  </span>
                </label>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- <div class="col border my-1 mx-2">
        <div class="col">
          <label>權限</label>
        </div>
        <tree-menu
          :name="categories.name"
          :children="categories.children"
          :regions="categories.regions"
          :depth="0"
          :downList="regionResponsible"
        ></tree-menu>
      </div> -->
    </div>
    <div class="row">
      <div class="col">
        <button class="btn btn-primary" @click="alertSucces()">確認</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AuthRolesWorkClassSetting",
  data: function () {
    return {
      selectedRole: "",
      select1: [
        { value: "", text: "" },
        { value: "1", text: "Admin" },
        { value: "2", text: "PM" },
        { value: "3", text: "工程審核人員" },
        { value: "4", text: "工程人員" },
        { value: "5", text: "User" },
      ],
      categories: {
        name: "",
        regions: [],
        children: [
          {
            name: "能源",
            isChecked: false,
            regions: ["新北市", "台北市-松山區"],
            children: [
              { name: "裝機", isChecked: false, regions: ["新北市"] },
              { name: "會勘", isChecked: false, regions: [] },
              { name: "維修", isChecked: false, regions: [] },
            ],
          },
          {
            name: "空品",
            isChecked: false,
            regions: [],
            children: [
              { name: "裝機", isChecked: false, regions: [] },
              { name: "會勘", isChecked: false, regions: [] },
              { name: "維修", isChecked: false, regions: [] },
            ],
          },
          {
            name: "報修",
            isChecked: false,
            regions: [],
            children: [
              { name: "查詢", isChecked: false, regions: [] },
              { name: "編輯", isChecked: false, regions: [] },
            ],
          },
          {
            name: "權限管理",
            isChecked: false,
            regions: [],
            children: [
              { name: "進度管理", isChecked: false, regions: [] },
              { name: "報表管理", isChecked: false, regions: [] },
            ],
          },
        ],
      },
      regionResponsible: [
        // { id: '', name: ''},
        { id: "台北市", name: "台北市" },
        { id: "台北市-中山區", name: "台北市-中山區" },
        { id: "台北市-松山區", name: "台北市-松山區" },
        { id: "台北市-中正區", name: "台北市-中正區" },
        { id: "新北市", name: "新北市" },
      ],
    };
  },
  methods: {
    alertSucces() {
      alert("更新角色權限完成！");
    },
    checkedCategory(selectedCategory) {
      if (selectedCategory) {
        selectedCategory.children?.forEach((child) => {
          child.isChecked = selectedCategory.isChecked;
        });
      }
    },
    checkedchild(parentCategory) {
      let checked = parentCategory.children?.find((child) => child.isChecked);
      if (checked) {
        parentCategory.isChecked = true;
      } else {
        parentCategory.isChecked = false;
      }
    },
    changeRole() {
      if (
        this.selectedRole &&
        this.selectedRole !== "" &&
        parseInt(this.selectedRole) > 0
      ) {
        this.categories.forEach((category) => {
          this.checkRole(category);
          if (category.children) {
            category.children.forEach((child) => {
              this.checkchildRole(child, category);
            });
          }
        });
      } else if (this.selectedRole === "") {
        this.init();
      }
    },
    checkRole(category) {
      let isChecked = true;
      if (this.selectedRole && parseInt(this.selectedRole) > 0) {
        if (category.role !== "" && category.role < this.selectedRole) {
          isChecked = false;
        }
      }
      category.isChecked = isChecked;
      return isChecked;
    },
    checkchildRole(child, category) {
      let isChecked = true;
      if (this.selectedRole && parseInt(this.selectedRole) > 0) {
        if (child.role === "") {
          isChecked = this.checkRole(category);
        } else if (child.role !== "" && child.role < this.selectedRole) {
          isChecked = false;
        }
      }
      child.isChecked = isChecked;
      if (isChecked) {
        category.isChecked = isChecked;
      }
      return isChecked;
    },
    init() {
      this.categories.forEach((category) => {
        category.isChecked = false;
        if (category.children) {
          category.children.forEach((child) => (child.isChecked = false));
        }
      });
    },
  },
};
</script>

<style>
.item {
  cursor: pointer;
}
</style>
