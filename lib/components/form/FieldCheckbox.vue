<!-- *************************************************************************
     TEMPLATE
     ************************************************************************* -->

<template lang="pug">
div(
  :class=`[
    "dm-field-checkbox",
    "dm-field-checkbox--" + computedStatus,
    "dm-field-checkbox--" + size,
    {
      "dm-field-checkbox--disabled": disabled,
      "dm-field-checkbox--full-width": fullWidth
    }
  ]`
)
  .dm-field-checkbox__container
    input(
      v-validate="validation"
      @change="onFieldChange"
      :checked="currentValue"
      :data-vv-as="validationVvAs"
      :disabled="disabled"
      :id="uuid"
      :name="name"
      :ref="validationRef"
      class="dm-field-checkbox__field"
      type="checkbox"
    )
    field-label(
      v-if="label"
      :forField="uuid"
      :required="validation && validation.includes('required')"
      :size="size"
      :uppercase="false"
      class="dm-field-checkbox__label"
    ) {{ label }}

  field-message(
    v-if="computedMessageLevel"
    :level="computedMessageLevel"
    :message="computedMessageContent"
    :size="size"
  )
</template>

<!-- *************************************************************************
     SCRIPT
     ************************************************************************* -->

<script>
// PROJECT
import { generateUUID } from "../../helpers/helpers.js";
import FieldLabel from "./FieldLabel.vue";
import FieldCommonMixin from "../../mixins/FieldCommonMixin.js";
import FieldMessageMixin from "../../mixins/FieldMessageMixin.js";
import FieldValidationMixin from "../../mixins/FieldValidationMixin.js";

export default {
  components: {
    FieldLabel
  },

  mixins: [FieldCommonMixin, FieldMessageMixin, FieldValidationMixin],

  props: {
    fullWidth: {
      type: Boolean,
      default: true
    },
    label: {
      type: String,
      default: null
    },
    value: {
      type: Boolean,
      default: false
    }
  },

  data() {
    return {
      // --> STATE <--

      currentValue: null,
      uuid: ""
    };
  },

  watch: {
    value: {
      immediate: true,
      handler(value) {
        this.currentValue = value;
      }
    }
  },

  mounted() {
    this.uuid = generateUUID();
  },

  methods: {
    // --> EVENT LISTENERS <--

    onFieldChange(event) {
      this.currentValue = event.target.checked;

      this.$emit("change", event.target.checked, this.name, event);
      this.$emit("input", event.target.checked); // Synchronization for v-model
    }
  }
};
</script>

<!-- *************************************************************************
     STYLE
     ************************************************************************* -->

<style lang="scss">
// IMPORTS
@import "assets/settings/_settings.colors.scss";

// VARIABLES
$c: ".dm-field-checkbox";
$sizes: mini, small, default, medium, large;
$statuses: error, normal, success, warning;

#{$c} {
  display: inline-block;
  text-align: left;
  font-family: "Heebo", "Helvetica Neue", Source Sans Pro, Helvetica, Arial,
    sans-serif;

  #{$c}__container {
    display: flex;

    #{$c}__field {
      position: relative;
      margin-bottom: 0;
      outline: 0;
      border: none;
      border-radius: 2px;
      transition: all ease-in-out 250ms;
      -webkit-appearance: none;
      cursor: pointer;

      &:before,
      &:after {
        position: absolute;
        display: inline-block;
        box-sizing: border-box;
        transition: all ease-in-out 250ms;
      }

      &:before {
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        border: 1px solid $regent-st-blue;
        border-radius: 3px;
        background-color: $white;
        content: "";
      }

      &:after {
        border: 2px solid $white;
        border-top: 0;
        border-left: 0;
        transform: rotate(45deg);
        content: "";
      }

      &:hover {
        &:after {
          border-color: $oxford-blue;
          border-right-width: 2px;
          border-bottom-width: 2px;
        }
      }

      &:checked {
        &:after {
          border-color: $white;
        }
      }
    }

    #{$c}__label {
      flex: 1;
      margin-bottom: 0;
      color: $white;
      font-weight: 400;
    }
  }

  // --> SIZES <--

  @each $size in $sizes {
    $i: index($sizes, $size) - 1;

    &--#{$size} {
      #{$c}__container {
        #{$c}__field {
          width: 12px + (2px * $i);
          height: 12px + (2px * $i);
          margin-right: 6px + (1px * $i);

          &:after {
            @if ($size == mini) {
              top: 1px;
              left: 4px;
              width: 4px;
              height: 8px;
            } @else if ($size == small) {
              top: 2px;
              left: 5px;
              width: 4px;
              height: 8px;
            } @else if ($size == default) {
              top: 2px;
              left: 6px;
              width: 5px;
              height: 10px;
            } @else if ($size == medium) {
              top: 3px;
              left: 7px;
              width: 5px;
              height: 10px;
            } @else if ($size == large) {
              top: 3px;
              left: 8px;
              width: 6px;
              height: 12px;
            }
          }
        }

        #{$c}__label {
          line-height: 12px + (2px * $i);
        }
      }
    }
  }

  // --> STATUSES <--

  @each $status in $statuses {
    &--#{$status} {
      #{$c}__container {
        #{$c}__field {
          &:hover {
            &:before {
              border-color: map-get($statusColors, $status);
            }
          }

          &:checked {
            &:before {
              border-color: map-get($statusColors, $status);
              background: map-get($statusColors, $status);
            }
          }

          &:focus {
            box-shadow: 0 0 0 2px $mirage,
              0 0 0 3px map-get($statusColors, $status);
            transition: box-shadow ease-in-out 0s;
          }
        }
      }
    }
  }

  // --> BOOLEANS <--

  &--disabled {
    opacity: 0.7;

    #{$c}__container {
      #{$c}__field,
      #{$c}__label {
        cursor: not-allowed;
      }
    }
  }

  &--full-width {
    width: 100%;
  }
}
</style>
