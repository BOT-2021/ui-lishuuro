<template>
  <div :class="`info-wrap${part}`">
    <div class="clock-wrap">
      <div :id="`clock${part}`">
        <div class="clock" :class="{ running: isRunning(), hurry: isHurry() }">
          <div class="clock-time min">
            {{ min() }}
          </div>
          <div class="clock-sep low">:</div>
          <div class="clock-time sec">
            {{ sec() }}
          </div>
          <div class="clock-time byo">+{{ shuuroStore.incr }}s</div>
        </div>
      </div>
      <div id="more-time" />
      <div id="`berserk${part}`" />
    </div>
    <div :id="`misc-info${part}`" />
  </div>
</template>
<script setup lang="ts">
import { defineProps } from "vue";
import { useShuuroStore } from "@/store/useShuuroStore";

const props = defineProps<{
  color: string;
  part: string;
}>();

const shuuroStore = useShuuroStore();

function min(): string {
  let id = props.color == "white" ? 0 : 1;
  return shuuroStore.clocks[id].currentMin;
}

function sec(): string {
  let id = props.color == "white" ? 0 : 1;
  let result = shuuroStore.clocks[id].currentSec;
  return result;
}

function isRunning(): boolean {
  let id = props.color == "white" ? 0 : 1;
  return shuuroStore.clocks[id].running;
}

function isHurry(): boolean {
  let id = props.color == "white" ? 0 : 1;
  return shuuroStore.clocks[id].hurry;
}
</script>
<style></style>
