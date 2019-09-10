<template>
	<div id="app">
    <div class="box" ref="myTable">
      <vue-scrolling-table
        :scroll-horizontal="scrollHorizontal"
        :scroll-vertical="scrollVertical"
        :sync-header-scroll="syncHeaderScroll"
        :sync-footer-scroll="syncFooterScroll"
        :include-footer="includeFooter"
        :dead-area-color="deadAreaColor"
        :class="{ freezeFirstColumn:freezeFirstColumn }"
      >
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

var vm = null;

export default {
	name: "SampleApp",
	components: {
		VueScrollingTable,
	},
	data: function() {
		return {
      resizeEvent: null,
			scrollVertical: true,
			scrollHorizontal: true,
			syncHeaderScroll: true,
			syncFooterScroll: true,
			includeFooter: true,
			deadAreaColor: "#fff",
			maxRows: 100,
			freezeFirstColumn: true,
			columns: [],
      allItems: [],
      currentRow: null,
      currentCol: null,
      cellWidthMin: 80 // the min width of cell in table
		}
  },
  mounted() {
    vm = this;
    this.fetchData();
    this.resizeEvent = window.addEventListener('resize', this.resizeWindow);
    setTimeout(() => {
      this.resizeWindow();
    }, 500);

  },
  beforeDestroy() {
    if (this.resizeEvent) {
      window.removeEventListener('resize', this.resizeWindow);
      this.resizeWindow = null;
    }
  },
	computed: {
		items() {
			return this.allItems.slice(0, this.maxRows)
		},
  },
  methods: {
    resizeWindow() {
      const tb = vm.$refs.myTable;
      const cols = vm.columns.length;
      if (tb && cols) {
        const cellWidth = Math.max(tb.clientWidth / cols, vm.cellWidthMin);
        const ths = document.querySelectorAll(".box table.scrolling th");
        const tds = document.querySelectorAll(".box table.scrolling td");
        ths.forEach(th => {
          th.style.minWidth = `${cellWidth}px`;
        });
        tds.forEach(td => {
          td.style.minWidth = `${cellWidth}px`;
        });
        vm.scrollHorizontal = cellWidth == vm.cellWidthMin;
      }
    },
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
  background-color: #f8f8f8;
}

.box {
	padding: 0;
	height: 400px;
	margin-left: auto;
	margin-right: auto;
	overflow: hidden;
}

.box table.scrolling td, .box table.scrolling th {
  position: relative;
  cursor: pointer;
  border: none;
  padding: 0px;
}
.box table.scrolling td {
  height: 50px;
}

.box table.scrolling tr:nth-child(odd) td {
  background-color: #f0f0f0;
}

.box .cell-over {
  position: absolute;
  background-color: rgba(62, 131, 255, .3);
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
}
.box .cell-size {
  font-weight: 700;
  text-align: center;
}
.box .cell-inch, .box .cell-centi {
  text-align: center;
  line-height: 18px;
}

.box .cell-symbol {
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  background-color: #3e83ff;
  color: white;
  margin-top: -30px;
  position: absolute;
  /* top: 15px; */
}
.box table.scrolling th {
  height: 60px;
  vertical-align: bottom;
  background-color: #fff;
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
  height: 400px;
  min-height: 300px;
  display: flex;
  justify-content: center;
  align-items: center; 
  overflow: hidden; 
}
.wrapper {
  position: relative;
  border-radius: 10px;
  border: 1px solid rgba(0, 0, 0, 0.3);
  overflow: hidden;
}
.responsive-img {
  max-width: 100%;
  height: auto;
  max-height: 100%;
}


::-webkit-scrollbar {
width: 5px; 
height: 5px;
background: transparent; 
}
::-webkit-scrollbar-thumb {
  background: #aaa;
}





</style>