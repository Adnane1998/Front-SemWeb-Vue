<template>

  <div class="window border border-secondary rounded p-2 mb-2" :class="{expanded: isExpanded}">
    
    <div class="top-row d-flex" @click="toggleExpand">
      
      <div class="window-name fw-bold pe-3">{{window.hostRoom}}</div>
      <div class="room-name text-muted  pe-3">{{window.resultTime}}h   </div>
      
       <div class="room-name text-muted pe-3">Inside:{{window.result}} °C</div>
       <div class="room-name text-muted pe-3">Outside:{{window.result_weather}}</div>
      

      <div class="open-status ms-4" :class="{open: isWindowOpen, closed: !isWindowOpen}">
        <template v-if="isWindowOpen">
          <span class="icon">&#x2B24;</span> Interest Of
        </template>
        <template v-if="!isWindowOpen">
          <span class="icon">&#x2716;</span> Alarming
        </template>
      </div>
        <div class="opens-status ms-4" :class="{opens: isNormal}">
        <template v-if="isNormal">
          <span class="icon">&#x2B24;</span> Normal
        </template>
     
      </div>

      <div class="expand-button ms-auto">
        {{ isExpanded ? '&#9660;' : '&#9658;' }}
      </div>
    </div>
 
  </div>
</template>

<script>
import axios from 'axios';
import {API_HOST} from '../config';

export default {
  name: 'WindowsListItem',
  props: ['window'],
  data: function() {
    return {
      isExpanded: false,
      weather:[],
      difference:0
    }
  }, 
  computed: {
    isWindowOpen: function() {
console.log(this.result_weather);
console.log( Number(Math.round(this.window.result.replace('°C',''))));
var difference = Number(this.window.result.replace('°C','')) - Number(this.window.result_weather.replace('°C',''))


if( difference>15)
{
      return false ; 
}
else if( difference>=12 && difference <15)
{
      return true ; 
}
else return true

    
    
    },
        isNormal: function() {

console.log( Number(Math.round(this.window.result.replace('°C',''))));
var difference = Number(this.window.result.replace('°C','')) - Number(this.window.result_weather.replace('°C',''))
console.log(difference);

if( difference>10.00  && difference<12.00 )
{
      return true ; 
      
}

    
    
    }
    
  
  },
 
  methods: {
    toggleExpand() {
      this.isExpanded = !this.isExpanded;
    },
    async switchWindow() {
      let response = await axios.put(`${API_HOST}/api/windows/${this.window.id}/switch`);
      let updatedWindow = response.data;
      this.$emit('window-updated', updatedWindow);
    }
  }
}
</script>

<style lang="scss" scoped>

.open-status {
  .icon {
    position: relative;
  }

  &.open {
    color: #198754;
    .icon {
      font-size: 12px;
      top: -3px;
    }
  }

  &.closed {
    color: #dc3545;
  }
}

.window {
  .top-row {
    cursor: pointer;
  }
}
</style>
