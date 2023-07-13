<template>
  <div class="myform">
    <json-forms
      :data="data"
      :renderers="renderers"
      :schema="schema"
      :uischema="uischema"
      @change="onChange"
    />
    <button v-if="data.age > 18">NEXT</button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import { JsonForms, JsonFormsChangeEvent } from "@jsonforms/vue";
import {
  defaultStyles,
  mergeStyles,
  vanillaRenderers,
} from "@jsonforms/vue-vanilla";
import FormInput from './components/FormInput.vue'
import { rankWith, isStringControl } from '@jsonforms/core'
// mergeStyles combines all classes from both styles definitions into one
const myStyles = mergeStyles(defaultStyles, { control: { label: "mylabel" } });
import moment from 'moment';

import schema from './appdata/UserSchema.json';
import uischema from './appdata/UserUISchema.json';


const renderers = [
  ...vanillaRenderers,
  // here you can add custom renderers
  {
    tester: rankWith(
      2,
      isStringControl,
    ),
    renderer: FormInput,
  }
];

export default defineComponent({
  name: "App",
  components: {
    JsonForms,
  },
  data() {
    return {
      // freeze renderers for performance gains
      renderers: Object.freeze(renderers),
      data: {
        firstName: "",
        lastName: "",
        birthday:moment().format("YYYY-MM-DD"),
        email: "",
        age:0,
      },
      schema,
      uischema,
    };
  },
  methods: {
    onChange(event: JsonFormsChangeEvent) {
      this.data = event.data;
      this.ageCheck(event.data.birthday);
    },
    ageCheck (birthdate:string) {
      const computedAge = moment().diff(birthdate, 'years');
      this.data.age = computedAge;
    }
  },
  provide() {
    return {
      styles: myStyles,
    };
  },
});
</script>

<style>
body{
  background: #6bbaff4f;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-left: 120px;
  margin-right: 120px;
  width: 100%;
  background: #0072ff0a;
  border-radius: 25px;
  filter: drop-shadow(6px 3px 5px);
}

.mylabel {
  margin-right: 1em;
  display: inline-flex;
  min-width: 5em;
  color: white;
  font-family: monospace;
  font-size: 1.1em;
  font-weight: 800;
}

.vertical-layout {
  margin-left: 10px;
  margin-right: 10px;
}

.myform {
  width: 100%;
  margin: 0 auto;
}

.text-area {
  min-height: 80px;
}

.input{
    width: 230px;
    border-radius: 5px;
    height: 2em;
}
.control{
  display: inline-flex;
  margin-left: 1em;
}
</style>
