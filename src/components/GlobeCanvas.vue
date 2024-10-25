<template>
  <div ref="globeContainer"></div>
</template>

<script>
import * as d3 from 'd3';
import * as topojson from 'topojson-client';
import Versor from '@/utils/Versor';
import world from '@/assets/countries-110m.json';


export default {
  name: 'GlobeCanvas',
  props: {

  },
  mounted() {
    this.initGlobe();
  },
  methods: {
    initGlobe() {
      const width = this.$refs.globeContainer.clientWidth;
      const height = Math.min(width, 720);

      const dpr = window.devicePixelRatio ?? 1;
      const canvas = d3.create("canvas")
        .attr("width", dpr * width)
        .attr("height", dpr * height)
        .style("width", `${width}px`);
      const context = canvas.node().getContext("2d");
      context.scale(dpr, dpr);

      const projection = d3.geoOrthographic().fitExtent([[10, 10], [width - 10, height - 10]], {type: "Sphere"});
      const path = d3.geoPath(projection, context);
      const tilt = 20;

      const land = topojson.feature(world, world.objects.land);
      const borders = topojson.mesh(world, world.objects.countries, (a, b) => a !== b);

      const render = (country, arc) => {
        context.clearRect(0, 0, width, height);
        context.beginPath(), path(land), context.fillStyle = "#ccc", context.fill();
        context.beginPath(), path(country), context.fillStyle = "#f00", context.fill();
        context.beginPath(), path(borders), context.strokeStyle = "#fff", context.lineWidth = 0.5, context.stroke();
        context.beginPath(), path({type: "Sphere"}), context.strokeStyle = "#000", context.lineWidth = 1.5, context.stroke();
        if (arc) context.beginPath(), path(arc), context.stroke();
        return context.canvas;
      };

     const countries = topojson.feature(world, world.objects.countries).features;
      let p1, p2 = [0, 0], r1, r2 = [0, 0, 0];

      const animate = async () => {
        for (const country of countries) {
          render(country);

          p1 = p2, p2 = d3.geoCentroid(country);
          r1 = r2, r2 = [-p2[0], tilt - p2[1], 0];
          const ip = d3.geoInterpolate(p1, p2);
          const iv = Versor.interpolateAngles(r1, r2);

          await d3.transition()
            .duration(1250)
            .tween("render", () => t => {
              projection.rotate(iv(t));
              render(country, {type: "LineString", coordinates: [p1, ip(t)]});
            })
            .transition()
            .tween("render", () => t => {
              render(country, {type: "LineString", coordinates: [ip(t), p2]});
            })
            .end();
        }
      };

      this.$refs.globeContainer.appendChild(canvas.node());
      animate();
    }
  }
}
</script>

<style scoped>
div {
  width: 100%;
  height: 100%;
}
</style>
