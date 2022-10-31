<script setup lang="ts">
import { ref, computed } from "vue";
import { Post, today, thisWeek, thisMonth } from "../posts";
import { DateTime } from "luxon";

const periods = ["Today", "This week", "This month"] as const;
type Period = typeof periods[number];
const selectedPeriod = ref<Period>("Today");
const posts = computed(() => {
  return [today, thisWeek, thisMonth]
    .map((post) => {
      return {
        ...post,
        created: DateTime.fromISO(post.created),
      };
    })
    .filter((post) => {
      switch (selectedPeriod.value) {
        case "Today":
          return post.created >= DateTime.now().minus({ day: 1 });

        case "This week":
          return post.created >= DateTime.now().minus({ week: 1 });

        case "This month":
          return post.created >= DateTime.now().minus({ week: 4 });

        default:
          break;
      }
    });
});

function selectPeriod(period: Period) {
  selectedPeriod.value = period;
}
</script>

<template>
  <nav class="is-primary panel">
    {{ selectedPeriod }}
    <span class="panel-tabs">
      <a
        v-for="period of periods"
        :key="period"
        :class="{ 'is-active': period === selectedPeriod }"
        @click="selectPeriod(period)"
        >{{ period }}</a
      >
    </span>
    <a v-for="post of posts" :key="post" class="panel-block">
      <a>{{ post.title }} </a>
      <div>{{ post.created.toFormat("d MMM yyyy") }}</div></a
    >
  </nav>
</template>
