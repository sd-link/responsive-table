<template>
	<div id="app">
    <div class="box">
      <vue-scrolling-table
        :scroll-horizontal="scrollHorizontal"
        :scroll-vertical="scrollVertical"
        :sync-header-scroll="syncHeaderScroll"
        :sync-footer-scroll="syncFooterScroll"
        :include-footer="includeFooter"
        :dead-area-color="deadAreaColor"
        :class="{ freezeFirstColumn:freezeFirstColumn }">
        <template slot="thead">
          <tr>
            <th v-for="(col, indexCol) in columns" 
            :class="col.cssClasses"
            :key="col.id"
            @mouseover="setCurrent(0, indexCol)"
          >
      
            <div class="cell-header">
              <div v-if="indexCol" class="cell-symbol">
                {{ col.symbol }}
              </div>     
              <span>{{ col.title }}</span>
              <div v-if="indexCol==currentCol" class="cell-over">
              </div>
            </div>
          
          </th>
          </tr>
        </template>
        <template slot="tbody">
          <tr v-for="(item, indexRow) in items" :key="item.id">
            <td v-for="(col, indexCol) in columns"
              :class="col.cssClasses"
              :key="col.id"
              @mouseover="setCurrent(indexRow, indexCol)"
            >
              <div v-if="indexCol">
                <div class="cell-inch">{{ toInch(item[col.id]) }}"</div>
                <div class="cell-centi">{{ item[col.id] }}cm</div>
              </div>
              <div v-else class="cell-size">
                {{ item[col.id] }}
              </div>
              <div v-if="indexRow==currentRow" class="cell-over">
              </div>
              <div v-if="indexCol==currentCol" class="cell-over">
              </div>
              
            </td>
          </tr>
        </template>
      </vue-scrolling-table>
    </div>
	</div>
</template>
<script>
import VueScrollingTable from "vue-scrolling-table";
import axios from 'axios'

export default {
	name: "SampleApp",
	components: {
		VueScrollingTable,
	},
	data: function() {
		return {
			scrollVertical: true,
			scrollHorizontal: true,
			syncHeaderScroll: true,
			syncFooterScroll: true,
			includeFooter: true,
			deadAreaColor: "#DDDDDD",
			maxRows: 100,
			freezeFirstColumn: true,
			columns: [],
      allItems: [],
      currentRow: null,
      currentCol: null
		}
  },
  mounted() {
    this.fetchData();
  },
	computed: {
		items() {
			return this.allItems.slice(0, this.maxRows)
		},
  },
  methods: {
    toInch(centi) {
      return (centi * 0.393701).toFixed(1);
      
    },
    setCurrent(row, col) {
      this.currentRow = row;
      this.currentCol = col || 1;
    },
    fetchData(){
      axios.get('table-data.json').then(response => {
        const { data } = response;
        if (data) {
          this.columns = data.columns;
          this.allItems = data.allItems;
        }
      })
    }
  }
}
</script>
<style>


table.freezeFirstColumn thead tr,
table.freezeFirstColumn tbody tr {
	display: block;
	width: min-content;
}
table.freezeFirstColumn thead td:first-child,
table.freezeFirstColumn tbody td:first-child,
table.freezeFirstColumn thead th:first-child,
table.freezeFirstColumn tbody th:first-child {
	position: sticky;
	position: -webkit-sticky;
	left: 0;
  z-index: 2;
}

.box {
	padding: 0;
	min-height: 300px;
	height: 40vh;
	margin-left: auto;
	margin-right: auto;
	overflow: hidden;
  border-radius: 10px;
}

.box table.scrolling td, .box table.scrolling th {
  position: relative;
  cursor: pointer;
  border: none;
  min-width: 100px;
}
.box table.scrolling td {
  height: 50px;
}

.box table.scrolling tr:nth-child(odd) td {
  background-color: #f0f0f0;
}

.box .cell-over {
  position: absolute;
  background-color: rgba(0, 0, 0, .3);
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}
.box .cell-size {
  font-weight: 700;
  font-size: 22px;
  text-align: center;
}
.box .cell-inch, .box .cell-centi {
  text-align: center;
}

.box .cell-symbol {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  background-color: black;
  color: white;
  margin-top: -30px;
  position: absolute;
  /* top: 15px; */
}
.box table.scrolling th {
  height: 60px;
  vertical-align: bottom;
  background-color: white;
}
.box .cell-header {
  display: flex;
  position: relative;
  flex-direction: column;
  align-items: center;
  justify-content: flex-end;
  height: 70px;
  padding: 5px;
}
.box thead {
  --dead-area-color: #fff;
}
.img-wrapper {
  height: 300px;
  background: #f0f0f0;
  height: 100%;
  min-height: 300px
}
.wrapper {
  position: relative;
  border: 1px solid #f0f0f0;
  border-radius: 10px;
}







</style>