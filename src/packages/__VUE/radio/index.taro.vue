<script lang="ts">
import { computed, h, inject } from 'vue';
import nutIcon from '../icon/index.taro.vue';
import radiogroup from '../radiogroup/index.vue';
import { createComponent } from '../../utils/create';
const { componentName, create } = createComponent('radio');

export default create({
  children: [radiogroup],
  components: {
    'nut-radiogroup': radiogroup
  },
  props: {
    disabled: {
      type: Boolean,
      default: false
    },
    label: {
      type: String,
      default: ''
    },
    iconName: {
      type: String,
      default: 'check-normal'
    },
    iconActiveName: {
      type: String,
      default: 'check-checked'
    },
    iconSize: {
      type: [String, Number],
      default: 18
    }
  },
  setup(props, { emit, slots }) {
    let parent: any = inject('parent');

    const isCurValue = computed(() => {
      return parent.label.value === props.label;
    });

    const color = computed(() => {
      return !props.disabled
        ? isCurValue.value
          ? 'nut-radio__icon'
          : 'nut-radio__icon--unchecked'
        : 'nut-radio__icon--disable';
    });

    const position = computed(() => {
      return parent.position;
    });

    const renderIcon = () => {
      const { iconName, iconSize, iconActiveName } = props;
      return h(nutIcon, {
        name: isCurValue.value ? iconActiveName : iconName,
        size: iconSize,
        class: color.value
      });
    };

    const renderLabel = () => {
      return h(
        'view',
        {
          class: `${componentName}__label ${
            props.disabled ? `${componentName}__label--disabled` : ''
          }`
        },
        slots.default?.()
      );
    };

    const handleClick = () => {
      if (isCurValue.value || props.disabled) return;
      parent.updateValue(props.label);
    };

    return () => {
      return h(
        'view',
        {
          class: `${componentName} ${
            position.value === 'left' ? `${componentName}--reverse` : ''
          }`,
          onClick: handleClick
        },
        [renderIcon(), renderLabel()]
      );
    };
  }
});
</script>

<style lang="scss">
@import 'index.scss';
</style>
