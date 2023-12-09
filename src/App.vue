<template>
  <div class="header">
    <span style="letter-spacing: 1px; color: #2c3e50">DATA Visualization</span>

    <!-- <div class="zoom-buttons">
      <div
        style="height: 20px; width:50px; color: #2c3e50; border: 1px solid #ccc; display: flex; justify-content: center;align-items: center; cursor: pointer; background-color: #ffffff;"
        @click="handleZoom(1.2)">+</div>
      <div
        style="height: 20px; width:50px; color: #2c3e50; border: 1px solid #ccc; display: flex; justify-content: center;align-items: center; cursor: pointer; background-color: #ffffff;"
        @click="handleZoomOut(0.8)">-</div>
    </div> -->
  </div>
  <div class="zoomed-section" :class="{ grabbing: isDragging }">
    <!-- <svg  draggable="true" class="zoom-container" ref="zoomContainer" @wheel.prevent="handleScroll" @mousedown="startDrag"
      @mousemove="handleDrag" @mouseup="stopDrag"></svg> -->
    <svg
      @mousedown="startDrag"
      @mousemove="handleDrag"
      @mouseup="stopDrag"
      ref="zoomContainer"
    ></svg>
  </div>
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
// console.log('isDragging : ', isDragging);

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
  console.log("isDragging : ", isDragging);
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
  console.log("isDragging : ", isDragging);
};

const updateTransform = () => {
  zoomContainer.value.style.transform = `scale(${currentScale}%) translate(${dragX}px, ${dragY}px)`;
};

// let response = ``;

// response = response.split("\n");
// const temp = [];
// console.log(response.length);

// for (let i in response) {
//   temp.push({ name: response[i] });
// }

// response = temp;
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
  function splitToList(list) {
    list = list.split("|");
    var tmp = [];
    for (let i in list) {
      tmp.push({
        name: list[i],
      });
    }
    return tmp;
  }

  var toRel = splitToList(
    `Customer Design|Internal Design|CoOwned|Image Holder|Decision Applies To|UPR_LinkToResourceAbstract|Reference Document|Collection Content|DEL3DL_Link|Issue|EQUIVALENT|DERIVED_ABSTRACT|Assigned Apps|Risk Affected Items|VPLMrel/VPMSemanticRelation/C_Owner|VPLMrel/VPMPathElement/C_Owner|VPLMrel/VPMSemanticRelation|WSParentReserve|Resolved To|OntoRootRel|OntoRange|OntoRootOwns|Configuration Item|First Configuration Item|Agenda Item|Additional Context|DELIsFilteredBy|Criteria Output|UKConstrained|Subscribed Item|Candidate Affected Item|Change Affected Item|Implemented Item|Related Item|Vaulted Objects|Task Deliverable|Message Attachments|Meeting Attachments|Workflow Content|Folder Document|Assigned Documents|Vaulted Documents Rev2|Document Structure|Part Family Reference Document|Build Specification|Features Specification|Product Specification|Requirement Specification|Company Documents|Retained Record|Characteristic Test Method|SpecificationDocument|PLMResourceSetSubItemResource|CDMReferenceDocument|Active Version|Latest Version|Reported Against EC|Contains|Has Documents|Requested Document|Document Usage|Download Document|Classified Item|Sourcing Document|Favourite Of`
  );

  var fromRel = splitToList(
    `|Markup|Pending Job|ParameterAggregation|Sharing Access|DEL3DL_Link|Checklist|Configuration Context|EQUIVALENT|DERIVED_ABSTRACT|Root Questionnaire|Related Item Questionnaire|Contributes To|Publish Subscribe|VPLMrel/VPMSemanticRelation|Thread|Meeting Context|WSLocalReserve|OntoRootRel|ParameterUsage|isVersionOf|ParameterComposition|VPLMInteg-VPLMProjection|SpecificationDocument|hasViews|Informed User|Template|Performance Specification|Performance Specification Reference|UKConstrained|Effort|Object Route|Reference Document|Document Structure|Active Version|Latest Version|IEFComments|Document Sheets|Version `
  );

  // tmp.length = 0;

  data.value = {
    name: "Schema",
    children: [
      { name: "Compound Document" },
      { name: "Data Document" },
      { name: "DmtDocument" },
      {
        name: "Document",
        children: [
          {
            name: "Policies",
            children: [{ name: "Document Release" }, { name: "Version" }],
          },
          {
            name: "Attributes",
            children: [
              { name: "clau" },
              { name: "Designated User" },
              { name: "Access Type" },
              { name: "Checkin Reason" },
              { name: "Language" },
              { name: "Is Version Object" },
              { name: "Move Files To Version" },
              { name: "Suspend Versioning" },
              { name: "File Created Date" },
              { name: "File Dimension" },
              { name: "File Duration" },
              { name: "File Modified Date" },
              { name: "File Size" },
              { name: "File Type" },
              { name: "Originator" },
              { name: "Title" },
              { name: "Version" },
              { name: "File Version" },
              { name: "Version Date" },
              { name: "Secondary Keys" },
              { name: "CAD Type" },
              { name: "Primary Key" },
            ],
          },
          {
            name: "Relationships",
            children: [
              { name: "From Relation", children: fromRel },
              { name: "To Relation", children: toRel },
            ],
          },
          { name: "Triggers" },
        ],
      },
      { name: "Generic Document" },
      { name: "MS Excel Document" },
      { name: "MS Outlook Document" },
      { name: "MS Powerpoint Document" },
      { name: "MS Word Document" },
      { name: "Office Document" },
      { name: "Quality System Document" },
      { name: "Report Document" },
      { name: "Technical Document" },
      { name: "Version Document" },
      { name: "dsc_Simulation_Document" },
    ],
  };

  // Specify the charts’ dimensions. The height is variable, depending on the layout.
  const width = 1600;
  const marginTop = 10;
  const marginRight = 10;
  const marginBottom = 10;
  const marginLeft = 80;

  // Rows are separated by dx pixels, columns by dy pixels. These names can be counter-intuitive
  // (dx is a height, and dy a width). This because the tree must be viewed with the root at the
  // “bottom”, in the data domain. The width of a column is based on the tree’s height.
  const root = d3.hierarchy(data.value);
  const dx = 30;
  const dy = (width - marginRight - marginLeft) / (1 + root.height) + 30;

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
      "max-width: 100%; height: auto; font: 15px sans-serif; user-select: none;"
    );

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
    const duration = event?.altKey ? 3500 : 350; // hold the alt key to slow down the transition
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

    // Enter any new nodes at the parent's previous position.
    const nodeEnter = node
      .enter()
      .append("g")
      .attr("transform", (d) => `translate(${source.y0},${source.x0})`)
      .attr("fill-opacity", 0)
      .attr("stroke-opacity", 0)
      .on("click", (event, d) => {
        handleClick(d);
        d.children = d.children ? null : d._children;
        update(event, d);
      });

    // Custom click function
    function handleClick(nodeData) {
      console.log("Node clicked:", nodeData.data.name);
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
  /* border: 2px solid red; */
  width: 120px;
  position: sticky;
  bottom: 10px;
  right: 10px;
  z-index: 999;
  display: flex;
  justify-content: space-around;
}

.zoom-container {
  /* border: 2px solid red; */
  overflow: hidden;
  width: 100vw;
  height: 100vh;
  /* position: relative; */
  transition: transform 0.5s ease-in-out;
}

.zoomed-section {
  /* border: 2px solid red; */
  /* height: 100%; */
  width: 100%;
  /* Adjust width as needed */
  /* height: 100vh; */
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
