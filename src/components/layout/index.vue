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

const rectWidth = 50
const pathWidth = 150
const perRow = 7
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

//计算定位点函数
function calculateGridPos(i) {
  return [(i % perRow + 0.5) * pathWidth, (Math.floor(i / perRow) + 0.5) * pathWidth]
}
//计算分配颜色函数
const colorScale = d3.scaleOrdinal().domain(topGenres).range(petalColors).unknown(colorObj.Other)
//计算分配形状函数
const pathScale = d3.scaleOrdinal().range(petalPaths)
//计算花瓣缩放函数
const minMaxRating = d3.extent(movies, d => d.imdbRating)
const sizeScale = d3.scaleLinear().domain(minMaxRating).range([0.2, 0.75])
//计算花瓣数量函数
const minMaxVotes = d3.extent(movies, d => Number(d.imdbVotes.replace(/[,]/g,"")))
const numPetalScale = d3.scaleQuantize().domain(minMaxVotes).range([5,6,7,8,9,10])

let a = d3.scaleQuantize().domain([1,10]).range([1,2,3,4,5])
let flower = _.map(movies, (d, i) => {
    return { "color" : colorScale(d.Genre.split(",")[0]),
             "title" : d.Title,
             "translate" : calculateGridPos(i),
             "path" : pathScale(d.Rated),
             "scale" : sizeScale(d.imdbRating),
             "numpetal" : numPetalScale(Number(d.imdbVotes.replace(/[,]/g,"")))
            }
})

onMounted(() => {
      const svg = d3.select('#containter')
                    .selectAll('g')
                    .data(flower).enter()
                    .append('g')
                    .attr('transform', d => `translate(${d.translate})`)
        svg.selectAll('path').data(d => {
            return _.times(d.numpetal, i => {
            return Object.assign({}, d, { rotate: i * (360 / d.numpetal)})
        })
    }).enter().append('path')
              .attr('transform', d => `rotate(${d.rotate})scale(${d.scale})`)
              .attr('d', d => d.path)
              .attr('fill', d => d.color)
              .attr('stroke', d => d.color)
              .attr('fill-opacity', 0.75)
              .attr('stroke-width', 2)
 })

let svgWid = computed(() => {
    return rectWidth * barData.length
})

let svgHei = computed(() => {
    return (Math.ceil(movies.length / perRow) + 0.5) * pathWidth
})
</script>

<style scoped>

</style>