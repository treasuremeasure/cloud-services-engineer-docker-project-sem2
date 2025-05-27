<template>

  <Alert v-if="error_message" v-model="error_message" class="alert-danger" />

  <Alert v-if="success_message" v-model="success_message" class="alert-success" :dismissible="false"/>

  <form v-else @submit.prevent="handleSubmit" class="my-3">
    <div class="mb-2">
      <input 
        type="password" 
        v-model="oldPassword" 
        class="form-control" 
        placeholder="Old Password" 
        :disabled="loading"
        required
      />
    </div>
    <div class="mb-2">
      <input 
        type="password" 
        v-model="password" 
        class="form-control" 
        placeholder="New password" 
        ref="password" 
        @input="handlePasswordInput"
        :disabled="loading"
        required 
      />
    </div>
    <div class="mb-2">
      <input 
        type="password" 
        v-model="passwordConfirmation" 
        class="form-control" 
        placeholder="Confirm the new password" 
        ref="passwordConfirmation" 
        @input="handlePasswordInput"
        :disabled="loading"
        required 
      />
    </div>
    <div class="d-flex justify-content-end">
      <Center v-if="loading" class="mx-2">
        <Spinner size="2rem" thickness="0.75rem" />
      </Center>
      <button
        type="button" 
        class="btn btn-secondary me-3" 
        @click="$emit('close')"
        :disabled="loading"
      >Cancel</button>
      <button 
        type="submit" 
        class="btn btn-primary"
        :disabled="loading"
      >Change password</button>
    </div>
  </form>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from 'axios'                        
import { AxiosError } from 'axios'

import Spinner from '@/components/misc/Spinner.vue'
import Center  from '@/components/misc/Center.vue'
import Alert   from '@/components/misc/Alert.vue'

export default defineComponent({
  name: 'ChangePassword',
  components: { Spinner, Center, Alert },
  emits: ['close'],
  data() {
    return {
      oldPassword: '',
      password: '',
      passwordConfirmation: '',
      loading: false,
      error_message: '',
      success_message: ''
    }
  },
  methods: {
    handleSubmit() {
      this.loading = true

      this.$store.dispatch('changePassword', {
        old_password: this.oldPassword,
        new_password: this.password
      })
      .then((data: any) => {
        this.success_message = data.detail
        setTimeout(() => {
          this.$emit('close')
          this.$router.push('/login')
        }, 3000)
      })
      .catch((err: unknown) => {                 
        if (axios.isAxiosError(err) && err.response) {
          this.error_message = err.response.data.detail
        } else {
          this.error_message = 'Connection error'
        }
      })
      .finally(() => {
        this.loading = false
      })
    },
    handlePasswordInput() {
      const confirmation = this.$refs.passwordConfirmation as HTMLInputElement
      confirmation.setCustomValidity(
        this.password === this.passwordConfirmation ? '' : 'Passwords do not match'
      )
    }
  }
})
</script>
