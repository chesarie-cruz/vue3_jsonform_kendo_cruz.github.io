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

const schema = {
  properties: {
    firstName: {
      type: "string",
      description: "First Name",
      
    },
    lastName: {
      title: "Last Name",
      type: "string",
    },
    age: {
      type: "number",
      default:0,
    },
    birthday: {
      type: "string",
      format:"date",
    },
    email: {
      type: "string",
      format: 'email',
      pattern: '.*example\\.com'
    }
  },
};

const uischema = {
  type: "Categorization",
  elements: [
    {
      "type": "Category",
      "label":"Basic Info",
      "elements": [
      {
          type: "Control",
          scope: "#/properties/firstName",
          label:"fname",
          rule: {
            "effect": "HIDE",
            "condition": {
              "scope": "#/properties/firstName",
              "schema": { enum: ["foo", "bar"] }
            }
          }
        },
        {
          type: "Control",
          scope: "#/properties/lastName",
          label:"Lname",
          rule: {
          "effect": "DISABLE",
          "condition": {
            "scope": "#/properties/lastName",
            "schema": { enum: ["foo", "2017-12-18"] }
          }
      }
        },
        {
          "type": "Control",
          "label": "birthday",
          "scope": "#/properties/birthday"
        },
        {
          "type": "Control",
          "scope": "#/properties/age",
          "rule": {
            "effect": "HIDE",
            "condition": {
              "scope": "#/properties/age",
            }
          }
        },
        {
          "type": "Control",
          "label": "email",
          "scope": "#/properties/email",
          "rule": {
            "effect": "HIDE",
            "condition": {
              "scope": "#/properties/age",
              "schema": {
                "enum": [0,1,2,3,4,5,6,7,8,9,10,11,12],
              }
            }
          }
        }
      ]
    },
    {
      "type": "Category",
      "label": "Additional",
      "elements": [],
    },
  ]
};

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
        lastName: "Last Name",
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
      console.log(event)
      this.data = event.data;
      this.ageCheck(18,event.data.birthday)
    },
    ageCheck (minAge:number=18,birthdate:string) {
      console.log("minAge",minAge)
      console.log("birthdate",birthdate)
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
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  margin-left: 120px;
  margin-right: 120px;
}

.mylabel {
  color: darkslategrey;
}

.vertical-layout {
  margin-left: 10px;
  margin-right: 10px;
}

.myform {
  width: 640px;
  margin: 0 auto;
}

.text-area {
  min-height: 80px;
}
</style>
