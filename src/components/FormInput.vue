<template>
    <b v-if="control.visible ">{{ control.label }}</b>
      <input
      :id="control.id + '-input'"
      :value="control.data"
      :errorMessage="control.errors"
      @change="onChange"
      :disabled="!control.enabled"
      v-if="control.visible "
      /> 
      <p v-if="control.visible ">{{ control.errors }}</p>
  </template>
  
  <script lang="ts">
  import {
    ControlElement,
    JsonFormsRendererRegistryEntry,
    rankWith,
    isStringControl,
  } from '@jsonforms/core';
  import { defineComponent } from 'vue';
  import {
    rendererProps,
    useJsonFormsControl,
    RendererProps,
  } from '@jsonforms/vue';
  
  const controlRenderer = defineComponent({
    name: 'FormInput',
    components: {
    },
    mounted() {
        
        console.log("control - ",this.control)
        console.log("enabled",this.$props.enabled)
        
    },
    props: {
      ...rendererProps<ControlElement>(),
    },
    setup(props: RendererProps<ControlElement>) {
        return useJsonFormsControl(props);
    },
    methods: {
    onChange(event: Event) {
      this.handleChange(
        this.control.path,
        (event.target as HTMLInputElement).value
      );
    },
  },
  });
  
  export default controlRenderer;
  
  export const entry: JsonFormsRendererRegistryEntry = {
    renderer: controlRenderer,
    tester: rankWith(1, isStringControl),
  };
  </script>