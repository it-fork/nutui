<template>
  <view :class="classes" :style="position">
    <nut-overlay
      v-if="overlay"
      :visible="visible"
      :z-index="200"
      @click="updateValue(false)"
    />
    <slot name="list">
      <view class="nut-fixednav__list">
        <view
          class="nut-fixednav__list-item"
          v-for="(item, index) in navList"
          @click="selected(item, $event)"
          :key="item.id || index"
        >
          <img :src="item.icon" />
          <view class="span">{{ item.text }}</view>
          <view class="b" v-if="item.num">{{ item.num }}</view>
        </view>
      </view>
    </slot>
    <div class="nut-fixednav__btn" @click="updateValue()">
      <slot name="btn">
        <nut-icon name="left" color="#fff" />
        <view class="text">{{ visible ? activeText : unActiveText }}</view>
      </slot>
    </div>
  </view>
</template>
<script lang="ts">
import { computed } from 'vue';
import { createComponent } from '../../utils/create';
const { componentName, create } = createComponent('fixednav');

export default create({
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    overlay: {
      type: Boolean,
      default: true
    },
    navList: {
      default: () => [],
      type: Array
    },
    activeText: {
      default: '收起导航',
      type: String
    },
    unActiveText: {
      default: '快速导航',
      type: String
    },
    position: {
      default: () => {
        return {
          top: 'auto',
          bottom: 'auto'
        };
      },
      type: Object
    },
    type: {
      default: 'right',
      type: String
    }
  },
  components: {},
  emits: ['update:visible', 'selected'],

  setup(props, { emit }) {
    const classes = computed(() => {
      const prefixCls = componentName;
      return {
        [prefixCls]: true,
        active: props.visible,
        [props.type]: true
      };
    });

    const updateValue = (value: boolean = !props.visible) => {
      emit('update:visible', value);
    };
    const selected = (item: any, event: Event) => {
      emit('selected', {
        item,
        event
      });
    };

    return { classes, updateValue, selected };
  }
});
</script>

<style lang="scss">
@import 'index.scss';
</style>
