<template>
  <div>
    <table border="1" width="100%">
      <thead>
        <tr>
          <th>SR.No</th>
          <th @click="sort('name')">
            Name
            <span v-if="sortColumn === 'name'">{{
              sortOrder === "asc" ? "▲" : "▼"
            }}</span>
          </th>
          <th @click="sort('dob')">
            DOB
            <span v-if="sortColumn === 'dob'">{{
              sortOrder === "asc" ? "▲" : "▼"
            }}</span>
          </th>
          <th @click="sort('email')">
            Email
            <span v-if="sortColumn === 'email'">{{
              sortOrder === "asc" ? "▲" : "▼"
            }}</span>
          </th>
          <th>Location</th>
          <th @click="sort('phone')">
            Phone
            <span v-if="sortColumn === 'phone'">{{
              sortOrder === "asc" ? "▲" : "▼"
            }}</span>
          </th>
          <th>Photo</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, key) in sortedItems" :key="key">
          <td>{{ key + 1 }}</td>
          <td>{{ item.name }}</td>
          <td>
            {{ item.dob ? moment(item.dob).format("DD/MM/YYYY") : "-" }}
          </td>
          <td>{{ item.email }}</td>
          <td>
            {{ item.location ? item.location : "-" }}
            {{ item.city }}
          </td>
          <td>{{ item.phone }}</td>
          <td><img :src="item.image" alt="Item Image" /></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import moment from "moment";
const rand_data = ref([]);

const fetchData = async () => {
  try {
    const response = await fetch("https://randomuser.me/api/?results=50");
    const data = await response.json();
    data.results.forEach((item) => {
      rand_data.value.push({
        name: item.name.first,
        dob: item.dob.date,
        email: item.email,
        location: item.location.street.name,
        city: item.location.city,
        phone: item.phone,
        image: item.picture.medium,
      });
    });
  } catch (error) {
    console.error("Error", error);
  }
};

onMounted(fetchData);

const sortColumn = ref("");
const sortOrder = ref("asc");

const sortedItems = computed(() => {
  return rand_data.value.slice().sort((a, b) => {
    const aValue = a[sortColumn.value];
    const bValue = b[sortColumn.value];

    if (aValue < bValue) {
      return sortOrder.value === "asc" ? -1 : 1;
    } else if (aValue > bValue) {
      return sortOrder.value === "asc" ? 1 : -1;
    }
    return -1;
  });
});

const sort = (column) => {
  if (sortColumn.value === column) {
    sortOrder.value = sortOrder.value === "asc" ? "desc" : "asc";
  } else {
    sortColumn.value = column;
    sortOrder.value = "asc";
  }
};
</script>
