<template>
    <fieldwrapper>
        <klabel v-if="control.visible" >
        {{control.label}} 
        </klabel>
      <KInput
        :style="{ width: '230px' }"
        :id="control.id + '-input'"
        :errorMessage="control.errors"
        @change="onChange"
        :disabled="false"
        v-if="control.visible "
      ></KInput>
        <error v-if="control.visible">
        {{control.errors}}
        </error>
    </fieldwrapper>
  </template>
  
  <script lang="ts">
  import { FieldWrapper } from "@progress/kendo-vue-form";
  import { Error, Hint, Label } from "@progress/kendo-vue-labels";
  import { Input } from '@progress/kendo-vue-inputs';
  import '@progress/kendo-theme-default';
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
        KInput: Input,
        fieldwrapper: FieldWrapper,
        error: Error,
        hint: Hint,
        klabel: Label,
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
<style>
.k-label{
    margin: 1em;
    display: inline-flex;
    min-width: 5em;
    color: white;
    font-family: monospace;
    font-size: 1.1em;
    font-weight: 800;
}
.k-form-error{
    justify-content: center;
}
</style>