<script setup>
import EventService from "@/services/EventService.js";
import { computed, onMounted, ref, inject } from "vue";
import { useRouter } from "vue-router";
const router = useRouter();
const props = defineProps(["id"]);
const currentPage = inject("currentPage");
const event = ref("");
const id = computed(() => props.id);
onMounted(() => {
  EventService.getEvent(id.value)
    .then((response) => {
      event.value = response.data;
    })
    .catch((error) => {
      console.log(error);
    });
});
function goBack() {
  router.go(-1);
}
</script>

<template>
  <div v-if="event">
    <h1>{{ event.title }}</h1>
    <div id="nav">
      <router-link :to="{ name: 'EventDetails' }">Details</router-link>
      |
      <router-link :to="{ name: 'EventRegister' }">Register</router-link>
      |
      <router-link :to="{ name: 'EventEdit' }">Edit</router-link>
    </div>
    <router-view :event="event"></router-view>
  </div>
</template>

<style scoped>
.back-button {
  border: 0.6px solid black;
  background-color: transparent;
  padding: 0.3rem 0.7rem;
  cursor: pointer;
}
.back-button:active {
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
}
</style>
