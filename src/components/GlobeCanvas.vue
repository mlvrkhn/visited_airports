<template>
  <div ref="globeContainer"></div>
</template>

<script>
import * as d3 from 'd3';
import * as topojson from 'topojson-client';
import world from '@/assets/countries-110m.json';

export default {
  name: 'GlobeCanvas',
  props: {

  },
  mounted() {
    this.initGlobe();
  },
  data() {
    return {
      rotation: [0, 0],
      dragBehavior: null,
      animationId: null,
      velocity: [0.5, 0],
      lastTime: 0,
      isDragging: false,
      dragVelocity: [0, 0],
      easeFactor: 0.2,
      releaseTime: null,
      spinDuration: 1000, // 1 second in milliseconds
    };
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

      const projection = d3.geoOrthographic()
        .fitExtent([[10, 10], [width - 10, height - 10]], {type: "Sphere"});

      // Add drag behavior
      this.dragBehavior = d3.drag()
        .on('start', this.dragStart)
        .on('drag', this.dragged);

      canvas.call(this.dragBehavior);

      const path = d3.geoPath(projection, context);

      const render = () => {
        projection.rotate(this.rotation);
        context.clearRect(0, 0, width, height);
        context.beginPath(), path(topojson.feature(world, world.objects.land)), context.fillStyle = "#ccc", context.fill();
        context.beginPath(), path(topojson.mesh(world, world.objects.countries, (a, b) => a !== b)), context.strokeStyle = "#fff", context.lineWidth = 0.5, context.stroke();
        context.beginPath(), path({type: "Sphere"}), context.strokeStyle = "#000", context.lineWidth = 1, context.stroke();
      };

      const animate = (time) => {
        if (this.lastTime) {
          const deltaTime = time - this.lastTime;
          
          if (!this.isDragging) {
            if (this.releaseTime && time - this.releaseTime > this.spinDuration) {
              // Stop spinning after spinDuration
              this.velocity = [0, 0];
            } else {
              // Apply friction to slow down rotation
              this.velocity[0] *= 0.95;
              this.velocity[1] *= 0.95;
            }
          } else {
            // Use drag velocity directly when dragging
            this.velocity = [...this.dragVelocity];
          }

          // Apply rotation
          this.rotation[0] += this.velocity[0] * deltaTime / 16;
          this.rotation[1] += this.velocity[1] * deltaTime / 16;

          // Keep rotation within bounds
          this.rotation[0] %= 360;
          this.rotation[1] = Math.max(-90, Math.min(90, this.rotation[1]));
        }
        this.lastTime = time;
        
        render();
        this.animationId = requestAnimationFrame(animate);
      };

      this.render = render;
      this.animate = animate;

      this.animationId = requestAnimationFrame(this.animate);
      this.$refs.globeContainer.appendChild(canvas.node());
    },

    dragStart(event) {
      this.isDragging = true;
      this.dragVelocity = [0, 0];
      this.releaseTime = null;
      this.dragBehavior.on('drag', this.dragged);
    },

    dragged(event) {
      const projection = d3.geoOrthographic();
      const sensitivity = 75 / projection.scale();
      
      this.rotation[0] += event.dx * sensitivity;
      this.rotation[1] -= event.dy * sensitivity;

      // Update drag velocity
      this.dragVelocity[0] = event.dx * sensitivity * 0.1;
      this.dragVelocity[1] = -event.dy * sensitivity * 0.1;

      // Keep rotation within bounds
      this.rotation[0] %= 360;
      this.rotation[1] = Math.max(-90, Math.min(90, this.rotation[1]));
    },

    dragEnd() {
      this.isDragging = false;
      this.releaseTime = performance.now();
      // Set initial velocity after release based on drag velocity
      this.velocity = [...this.dragVelocity];
    },
  },
  beforeUnmount() {
    if (this.animationId) {
      cancelAnimationFrame(this.animationId);
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
