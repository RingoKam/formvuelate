<template>
  <div style="display: flex">
    <div>
      <textarea v-model="schema" class="editor" :class="{ 'editor-error': hasParseErrors }"/>
      <p v-if="hasParseErrors" style="color: red">The Schema is invalid. Must be valid JSON value</p>
    </div>
    <div>
      <SchemaForm :schema="parsedSchema" v-model="value"/>
      {{ value }}
    </div>
  </div>
</template>

<script>
import { ref, watchEffect } from 'vue'
import FormText from './form-elements/FormText.vue'
import FormSelect from './form-elements/FormSelect.vue'
import FormCheckbox from './form-elements/FormCheckbox.vue'

export default {
  components: {
    FormText, FormSelect, FormCheckbox
  },
  setup () {
    const schema = ref(JSON.stringify({
      firstName: {
        component: 'FormText',
        label: 'First Name',
      },
      lastName: {
        component: 'FormText',
        label: 'Last Name',
      },
      email: {
        component: 'FormText',
        label: 'Your email',
        required: true,
        config: {
          type: 'email'
        }
      },
    }, null, 2))
    const value = ref('')
    const hasParseErrors = ref(false)
    const parsedSchema = ref(JSON.parse(schema.value))

    watchEffect(() => {
      try {
        const parsingResult = JSON.parse(schema.value)
        parsedSchema.value = parsingResult
        schema.value = JSON.stringify(parsingResult, null, 2)
        hasParseErrors.value = false
      } catch (e) {
        hasParseErrors.value = true
      }
    })

    return {
      schema,
      parsedSchema,
      hasParseErrors,
      value
    }
  }
}
</script>

<style lang="stylus" scoped>
.editor
  min-width: 500px;
  min-height: 600px;
  white-space: pre;
  padding: 10px;
  margin-right: 20px;
  font-size: 1rem;

.editor-error
  border: 1px solid red;
</style>