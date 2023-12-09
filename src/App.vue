<template>
  <!-- <div class="header">
    <span style="letter-spacing: 1px;color: #2c3e50;">DATA Visualization</span>

    <div class="zoom-buttons">
      <div
        style="height: 20px; width:50px; color: #2c3e50; border: 1px solid #ccc; display: flex; justify-content: center;align-items: center; cursor: pointer; background-color: #ffffff;"
        @click="handleZoom(1.2)">+</div>
      <div
        style="height: 20px; width:50px; color: #2c3e50; border: 1px solid #ccc; display: flex; justify-content: center;align-items: center; cursor: pointer; background-color: #ffffff;"
        @click="handleZoomOut(0.8)">-</div>
    </div>
  </div>
  <div class="zoomed-section" :class="{ grabbing: isDragging }">
    <svg  draggable="true" class="zoom-container" ref="zoomContainer" @wheel.prevent="handleScroll" @mousedown="startDrag"
      @mousemove="handleDrag" @mouseup="stopDrag"></svg>
  </div> -->

  <svg ref="zoomContainer"></svg>
</template>

<script setup>
/* eslint-disable */
import * as d3 from "d3";

// import HelloWorld from './components/HelloWorld.vue'

import { ref, onMounted } from "vue";
const zoomContainer = ref(null);
let currentScale = 100;
let isDragging = false;
let dragStartX = 0;
let dragStartY = 0;
let dragX = 0;
let dragY = 0;
const handleZoom = (scaleFactor) => {
  currentScale += scaleFactor;
  zoomContainer.value.style.transform = `scale(${currentScale})`;
  console.log("currentScale : ", currentScale);
};
const handleZoomOut = (scaleFactor) => {
  currentScale -= scaleFactor;
  zoomContainer.value.style.transform = `scale(${currentScale})`;
  console.log("currentScale : ", currentScale);
};
const handleScroll = (event) => {
  if (event.ctrlKey) {
    event.deltaY > 0 ? handleZoom(0.8) : handleZoom(1.2);
  }
};

const startDrag = (event) => {
  isDragging = true;
  dragStartX = event.clientX;
  dragStartY = event.clientY;
};

const handleDrag = (event) => {
  if (isDragging) {
    const deltaX = event.clientX - dragStartX;
    const deltaY = event.clientY - dragStartY;
    dragX += deltaX;
    dragY += deltaY;
    dragStartX = event.clientX;
    dragStartY = event.clientY;
    updateTransform();
  }
};

const stopDrag = () => {
  isDragging = false;
};

const updateTransform = () => {
  zoomContainer.value.style.transform = `scale(${currentScale}%) translate(${dragX}px, ${dragY}px)`;
};

let response = `AecBuildingElementPart
Appliance Product Part
Application Part
Assembled Product Part
BridgePart
Closure Part
Compliance Enterprise Part
Compliance Plant Specific Part
Compliance Reported Part
Compliance Subtier Part
Consumer Kit Part
Consumer Unit Part
DELSerializedLotContinuousProvidedPart
DELSerializedLotManufacturedPart
DELSerializedLotProvidedPart
DELSerializedUnitManufacturedPart
DELSerializedUnitProvidedPart
DamPart
Display Unit Part
DrainagePart
Electrical Part
EnsLogicalPart
Finished Product Part
Formulation Part
GeoMineSurfacePart
GeologyModelPart
GeologyPart
HVAC_Logical_Part
HVAC_Part
Handling Unit Part
Hardware Part
Kit Part
Label Part
Manufacturing Part
MarinePart
Material Part
Mechanical Part
Packaging Assembly Part
Packaging Component Part
Packaging Material Part
Pallet Part
Part
Phantom Part
Piping_Logical_Part
Piping_Part
Product Data Part
Raceway_Part
RailwayPart
RoadPart
SOECoveringPart
SOESystemPart
Semi-Finished Product Part
Software Part
SubgradePart
Supplier Equivalent Part
Support Part
Synthetic Part
Tool Part
Trade Unit Part
Transport Unit Part
TunnelPart`;

response = response.split("\n");
const temp = [];
console.log(response.length);

for (let i in response) {
  temp.push({ name: response[i], type: "type", id: i });
}

response = temp;
// temp = undefined;

const data = ref();
const svg = ref(null); // Declare svg as a ref

onMounted(() => {
  // data.value = {
  //   name: "father",
  //   children: [
  //     {
  //       name: "son1",
  //       children: [{ name: "grandson" }, { name: "grandson2" }],
  //     },
  //     {
  //       name: "son2",
  //       children: [{ name: "grandson3" }, { name: "grandson4" }],
  //     },
  //   ],
  // };

  data.value = {
    name: "Schema",
    children: response,
  };

  // Specify the charts’ dimensions. The height is variable, depending on the layout.
  const width = 928;
  const marginTop = 10;
  const marginRight = 10;
  const marginBottom = 10;
  const marginLeft = 40;

  // Rows are separated by dx pixels, columns by dy pixels. These names can be counter-intuitive
  // (dx is a height, and dy a width). This because the tree must be viewed with the root at the
  // “bottom”, in the data domain. The width of a column is based on the tree’s height.
  const root = d3.hierarchy(data.value);
  const dx = 30;
  const dy = (width - marginRight - marginLeft) / (1 + root.height);

  // Define the tree layout and the shape for links.
  const tree = d3.tree().nodeSize([dx, dy]);
  const diagonal = d3
    .linkHorizontal()
    .x((d) => d.y)
    .y((d) => d.x);

  // Create the SVG container, a layer for the links and a layer for the nodes.

  const svg = d3
    .select("svg")
    .attr("width", width)
    .attr("height", dx)
    .attr("viewBox", [-marginLeft, -marginTop, width, dx])
    .attr(
      "style",
      "max-width: 100%; height: auto; font: 13px sans-serif; user-select: none;"
    );

  // const svg = d3
  //   .select(this.$refs.chartContainer)
  //   // .selectAll("svg")
  //   .attr("width", width)
  //   .attr("height", dx)
  //   .attr("viewBox", [-marginLeft, -marginTop, width, dx])
  //   .attr(
  //     "style",
  //     "max-width: 100%; height: auto; font: 10px sans-serif; user-select: none;"
  //   );

  const gLink = svg
    .append("g")
    .attr("fill", "none")
    .attr("stroke", "#555")
    .attr("stroke-opacity", 0.4)
    .attr("stroke-width", 1.5);

  const gNode = svg
    .append("g")
    .attr("cursor", "pointer")
    .attr("pointer-events", "all");

  function update(event, source) {
    const duration = event?.altKey ? 2500 : 250; // hold the alt key to slow down the transition
    const nodes = root.descendants().reverse();
    const links = root.links();

    // Compute the new tree layout.
    tree(root);

    let left = root;
    let right = root;
    root.eachBefore((node) => {
      if (node.x < left.x) left = node;
      if (node.x > right.x) right = node;
    });

    const height = right.x - left.x + marginTop + marginBottom;

    const transition = svg
      .transition()
      .duration(duration)
      .attr("height", height)
      .attr("viewBox", [-marginLeft, left.x - marginTop, width, height])
      .tween(
        "resize",
        window.ResizeObserver ? null : () => () => svg.dispatch("toggle")
      );

    // Update the nodes…
    const node = gNode.selectAll("g").data(nodes, (d) => d.id);

    function toggleChildren(d) {
      if (!d.children) {
        // If there are no children or they are hidden, add a new child
        d.children = [{ name: "New Child" }];
      } else {
        // If the node has children, toggle visibility
        if (d.children) {
          d._children = d.children;
          d.children = null;
        } else {
          d.children = d._children;
          d._children = null;
        }
      }
    }

    // Enter any new nodes at the parent's previous position.
    const nodeEnter = node
      .enter()
      .append("g")
      .attr("transform", (d) => `translate(${source.y0},${source.x0})`)
      .attr("fill-opacity", 0)
      .attr("stroke-opacity", 0)
      .on("click", (event, d) => {
        handleClick(d);
        // d.children = d.children ? null : d._children;
        console.log(d);
        // data.value.children[d.data.id].children = [
        //   { name: "Policies" },
        //   { name: "Attributes" },
        //   { name: "Relations" },
        // ];
        update(null, d);
      });

    // Custom click function
    function handleClick(nodeData) {
      console.log("Node clicked:", nodeData.data.id);

      console.log(data.value.children[nodeData.data.id]);
      toggleChildren(nodeData);
      // update(null, root);
      // Add your custom logic here
      // For example, you can open a modal, navigate to a different page, etc.
    }
    nodeEnter
      .append("circle")
      .attr("r", 2.5)
      .attr("fill", (d) => (d._children ? "#555" : "#999"))
      .attr("stroke-width", 10);

    nodeEnter
      .append("text")
      .attr("dy", "0.31em")
      .attr("x", (d) => (d._children ? -6 : 6))
      .attr("text-anchor", (d) => (d._children ? "end" : "start"))
      .text((d) => d.data.name)
      .clone(true)
      .lower()
      .attr("stroke-linejoin", "round")
      .attr("stroke-width", 3)
      .attr("stroke", "white");

    // Transition nodes to their new position.
    const nodeUpdate = node
      .merge(nodeEnter)
      .transition(transition)
      .attr("transform", (d) => `translate(${d.y},${d.x})`)
      .attr("fill-opacity", 1)
      .attr("stroke-opacity", 1);

    // Transition exiting nodes to the parent's new position.
    const nodeExit = node
      .exit()
      .transition(transition)
      .remove()
      .attr("transform", (d) => `translate(${source.y},${source.x})`)
      .attr("fill-opacity", 0)
      .attr("stroke-opacity", 0)
      .attr("onclick", "method");

    // Update the links…
    const link = gLink.selectAll("path").data(links, (d) => d.target.id);

    // Enter any new links at the parent's previous position.
    const linkEnter = link
      .enter()
      .append("path")
      .attr("d", (d) => {
        const o = { x: source.x0, y: source.y0 };
        return diagonal({ source: o, target: o });
      });

    // Transition links to their new position.
    link.merge(linkEnter).transition(transition).attr("d", diagonal);

    // Transition exiting nodes to the parent's new position.
    link
      .exit()
      .transition(transition)
      .remove()
      .attr("d", (d) => {
        const o = { x: source.x, y: source.y };
        return diagonal({ source: o, target: o });
      });

    // Stash the old positions for transition.
    root.eachBefore((d) => {
      d.x0 = d.x;
      d.y0 = d.y;
    });
  }

  // Do the first update to the initial configuration of the tree — where a number of nodes
  // are open (arbitrarily selected as the root, plus nodes with 7 letters).
  root.x0 = dy / 2;
  root.y0 = 0;
  root.descendants().forEach((d, i) => {
    d.id = i;
    d._children = d.children;
    if (d.depth && d.data.name.length !== 7) d.children = null;
  });

  update(null, root);

  d3.select(".zoom-buttons")
    .style("position", "absolute")
    .style("bottom", "10px")
    .style("right", "10px");
  // ... (existing code)

  return svg.node();
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.header {
  border: 2px solid #ccc;
  height: 50px;
  width: 100vw;
  color: #bd34d1;
  letter-spacing: normal;
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-family: sans-serif;
  font-weight: bold;
  font-size: medium;
  padding-left: 10px;
  background-color: #ffffff;
  margin-bottom: 10px;
}

.zoom-buttons {
  width: 120px;
  /* border: 2px solid red; */
  position: sticky;
  bottom: 10px;
  right: 10px;
  z-index: 999;
  display: flex;
  justify-content: space-around;
}

.zoom-container {
  overflow: hidden;
  width: 100vw;
  height: 100vh;
  position: relative;
  transition: transform 0.5s ease-in-out;
}

.zoomed-section {
  /* border: 2px solid red; */
  width: 100%;
  /* Adjust width as needed */
  height: 100vh;
  /* Adjust height as needed */
  /* background-color: lightblue; */
  transform-origin: top right;
  cursor: grab;
  overflow: hidden;
}

.zoom-container {
  cursor: grab;
  overflow: hidden;
  width: 100vw;
  height: 100vh;
  position: relative;
  transition: transform 0.5s ease-in-out;
}
</style>
