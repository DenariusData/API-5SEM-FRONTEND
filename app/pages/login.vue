<script setup lang="ts">
definePageMeta({
  layout: false
})

const email = ref('')
const senha = ref('')
const erro = ref('')
const carregando = ref(false)
const mostrarSenha = ref(false)

const emailValido = computed(() => /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value))
const formularioValido = computed(() => emailValido.value && senha.value.length >= 6)

async function login() {
  erro.value = ''
  carregando.value = true

  try {
    const { error } = await useFetch('/api/auth/login', {
      method: 'POST',
      body: { email: email.value, password: senha.value }
    })

    if (error.value) {
      erro.value = 'E-mail ou senha inválidos.'
      return
    }

    await navigateTo('/')
  } catch {
    erro.value = 'Erro ao conectar ao servidor.'
  } finally {
    carregando.value = false
  }
}
</script>

<template>
  <div class="relative flex min-h-screen items-center justify-center overflow-hidden bg-gray-50 dark:bg-gray-950">
    <div class="absolute right-4 top-4">
      <UColorModeButton />
    </div>

    <div class="absolute inset-0 -z-10">
      <div class="absolute left-1/4 top-1/4 size-96 rounded-full bg-primary/5 blur-3xl" />
      <div class="absolute bottom-1/4 right-1/4 size-96 rounded-full bg-primary/5 blur-3xl" />
    </div>

    <div class="w-full max-w-md px-4">
      <div class="mb-8 flex flex-col items-center gap-3">
        <AppLogo class="h-10 w-auto" />
        <div class="text-center">
          <h1 class="text-2xl font-bold text-gray-900 dark:text-white">
            Bem-vindo de volta
          </h1>
          <p class="mt-1 text-sm text-gray-500">
            Entre com suas credenciais para acessar o painel
          </p>
        </div>
      </div>

      <UCard class="shadow-xl">
        <div class="space-y-5 p-2">
          <UFormField
            label="E-mail"
            :error="email.length > 0 && !emailValido ? 'E-mail inválido' : undefined"
          >
            <UInput
              v-model="email"
              type="email"
              placeholder="seu@email.com"
              icon="i-lucide-mail"
              size="lg"
              class="w-full"
            />
          </UFormField>

          <UFormField label="Senha">
            <UInput
              v-model="senha"
              :type="mostrarSenha ? 'text' : 'password'"
              placeholder="••••••••"
              icon="i-lucide-lock"
              size="lg"
              class="w-full"
              @keyup.enter="formularioValido && login()"
            >
              <template #trailing>
                <UButton
                  :icon="mostrarSenha ? 'i-lucide-eye-off' : 'i-lucide-eye'"
                  variant="ghost"
                  size="xs"
                  @click="mostrarSenha = !mostrarSenha"
                />
              </template>
            </UInput>
          </UFormField>

          <UAlert
            v-if="erro"
            color="error"
            variant="subtle"
            icon="i-lucide-alert-circle"
            :description="erro"
          />

          <UButton
            size="lg"
            class="w-full justify-center"
            :loading="carregando"
            :disabled="!formularioValido || carregando"
            @click="login"
          >
            Entrar
          </UButton>
        </div>
      </UCard>
    </div>
  </div>
</template>
