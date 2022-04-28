<template>
  <main>
    <AddCategory v-if="shouldShowAddCategory" v-on:addCategory="addCategory"/>
    <div v-else>
      <AddBill v-if="shouldShowAddBill" :categories="categories" v-on:addBill="addBill"/>
      <div v-else>
        <NavBar
          :categories="categories"
          :activeCategory="activeCategory"
          v-on:clearActiveCategory="clearActiveCategory"
          v-on:setActiveCategory="setActiveCategory"
          v-on:triggerShowAddCategory="triggerShowAddCategory"
        />
        <div class="container flex">
          <div class="w-1/2 bg-gray-200">
            <BillsTable
              :bills="activeBills"
              v-on:triggerShowAddBill="triggerShowAddBill"
              v-on:removeBill="removeBill"
            />
          </div>
          <div class="w-1/2 bg-gray-400 pt-4 pl-4 text-2xl">
            <Chart :bills="activeBills"/>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>


<script>
import Vue from "vue";
Vue.use(require("vue-moment"));

import AddCategory from "./components/AddCategory.vue";
import AddBill from "./components/AddBill.vue";
import NavBar from "./components/NavBar.vue";
import Chart from "./components/Chart.vue";
import BillsTable from "./components/BillsTable.vue";

export default {
  name: "app",
  components: {
    AddCategory,
    AddBill,
    Chart,
    BillsTable,
    NavBar
  },
  data() {
    return {
      bills: [],
      categories: [],
      shouldShowAddCategory: false,
      shouldShowAddBill: true,
      activeCategory: ""
    };
  },
  methods: {
    addCategory(category) {
      this.categories.push(category);
      this.shouldShowAddCategory = false;
    },
    triggerShowAddCategory() {
      this.shouldShowAddCategory = true;
    },
    addBill(bill) {
      this.bills.push(bill);
      this.shouldShowAddBill = false;
    },
    triggerShowAddBill() {
      this.shouldShowAddBill = true;
    },
    removeBill(index) {
      this.bills = this.bills
        .slice(0, index)
        .concat(this.bills.slice(index + 1, this.bills.length));
    },
    clearActiveCategory() {
      this.activeCategory = "";
    },
    setActiveCategory(category) {
      this.activeCategory = category;
    }
  },
  watch: {
    bills() {
      localStorage.setItem("bills", JSON.stringify(this.bills));
    },
    categories() {
      localStorage.setItem("categories", JSON.stringify(this.categories));
    }
  },
  mounted() {
    if (localStorage.getItem("bills")) {
      this.bills = JSON.parse(localStorage.getItem("bills"));
    }

    if (localStorage.getItem("categories")) {
      this.categories = JSON.parse(localStorage.getItem("categories"));
    }

    if (!this.bills.length && !this.categories.length) {
      this.shouldShowAddCategory = true;
    }
  },
  computed: {
    activeBills() {
      return this.bills
        .filter(bill =>
          this.activeCategory ? bill.category === this.activeCategory : true
        )
        .sort((a, b) => (new Date(a.date) < new Date(b.date) ? 1 : -1));
    }
  }
};
</script>
