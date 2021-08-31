<template>
  <div class="p-4 space-y-4">
    <h2 class="text-lg font-bold">Demo: Form</h2>
    <TForm
      v-model="data"
      :fields="fields"
      :field-config="{ labelPosition: 'top' }"
      class="space-y-4"
      submit-label="Submit"
      @save="submit"
    />
  </div>
</template>

<script>
import {
  required,
  email,
  minLength,
  alphaNum,
  helpers
} from '@vuelidate/validators'
import { ref } from '@nuxtjs/composition-api'
import { useDoc } from '~/use/doc'
export default {
  name: 'Form',
  setup() {
    const { find, exists } = useDoc('profiles')
    const data = ref({
      name: '',
      email: '',
      username: '',
      place: ''
    })
    const isUnique = async (value) => {
      await find('username', value)
      if (exists.value) {
        return false
      } else {
        return true
      }
    }
    const ErrorMessages = {
      name: {
        required: 'name is a required field',
        minLength: 'name must be at least 3 characters'
      },
      email: {
        required: 'email is a required field',
        email: 'email must be valid'
      },
      username: {
        required: 'username is a required field',
        minLength: 'username must be at least 3 characters',
        isUnique: 'username must be unique',
        alphaNum: 'username must be alphanumerics'
      },
      place: {
        required: 'place is a required field'
      }
    }
    const fields = [
      {
        name: 'name',
        key: 'account.name',
        validations: {
          required: helpers.withMessage(ErrorMessages.name.required, required),
          minLength: helpers.withMessage(
            ErrorMessages.name.minLength,
            minLength(3)
          )
        }
      },
      {
        name: 'email',
        key: 'account.email',
        validations: {
          required: helpers.withMessage(ErrorMessages.email.required, required),
          email: helpers.withMessage(ErrorMessages.email.email, email)
        }
      },
      {
        name: 'username',
        key: 'profile.username',
        type: 'username',
        before: 'Use only letters, numbers, underscores and periods.',
        validations: {
          required: helpers.withMessage(
            ErrorMessages.username.required,
            required
          ),
          alphaNum: helpers.withMessage(
            ErrorMessages.username.alphaNum,
            alphaNum
          ),
          minLength: helpers.withMessage(
            ErrorMessages.username.minLength,
            minLength(4)
          ),
          isUnique: helpers.withMessage(
            'This username is not unique',
            helpers.withAsync(isUnique)
          )
        }
      },
      {
        name: 'place',
        label: 'Your city',
        type: 'place',
        placeholder: 'City',
        validations: {
          required: helpers.withMessage(ErrorMessages.place.required, required)
        }
      }
    ]
    return {
      data,
      fields,
      find,
      exists
    }
  },
  methods: {
    submit(data) {
      console.log('submited', data)
    }
  }
}
</script>
