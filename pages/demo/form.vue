<template>
  <div class="p-4 space-y-4">
    <h2 class="text-lg font-bold">Demo: Form</h2>
    <TForm
      v-model="data"
      :fields="fields"
      class="space-y-4"
      submit-label="Submit"
      @save="submit"
    />
  </div>
</template>

<script>
import { required, email, alphaNum, minLength } from 'vuelidate/lib/validators'
import { ref } from '@nuxtjs/composition-api'

export default {
  setup() {
    const data = ref({
      name: '',
      email: '',
      username: '',
      place: ''
    })
    const fields = [
      {
        name: 'name',
        key: 'account.name',
        validations: { required, minLength: minLength(3) }
      },
      {
        name: 'email',
        key: 'account.email',
        validations: { required, email }
      },
      {
        name: 'username',
        key: 'profile.username',
        type: 'username',
        before: 'Use only letters, numbers, underscores and periods.',
        validations: { required, alphaNum }
      },
      {
        name: 'place',
        label: 'Your city',
        type: 'place',
        placeholder: 'City',
        validations: { required }
      }
    ]
    return {
      data,
      fields
    }
  },
  methods: {
    submit(data) {
      console.log('submited', data)
    }
  }
}
</script>
