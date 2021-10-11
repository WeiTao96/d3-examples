<script setup>
import { onMounted } from "vue";
import * as d3 from "d3";
const list = [
  {
    cityName: "北京",
    num: 1179,
  },
  {
    cityName: "上海",
    num: 1162,
  },
  {
    cityName: "成都",
    num: 1087,
  },
  {
    cityName: "武汉",
    num: 1067,
  },
  {
    cityName: "杭州",
    num: 874,
  },
  {
    cityName: "天津",
    num: 768,
  },
  {
    cityName: "深圳",
    num: 756,
  },
  {
    cityName: "长沙",
    num: 725,
  },
  {
    cityName: "西安",
    num: 716,
  },
  {
    cityName: "南京",
    num: 662,
  },
  {
    cityName: "重庆",
    num: 632,
  },
  {
    cityName: "沈阳",
    num: 626,
  },
  {
    cityName: "广州",
    num: 588,
  },
  {
    cityName: "郑州",
    num: 575,
  },
  {
    cityName: "青岛",
    num: 511,
  },
  {
    cityName: "苏州",
    num: 500,
  },
  {
    cityName: "合肥",
    num: 489,
  },
  {
    cityName: "哈尔滨",
    num: 472,
  },
  {
    cityName: "济南",
    num: 357,
  },
  {
    cityName: "贵阳",
    num: 353,
  },
  {
    cityName: "长春",
    num: 344,
  },
  {
    cityName: "无锡",
    num: 343,
  },
  {
    cityName: "大连",
    num: 342,
  },
  {
    cityName: "南昌",
    num: 337,
  },
  {
    cityName: "宁波",
    num: 311,
  },
  {
    cityName: "太原",
    num: 300,
  },
  {
    cityName: "昆明",
    num: 290,
  },
  {
    cityName: "常州",
    num: 254,
  },
  {
    cityName: "厦门",
    num: 229,
  },
  {
    cityName: "石家庄",
    num: 221,
  },
  {
    cityName: "其他",
    num: 14358,
  },
];
const svgWidth = 960,
  svgHeight = 500,
  radius = 220,
  labelHeight = 18;

let total = 0;

onMounted(() => {
  init();
});

const init = () => {
  list.map((item) => (total += item.num));
  setSvg();
};

const setSvg = () => {
  // 设置画布大小
  let svg = d3
    .select("#pie")
    .append("svg")
    .attr("width", svgWidth)
    .attr("height", svgHeight)
    .append("g")
    .attr("transform", `translate(${svgWidth / 2}, ${svgHeight / 2})`);

  // 用一段弧形来定义一个圆
  let arc = d3
    .arc()
    .innerRadius(radius - 60)
    .outerRadius(radius);

  let arcHover = d3
    .arc()
    .innerRadius(radius - 60)
    .outerRadius(radius + 10);

  // 定义一个 pie
  let pie = d3
    .pie()
    .value((d) => d.num)
    .padAngle(0.02); // 设置间隔

  // 设置颜色
  let color = d3
    .scaleOrdinal()
    .domain(list.map((d) => d.num))
    .range(
      d3
        .quantize((t) => d3.interpolateSpectral(t * 0.8 + 0.1), list.length)
        .reverse()
    );

  // 定义一个legend
  const legend = svg
    .append("g")
    .attr(
      "transform",
      `translate(-${svgWidth / 2 - 30},-${svgHeight / 2 - 30})`
    );

  legend
    .selectAll(null)
    .data(pie(list))
    .enter()
    .append("rect")
    .attr("x", (d) => labelHeight * parseInt(d.index / 14) * 4.8)
    .attr(
      "y",
      (d) => labelHeight * (d.index - parseInt(d.index / 14) * 14) * 1.8
    )
    .attr("width", labelHeight)
    .attr("height", labelHeight)
    .attr("fill", (d, i) => {
      return color(i);
    })
    .attr("stroke", "grey")
    .style("stroke-width", "1px");

  legend
    .selectAll(null)
    .data(pie(list))
    .enter()
    .append("text")
    .text((d) => d.data.cityName)
    .attr("x", (d) => labelHeight * parseInt(d.index / 14) * 4.8 + 25)
    .attr(
      "y",
      (d) =>
        labelHeight * (d.index - parseInt(d.index / 14) * 14) * 1.8 +
        labelHeight
    )
    .style("font-family", "sans-serif")
    .style("font-size", `${labelHeight}px`);

  svg
    .append("g")
    .selectAll("path")
    .data(pie(list))
    .enter()
    .append("path")
    .style("fill", (d, i) => {
      return color(i);
    })
    .attr("d", arc)
    .on("mouseenter", function (e, d) {
      svg
        .data([d])
        .append("text")
        .attr("class", "text")
        .text((d) => {
          return d.data.cityName;
        });
      svg
        .data([d])
        .append("text")
        .attr("class", "info")
        .attr("dy", "2.5em")
        .text((d) => {
          return `${d.data.num} 家`;
        })
        .attr("fill", "#909399");
      svg
        .data([d])
        .append("text")
        .attr("class", "info")
        .attr("dy", "4em")
        .text((d) => {
          return `${((d.data.num / total) * 100).toFixed(2)} %`;
        })
        .attr("fill", "#909399");

      d3.select(this)
        .transition()
        .duration(200)
        .ease(d3.easeBounceOut)
        .attr("d", arcHover);
    })
    .on("mouseleave", function (e, d) {
      svg.selectAll(".text").remove();
      svg.selectAll(".info").remove();
      d3.select(this)
        .transition()
        .duration(200)
        .ease(d3.easeBounceOut)
        .attr("d", arc);
    });
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
