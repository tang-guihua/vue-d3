<template>
    <div>
        <svg id="containter" width=100% :height=svgHei style='border: 1px dashed'>
            
        </svg>  
    </div>
</template>

<script setup>
import * as d3 from "d3"
import _ from 'lodash'
import data from '../../../../single-d3/a.json'
import { ref, onMounted, computed } from "vue"

let movies = _.values(data) 
// console.log(movies)
const rectWidth = 50
const barData = [45, 67, 96, 84, 41]
const pathWidth = 120
const perRow = 8
const pathColors = ['#46e276', '#6e94e7', '#b877e9', '#f25d75']
const petalColors = ['#ffc8f0', '#cbf2bd', '#afe9ff', '#ffb09e']
const topGenres = ["Action", "Comedy", "Animation", "Drama"]
const petalPaths = [
                    'M0 0 C50 50 50 100 0 100 C-50 100 -50 50 0 0',
                    'M-35 0 C-25 25 25 25 35 0 C50 25 25 75 0 100 C-25 75 -50 25 -35 0',
                    'M0,0 C50,40 50,70 20,100 L0,85 L-20,100 C-50,70 -50,40 0,0',
                    'M0 0 C50 25 50 75 0 100 C-50 75 -50 25 0 0',
                    ]
const rated = ['G', 'PG', 'PG-13', 'R']
const colorObj = {
  "colors" : _.zipObject(topGenres, petalColors),
  "Other" :'#FFF2B4'
}
const pathObj = _.zipObject(rated, petalPaths)
console.log(colorObj.colors.Action)

function calculateGridPos(i) {
  return [(i % perRow + 0.5) * pathWidth, (Math.floor(i / perRow) + 0.5) * pathWidth]
}

onMounted(() => {
    const svg = d3.select('#containter')
    const selectAll = svg.selectAll('path')
                         .data(movies)
                         .enter()
                         .append('path')
                         .attr('transform', (d, i) => `translate(${calculateGridPos(i)})`)
                         .attr('d', d => pathObj[d.Rated])
                         .attr('fill', d => colorObj.colors[d.Genre.split(",")[0]] || colorObj.Other )
                         .attr('stroke', d => colorObj.colors[d.Genre.split(",")[0]] || colorObj.Other )
                         .attr('fill-opacity', 0.5)
                         .attr('stroke-width', 2)
                        
})

let svgWid = computed(() => {
    return rectWidth * barData.length
})

let svgHei = computed(() => {
    return (Math.ceil(movies.length / perRow) + 0.5) * pathWidth
})
</script>

<style>

</style>