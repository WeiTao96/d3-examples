<script setup>
import { onMounted } from "vue";
import * as d3 from "d3";

const width = 960,
  height = 500,
  margin = { top: 20, right: 30, bottom: 30, left: 40 };

const data = [
  {
    date: "2007-04-23",
    value: 93.24,
  },
  {
    date: "2007-04-24",
    value: 43.24,
  },
  {
    date: "2007-04-25",
    value: 55.24,
  },
  {
    date: "2007-04-26",
    value: 64.24,
  },
  {
    date: "2007-04-27",
    value: 13.24,
  },
  {
    date: "2007-04-28",
    value: 97.24,
  },
  {
    date: "2007-04-29",
    value: 53.24,
  },
  {
    date: "2007-04-30",
    value: 23.24,
  },
  {
    date: "2007-05-01",
    value: 96.24,
  },
];

onMounted(() => {
  init();
});

const init = () => {
  setSvg();
};

const timeDomain = d3.extent(data, (d) => d.date);

const x = d3
  .scaleTime() // 返回时间刻度
  .domain([new Date(timeDomain[0]), new Date(timeDomain[1])]) // 获得返回的数组中的最大值和最小值
  .range([margin.left, width - margin.right]);

const y = d3
  .scaleLinear()
  .domain([0, d3.max(data, (d) => d.value)]) // 获得最大值
  .nice()
  .range([height - margin.bottom, margin.top]);

const xAxis = (g) =>
  g.attr("transform", `translate(0,${height - margin.bottom})`).call(
    d3
      .axisBottom(x) // 使用给定的 scale 构建一个刻度在下的坐标轴生成器
      .ticks(width / 80) // 设置间隔
      .tickSizeOuter(0) // 设置外侧的刻度大小
  );

const yAxis = (g) =>
  g
    .attr("transform", `translate(${margin.left},0)`)
    .call(d3.axisLeft(y)) // 使用给定的 scale 构建一个刻度在左的坐标轴生成器
    .call((g) => g.select(".domain").remove())
    .call((g) =>
      g
        .select(".tick:last-of-type text")
        .clone()
        .attr("x", 3)
        .attr("text-anchor", "start")
        .attr("font-weight", "bold")
    );

const line = d3
  .line()
  .defined((d) => !isNaN(d.value))
  .x((d) => x(new Date(d.date)))
  .y((d) => y(d.value));

const setSvg = () => {
  let svg = d3
    .select("#pie")
    .append("svg")
    .attr("width", width)
    .attr("height", height);
  svg.append("g").call(xAxis);
  svg.append("g").call(yAxis);

  svg
    .append("path")
    .datum(data)
    .attr("fill", "none")
    .attr("stroke", "steelblue")
    .attr("stroke-width", 1.5)
    .attr("stroke-linejoin", "round")
    .attr("stroke-linecap", "round")
    .attr("d", line);
};
</script>

<template>
  <div id="pie"></div>
</template>

<style>
.text {
  text-anchor: middle;
  font-size: 28px;
  font-weight: bold;
  letter-spacing: 3px;
}
.info {
  text-anchor: middle;
  font-size: 20px;
  font-weight: bold;
  letter-spacing: 3px;
  color: #909399;
}
</style>
