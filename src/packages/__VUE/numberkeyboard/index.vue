<template>
  <nut-popup
    v-model:visible="show"
    position="bottom"
    @click-overlay="closeBoard()"
    overlay-class="nut-numberkeyboard-overlay"
  >
    <div class="nut-numberkeyboard" ref="root">
      <div class="number-board-header" v-if="title">
        <h3 class="tit">{{ title }}</h3>
        <span class="keyboard-close" @click="closeBoard()">完成</span>
      </div>
      <div class="number-board-body">
        <div class="number-board">
          <div
            :class="[
              'key-board-wrapper',
              {
                'key-board-wrapper-large':
                  item.id == 0 &&
                  type == 'rightColumn' &&
                  Array.isArray(customKey) &&
                  customKey.length == 1
              }
            ]"
            v-for="item of keysList"
            :key="'key' + item.id"
          >
            <div
              :class="[
                'key',
                { active: item.id == clickKeyIndex },
                { lock: item.type == 'lock' },
                { delete: item.type == 'delete' }
              ]"
              @touchstart="(event) => onTouchstart(item, event)"
              @touchmove="(event) => onTouchMove(item, event)"
              @touchend="onTouchEnd"
            >
              <template v-if="item.type == 'number' || item.type == 'custom'">{{
                item.id
              }}</template>
              <img
                v-if="item.type == 'lock'"
                src="https://img11.360buyimg.com/imagetools/jfs/t1/146371/38/8485/738/5f606425Eca239740/14f4b4f5f20d8a68.png"
              />
              <img
                v-if="item.type == 'delete'"
                src="https://img11.360buyimg.com/imagetools/jfs/t1/129395/8/12735/2030/5f61ac37E70cab338/fb477dc11f46056c.png"
              />
            </div>
          </div>
        </div>
        <div class="number-board-sidebar" v-if="type == 'rightColumn'">
          <div class="key-board-wrapper">
            <div
              :class="['key', { active: clickKeyIndex == 'delete' }]"
              @touchstart="
                (event) => onTouchstart({ id: 'delete', type: 'delete' }, event)
              "
              @touchmove="
                (event) => onTouchMove({ id: 'delete', type: 'delete' }, event)
              "
              @touchend="onTouchEnd"
            >
              <img
                src="https://img11.360buyimg.com/imagetools/jfs/t1/129395/8/12735/2030/5f61ac37E70cab338/fb477dc11f46056c.png"
              />
            </div>
          </div>
          <div
            class="key-board-wrapper"
            @click="closeBoard()"
            v-if="title == ''"
          >
            <div
              :class="[
                'key',
                'finish',
                { activeFinsh: clickKeyIndex == 'finish' }
              ]"
            >
              完成
            </div>
          </div>
        </div>
      </div>
    </div>
  </nut-popup>
</template>

<script lang="ts">
import {
  computed,
  onMounted,
  provide,
  reactive,
  nextTick,
  ref,
  watch,
  Ref
} from 'vue';
import { createComponent } from '../../utils/create';
const { create } = createComponent('numberkeyboard');
export default create({
  props: {
    title: {
      type: String,
      default: ''
    },
    visible: {
      type: Boolean,
      default: false
    },
    type: {
      type: String,
      default: 'default'
    },
    customKey: {
      type: Array,
      default: () => []
    },
    value: {
      type: String,
      default: ''
    },
    maxlength: {
      type: [Number, String],
      default: 6
    },
    randomKeys: {
      type: Boolean,
      default: false
    }
  },
  emits: ['input', 'delete', 'close', 'update:value'],
  setup(props, { emit }) {
    const clickKeyIndex = ref(undefined);
    const show = ref(props.visible);
    const root = ref<HTMLElement>();
    function defaultKey() {
      return [
        ...getBasicKeys(),
        { id: 'lock', type: 'lock' },
        { id: 0, type: 'number' },
        { id: 'delete', type: 'delete' }
      ];
    }

    function getBasicKeys() {
      const keys: any = [];
      for (let i = 1; i <= 9; i++) {
        keys.push({ id: i, type: 'number' });
      }
      if (props.randomKeys) {
        return keys.sort(() => (Math.random() > 0.5 ? 1 : -1));
      }

      return keys;
    }

    function genCustomKeys() {
      const keys = getBasicKeys();
      const { customKey } = props;
      let customKeys = Array.isArray(customKey) ? customKey : [customKey];
      if (customKeys.length > 2) {
        customKeys = [customKeys[0], customKeys[1]];
      }
      if (customKeys.length === 1) {
        if (props.title) {
          keys.push(
            { id: customKeys[0], type: 'custom' },
            { id: 0, type: 'number' },
            { id: 'delete', type: 'delete' }
          );
        } else {
          keys.push(
            { id: 0, type: 'number' },
            { id: customKeys[0], type: 'custom' }
          );
        }
      } else if (customKeys.length === 2) {
        keys.push(
          { id: customKeys[0], type: 'custom' },
          { id: 0, type: 'number' },
          { id: customKeys[1], type: 'custom' }
        );
        if (props.title) {
          keys.push({ id: 'delete', type: 'delete' });
        }
      } else {
        keys.push({ id: 0, type: 'number' });
      }
      return keys;
    }
    const keysList = computed(() => {
      if (props.type == 'rightColumn' || props.title != '') {
        return genCustomKeys();
      }
      return defaultKey();
    });
    watch(
      () => props.visible,
      (value) => {
        show.value = value;
      }
    );

    function onTouchstart(item: any, event: any) {
      event.stopPropagation();
      clickKeyIndex.value = item.id;
      if (item.type == 'number' || item.type == 'custom') {
        emit('input', item.id);
        if (props.value.length < props.maxlength) {
          emit('update:value', props.value + item.id);
        }
      }
      if (item.type == 'lock') {
        closeBoard();
      }
      if (item.type == 'delete') {
        emit('delete');
        emit('update:value', props.value.slice(0, props.value.length - 1));
      }
    }
    function onTouchMove(id: any, event: any) {
      event.stopPropagation();
    }
    function onTouchEnd() {
      clickKeyIndex.value = undefined;
    }

    function closeBoard() {
      emit('close');
    }

    onMounted(() => {});
    return {
      clickKeyIndex,
      defaultKey,
      closeBoard,
      onTouchEnd,
      onTouchMove,
      onTouchstart,
      keysList,
      genCustomKeys,
      getBasicKeys,
      root,
      show
    };
  }
});
</script>

<style lang="scss">
@import 'index.scss';
</style>
