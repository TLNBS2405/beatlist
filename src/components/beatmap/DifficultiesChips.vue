<template>
  <v-container :class="short ? 'pa-0 pl-3' : 'pa-1 ml-n2'">
    <v-tooltip v-for="value in difficulties" :key="value.key" top>
      <template #activator="{ on }">
        <v-chip
          :color="value.color"
          :small="small"
          :class="short ? 'ml-n3' : 'ma-1'"
          v-on="on"
        >
          {{ short ? "" : value.chipName }}
        </v-chip>
      </template>
      <span>{{ value.chipName }}</span>
    </v-tooltip>
  </v-container>
</template>

<script lang="ts">
import Vue, { PropType } from "vue";
import { DifficultiesSimple } from "@/libraries/net/beatsaver/BeatsaverBeatmap";
import {
  getColorFor,
  getNameFor,
  getWeightFor,
} from "@/libraries/helper/DifficultiesHelper";

export default Vue.extend({
  name: "DifficultiesChips",
  props: {
    diff: { type: Object as PropType<DifficultiesSimple>, required: true },
    small: { type: Boolean, default: false },
    short: { type: Boolean, default: false },
  },
  data: () => ({
    difficulties: [] as any,
  }),
  watch: {
    diff: {
      handler() {
        this.computeDifficulties();
      },
      immediate: true,
    },
  },
  methods: {
    computeDifficulties() {
      this.difficulties = Object.entries(this.diff)
        .map(([key, value]) => ({
          name: key,
          enabled: value,
          chipName: getNameFor(key),
          color: getColorFor(key),
          weight: getWeightFor(key),
        }))
        .sort((a: any, b: any) => a.weight - b.weight);

      // to force the v-for to re-render on different state, we create an unique key
      const keyBase = Object.values(this.difficulties)
        .map((d: any) => d.enabled)
        .join();

      this.difficulties = this.difficulties
        .map((d: any) => {
          d.key = `${keyBase} - ${d.name}`;
          return d;
        })
        .filter((d: any) => d.enabled);
    },
  },
});
</script>

<style scoped></style>
