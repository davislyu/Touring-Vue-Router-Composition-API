//EventList.vue
<script setup>
import EventCard from "@/components/EventCard.vue";
import EventService from "@/services/EventService.js";
import { onMounted, ref, computed, watchEffect, provide } from "vue";

const totalPages = computed(() => {
  return Math.ceil(totalEvents.value / 2);
});

const events = ref("");
const props = defineProps(["page"]);
const currentPage = computed(() => {
  return page.value;
});
const totalEvents = ref(0);
const hasNextPage = computed(() => {
  const totalPages = Math.ceil(totalEvents.value / 2);
  return page.value < totalPages;
});
const page = computed(() => Number(props.page));

const changePage = computed(() => {
  currentPage.value = page.value;
});
provide("currentPage", page);

onMounted(() => {
  watchEffect(() => {
    events.value = null;
    EventService.getEvents(2, page.value)
      .then((response) => {
        events.value = response.data;
        totalEvents.value = response.headers["x-total-count"];
        console.log(page.value);
        console.log(currentPage.value);
      })
      .catch((error) => {
        console.log(error);
      });
  });
});
</script>

<template>
  <h1>Events for Good</h1>
  <div class="events">
    <EventCard v-for="event in events" :key="event.id" :event="event" />
    <div class="paginationControl">
      <router-link
        :to="{ name: 'EventList', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
      >
        ← Prev
      </router-link>

      <router-link
        :to="{ name: 'EventList', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage"
        >Next →</router-link
      >
    </div>
    <div class="pageNums">
      <router-link
        v-for="pageNum in totalPages"
        :key="pageNum"
        :to="{ name: 'EventList', query: { page: pageNum } }"
        :class="{ 'active-page': pageNum === page }"
      >
        {{ pageNum }}
      </router-link>
    </div>
  </div>
</template>

<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.paginationControl {
  display: flex;
  gap: 1.2rem;
}
.paginationControl a {
  text-decoration: none;
  color: black;
}
.paginationControl a:hover {
  transform: scale(1.1);
  transition: 0.3s;
}
.paginationControl a:active {
  transform: scale(1);
  transition: 0.3s;
}
.active-page {
  font-weight: 900;
}

.pageNums {
  display: flex;
  gap: 1.1rem;
  text-decoration: none;
  margin-top: 3rem;
}
.router-link-exact-active:active {
  color: black;
}
.router-link-exact-active {
  text-decoration: none;
  color: black;
}
</style>
