<template>
  <div class="cg-wrap pocket" :data-piece="hs.getPiece()" :style="inCenter ? '' : cgWidth()">
    <div v-if="isHand()" :id="divId()" style="flex-wrap: wrap" :style="pocketStyle()" :class="pocketCss()">
      <piece v-for="(i, index) in store.pieces()" v-if="handType == 'shop'" :key="i" :class="`${color} ${i} ${handType}`"
        :data-color="color" :data-role="i" :data-nb="dataNb(index)" :data-max="store.dataMax()[index]"
        @click="increment(index, i[0])" />
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, defineProps } from "vue";
import { useBoardSize } from "@/store/useBoardSize";
import { useShuuroStore } from "@/store/useShuuroStore";
import { useHeaderSettings } from "@/store/headerSettings";

const boardSize = useBoardSize();
const hs = useHeaderSettings();

onMounted(() => {
  let element = document.querySelector("#mainboard") as HTMLElement;
  boardSize.updateHeight(element.offsetWidth);
});

function divId() {
  return props.side == "top" ? "pocket0" : "pocket1";
}

const props = defineProps<{
  color: string;
  handType: string;
  counter: number[];
  inCenter: boolean;
  side: string;
}>();

const store = useShuuroStore();
window.addEventListener("resize", boardSize.resize, true);

function cgWidth(): string {
  return boardSize.genVars();
}

function piece_counter(): number[] {
  if (props.handType == "shop") {
    // read from shop
    return store.piece_counter! as unknown as number[];
  } else {
    // read from props
    return props.counter.slice().splice(1);
  }
}

function dataNb(index: number): number | string {
  let counter = piece_counter()[index];
  if (counter == 0 && props.handType == "shop") {
    // only shop has plus
    return "+";
  }
  return counter;
}

function increment(_index: number, p: string): void {

  if (props.handType == "shop" && store.am_i_player == true) {
    if (props.color == "white") {
      p = p.toUpperCase();
    } else {
      p = p.toLowerCase();
    }

    store.buy(p, props.color);
    scrollToBottom();
  }
}

function scrollToBottom(): void {
  const container = document.querySelector("#movelist");
  container!.scrollTop = container!.scrollHeight;
}

function pocketCss(): string {
  return `pocket ${props.side} usable`;
}

function isHand(): boolean {
  let stage = store.client_stage!;
  return stage == 0 || stage == 1;
}

function files(): number {
  let r = props.handType == "shop" ? 8 : 10;

  return props.handType == "pocket" && store.getVariant().endsWith("Fairy")
    ? 12
    : r;
}

function pocketLength(): number {
  let variant = store.getVariant();
  return variant.endsWith("Fairy") ? 9 : 6;
}

function pocketStyle(): string {
  return `--pocketLength: ${pocketLength()};
          --files: ${files()};
          --ranks: ${files()};
          ${cgWidth()}`
}
</script>

<style scoped></style>
