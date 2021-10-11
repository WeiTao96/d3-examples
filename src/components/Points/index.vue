<script setup>
import { onMounted } from "vue";
import * as d3 from "d3";

let width = 300,
  height = 200;

onMounted(() => {
  init();
});

const init = () => {
  setSvg();
};

const setSvg = () => {
  const svg = d3
    .select("#point")
    .append("svg")
    .attr("width", width)
    .attr("height", height);

  svg
    .selectAll("circle")
    .data(d3.range(100))
    .join("circle")
    .attr("cx", (d) => d * 50)
    .attr("cy", height / 2)
    .attr("r", (d) => Math.random() * 20)
    .attr("fill", "hsl(216deg 100% 13%)");

  return svg.node();
};
</script>

<template>
  <div id="point"></div>
</template>

<style>
</style>
