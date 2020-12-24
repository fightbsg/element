<template>
  <div class="el-tabs__active-bar" :class="`is-${ rootTabs.tabPosition }`" :style="barStyle"></div>
</template>
<script>
  import { arrayFind } from 'element-ui/src/utils/util';
  export default {
    name: 'TabBar',

    props: {
      tabs: Array
    },

    inject: ['rootTabs'],

    computed: {
      barStyle: {
        get() {
          let style = {};
          let offset = 0;
          let tabSize = 0;
          const sizeName = ['top', 'bottom'].indexOf(this.rootTabs.tabPosition) !== -1 ? 'width' : 'height';
          const sizeDir = sizeName === 'width' ? 'x' : 'y';
          const firstUpperCase = str => {
            return str.toLowerCase().replace(/( |^)[a-z]/g, (L) => L.toUpperCase());
          };
          const isHorizon = sizeName === 'width';

          this.tabs.every((tab) => {
            let $el = arrayFind(this.$parent.$refs.tabs || [], t => t.id.replace('tab-', '') === tab.paneName);
            if (!$el) { return false; }

            const tabStyles = window.getComputedStyle($el);
            if (!tab.active) {
              offset += $el[`client${firstUpperCase(sizeName)}`];
              if (isHorizon) {
                offset += parseFloat(tabStyles.marginLeft) + parseFloat(tabStyles.marginRight);
              } else {
                offset += parseFloat(tabStyles.marginTop) + parseFloat(tabStyles.marginBottom);
              }
              return true;
            } else {
              tabSize = $el[`client${firstUpperCase(sizeName)}`];
              if (isHorizon && this.tabs.length > 1) {
                tabSize -= parseFloat(tabStyles.paddingLeft) + parseFloat(tabStyles.paddingRight);
              }
              if (isHorizon) {
                offset += parseFloat(tabStyles.paddingLeft);
                offset += parseFloat(tabStyles.marginLeft);
              } else {
                offset += parseFloat(tabStyles.marginTop);
              }
              return false;
            }
          });

          const transform = `translate${firstUpperCase(sizeDir)}(${offset}px)`;
          style[sizeName] = tabSize + 'px';
          style.transform = transform;
          style.msTransform = transform;
          style.webkitTransform = transform;

          return style;
        }
      }
    }
  };
</script>
