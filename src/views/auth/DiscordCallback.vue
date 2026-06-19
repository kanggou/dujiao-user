<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useI18n } from 'vue-i18n'
import { useUserAuthStore } from '../../stores/userAuth'

const route = useRoute()
const router = useRouter()
const { t } = useI18n()
const userAuthStore = useUserAuthStore()

const status = ref<'loading' | 'error'>('loading')
const message = ref('')

onMounted(async () => {
  const code = typeof route.query.code === 'string' ? route.query.code : ''
  const state = typeof route.query.state === 'string' ? route.query.state : ''
  const savedState = sessionStorage.getItem('discord_oauth_state')
  sessionStorage.removeItem('discord_oauth_state')
  const redirectTarget = sessionStorage.getItem('discord_oauth_redirect') || '/me/orders'
  sessionStorage.removeItem('discord_oauth_redirect')

  if (route.query.error) {
    status.value = 'error'
    message.value = String(route.query.error_description || route.query.error)
    return
  }
  if (!code) {
    status.value = 'error'
    message.value = t('auth.login.discordLoginFailed')
    return
  }
  // CSRF 防护：state 必须与发起时一致
  if (!savedState || savedState !== state) {
    status.value = 'error'
    message.value = t('auth.login.discordStateMismatch')
    return
  }

  try {
    const result = await userAuthStore.discordLogin(code)
    if (result.requiresTotp) {
      router.replace({ path: '/auth/login', query: { discord_2fa: '1' } })
      return
    }
    router.replace(redirectTarget)
  } catch (err: any) {
    status.value = 'error'
    message.value = err?.message || t('auth.login.discordLoginFailed')
  }
})

const backToLogin = () => router.replace('/auth/login')
</script>

<template>
  <div class="flex min-h-[60vh] flex-col items-center justify-center gap-4 px-4 text-center">
    <template v-if="status === 'loading'">
      <div class="h-10 w-10 animate-spin rounded-full border-4 border-primary border-t-transparent"></div>
      <p class="text-muted-foreground">{{ t('auth.login.discordLoggingIn') }}</p>
    </template>
    <template v-else>
      <p class="text-lg font-medium text-destructive">{{ message }}</p>
      <button class="rounded-md bg-primary px-4 py-2 text-sm font-medium text-primary-foreground" @click="backToLogin">
        {{ t('auth.login.backToLogin') }}
      </button>
    </template>
  </div>
</template>
