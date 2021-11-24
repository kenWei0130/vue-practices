<template>
  <div class="container setting text-start">
    <div class="row">
      <!-- title -->
      <h2>權限管理</h2>
    </div>
    <div class="row">
      <!-- 控制的資訊 -->
      <div class="col col-lg-3 col-6">
        <label class="text-align-center form-label">帳號</label>
        <input
          type="text"
          class="form-control"
          value="1111111"
          disabled
          readonly
        />
      </div>
      <div class="col col-lg-3 col-6">
        <label class="text-align-center form-label">角色</label>
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
    <!-- <div class="row">
      <div class="m-3 border">
        <div v-for="category in categories" :key="category">
          <input
            type="checkbox"
            id="chk_{{category.name}}"
            class="me-2"
            v-model="category.isChecked"
            @change="checkedCategory(category)"
          />
          <span class="col me-2">{{ category.name }}</span>
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
          </div>
        </div>
      </div>
    </div> -->
    <div class="row">
      <tree-menu
        :name="categories.name"
        :children="categories.children"
        :depth="0"
      ></tree-menu>
    </div>
    <div class="row">
      <div class="col">
        <button class="btn btn-primary" @click="alertSucces()">確認</button>
      </div>
    </div>
  </div>
</template>

<script>
import TreeView from "./TreeView.vue";
export default {
  components: { 'tree-menu': TreeView },
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
        name: '',
        children: [
        {
          name: "能源",
          isChecked: false,
          role: "4",
          children: [
            { name: "裝機", isChecked: false, role: "" },
            { name: "會勘", isChecked: false, role: "" },
            { name: "維修", isChecked: false, role: "" },
            { name: "報表", isChecked: false, role: "5" },
          ],
        },
        {
          name: "空品",
          isChecked: false,
          role: "4",
          children: [
            { name: "裝機", isChecked: false, role: "" },
            { name: "會勘", isChecked: false, role: "" },
            { name: "維修", isChecked: false, role: "" },
            { name: "報表", isChecked: false, role: "5" },
          ],
        },
        {
          name: "報修",
          isChecked: false,
          role: "5",
          children: [
            { name: "查詢", isChecked: false, role: "5" },
            { name: "報表", isChecked: false, role: "4" },
          ],
        },
        {
          name: "權限管理",
          isChecked: false,
          role: "1",
          children: [
            { name: "進度管理", isChecked: false, role: "" },
            { name: "報表管理", isChecked: false, role: "" },
          ],
        },
      ]},
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
