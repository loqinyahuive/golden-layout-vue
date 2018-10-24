<template>
  <div>
    <button @click="add()">addGoldenLayout</button>
    <div id="golden" class="w-full h-screen"></div>
  </div>
</template>

<script>
import Vue from 'vue'
import GoldenLayout from 'golden-layout'
import FormComponent from "./FormComponent"

export default {
  components: { FormComponent },
  name: 'app',
  data() {
    return {
      myLayout: null
    }
  },

  methods: {
    add: function() {
      console.log('****** addLayout');
      let newConfig = {
        type: 'component',
        componentName: 'example',
        componentState: {
          message:"Top Right"
        }
      };
      // 不创建新的,而是直接插入.否则拖动面板的时候会重新绘制。导致多出窗口。
      // this.layout.createDragSource($(this.el.nativeElement).find("#layout"), newConfig);
      this.myLayout.root.contentItems[0].addChild(newConfig);
    }
  },
  mounted() {
    console.log('mounting....')
    console.log('App Store? ' + this.$store)
    console.log('App Store? ' + this.$store.getters.counter)

    var config = {
      content: [{
        type: 'row',
        content: [
          {
            type: 'component',
            componentName: 'example',
            componentState: { text: 'Component 1' }
          },
          {
            type: 'component',
            componentName: 'example',
            componentState: {
              firstName: 'Tom',
              lastName: 'De Waal'
            }
          }
        ]
      }]
    };

    var self = this
    this.myLayout = new GoldenLayout(config, $('#golden'));
    this.myLayout.registerComponent('example', function (container, state) {
      container.getElement().html('<div id="form-component"></div>');
      setTimeout(() => {
        const FormComponentConstructor = Vue.component('form-component').extend({
          store: self.$store
        });
        const vm = new FormComponentConstructor({
          propsData: {
            lastState: state || {},
            goldenLayoutContainer: container
          }
        });
        vm.$mount('#form-component');
      })
    });
    this.myLayout.init();
  }
}
</script>