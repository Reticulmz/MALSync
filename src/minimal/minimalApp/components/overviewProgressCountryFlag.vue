<template>
  <div class="flagIcon mdl-shadow--2dp" v-if="country" :title="text">
    <div v-html="flagHtml" v-if="flagHtml" class="flagflag"></div>
    <div v-else class="flagCountry">{{country}}</div>
    <div class="flagText" v-if="text !== 'SUB'">{{text}}</div>
  </div>
</template>

<script>
import {Cache} from './../../../utils/Cache';
export default {
  data: function(){
    return {
      flagHtml: '',
    }
  },
  props: {
    country: {
      type: String,
      required: true
    },
    text: {
      type: String,
      default: ''
    },
  },
  watch: {
    country: {
      immediate: true,
      async handler(newVal, oldVal) {
        var cache = new Cache('flag/'+newVal, (48 * 60 * 60 * 1000));
        if(await cache.hasValue()){
          cache.getValue().then(val => this.flagHtml = val);
        }else{
          return api.request.xhr('GET', 'https://raw.githubusercontent.com/lipis/flag-icon-css/master/flags/4x3/'+newVal+'.svg').then((response) => {
            if(response.responseText && response.status === 200) {
              this.flagHtml = response.responseText;
              cache.setValue(response.responseText);
            }else{
              cache.setValue(null);
            }

          });

        }
      }
    }
  }
}
</script>

<style lang="less" scoped>

</style>
