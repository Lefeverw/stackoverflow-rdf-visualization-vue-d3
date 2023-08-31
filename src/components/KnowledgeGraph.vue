<template>
  <div>
    <svg></svg>
  </div>
</template>
<script>
import * as d3 from "d3";
import {triplesToGraph} from "@/components/functions/triplesToGraph";

export default {
  mounted() {
    const width = 800;
    const height = 500;
    const triples = [
      {subject:"ex:ThaiLand", 	predicate:"ex:hasFood", 	object:"ex:TomYumKung"},
      {subject:"ex:TomYumKung", 	predicate:"rdf:type", 		object:"ex:SpicyFood"},
      {subject:"ex:TomYumKung", 	predicate:"ex:includes", 	object:"ex:shrimp"},
      {subject:"ex:TomYumKung", 	predicate:"ex:includes", 	object:"ex:chilly"},
      {subject:"ex:TomYumKung", 	predicate:"ex:includes", 	object:"ex:lemon"},
      {subject:"ex:lemon", 		predicate:"ex:hasTaste", 	object:"ex:sour"},
      {subject:"ex:chilly", 		predicate:"ex:hasTaste", 	object:"ex:spicy"}
    ];

    var graph = triplesToGraph(triples);
    const svg = d3.select("svg").attr("width", width).attr("height", height);

    function ticked() {
      var links = svg.selectAll(".link")
          .data(graph.links)
          .enter()
          .append("line")
          .attr("marker-end", "url(#end)")
          .attr("class", "link")
          .attr("stroke-width",1)
      ;//links


      // ==================== Add Link Names =====================
      var linkTexts = svg.selectAll(".link-text")
          .data(graph.links)
          .enter()
          .append("text")
          .attr("class", "link-text")
          .text( function (d) { return d.predicate; })
      ;
      // ==================== Add Link Names =====================
      var nodeTexts = svg.selectAll(".node-text")
          .data(graph.nodes)
          .enter()
          .append("text")
          .attr("class", "node-text")
          .text( function (d) { return d.label; })
      ;

      // ==================== Add Node =====================
      var nodes = svg.selectAll(".node")
          .data(graph.nodes)
          .enter()
          .append("circle")
          .attr("class", "node")
          .attr("r",8)
          // .call(force.drag)

      nodes
          .attr("cx", function(d){ return d.x; })
          .attr("cy", function(d){ return d.y; })
      ;

      links
          .attr("x1", 	function(d)	{ return d.source.x; })
          .attr("y1", 	function(d) { return d.source.y; })
          .attr("x2", 	function(d) { return d.target.x; })
          .attr("y2", 	function(d) { return d.target.y; })
      ;

      nodeTexts
          .attr("x", function(d) { return d.x + 12 ; })
          .attr("y", function(d) { return d.y + 3; })
      ;


      linkTexts
          .attr("x", function(d) { return 4 + (d.source.x + d.target.x)/2  ; })
          .attr("y", function(d) { return 4 + (d.source.y + d.target.y)/2 ; })
      ;
    }

    var force = d3.forceSimulation(graph.nodes)
        .force("link", d3.forceLink(graph.links).id(d => d.id))
        .force("charge", d3.forceManyBody())
        .force("center", d3.forceCenter(width / 2, height / 2))
        .on("tick", ticked);
  },
};
</script>