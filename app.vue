<template>
  <div class="min-h-screen bg-gradient-to-br from-gray-900 via-purple-900 to-violet-800 py-6 flex flex-col justify-center sm:py-12">
    <div class="relative px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto w-full">
      <!-- Container principal avec flex sur desktop -->
      <div class="flex flex-col lg:flex-row lg:items-center lg:space-x-8 space-y-8 lg:space-y-0">
        
        <!-- Carte bancaire - Prend 40% de l'espace sur desktop -->
        <div class="lg:w-2/5">
          <div class="relative group max-w-md mx-auto lg:ml-0">
            <div class="absolute -inset-1 bg-gradient-to-r from-pink-600 to-purple-600 rounded-lg blur opacity-25 group-hover:opacity-100 transition duration-1000 group-hover:duration-200"></div>
            <div class="relative w-full h-56 rounded-xl shadow-2xl overflow-hidden backdrop-blur-sm transition-all duration-500 transform hover:scale-105"
                 :class="[
                   cardType === 'visa' ? 'bg-gradient-to-br from-blue-600 to-blue-800' : 
                   cardType === 'mastercard' ? 'bg-gradient-to-br from-red-600 to-orange-600' : 
                   'bg-gradient-to-br from-gray-700 to-gray-900'
                 ]">
              <!-- Effet de brillance -->
              <div class="absolute inset-0 opacity-30 bg-[radial-gradient(ellipse_at_50%_40%,_rgba(255,255,255,0.8),transparent_70%)]"></div>
              
              <!-- Cercles décoratifs -->
              <div class="absolute -right-20 -top-20 w-60 h-60 rounded-full bg-white opacity-5"></div>
              <div class="absolute -left-20 -bottom-20 w-60 h-60 rounded-full bg-white opacity-5"></div>
              
              <!-- Logo de la carte -->
              <div class="absolute top-4 right-4 transition-transform duration-300 hover:scale-110">
                <div class="relative">
                  <div class="absolute inset-0 bg-white/20 blur-sm rounded-lg"></div>
                  <img v-if="cardType === 'visa'" src="/visa-logo.svg" class="w-16 h-auto relative z-10" alt="Visa" />
                  <img v-else-if="cardType === 'mastercard'" src="/Mastercard-logo.svg" class="w-16 h-auto relative z-10" alt="Mastercard" />
                  <div v-else class="w-16 h-10 bg-gray-400/30 backdrop-blur rounded relative z-10"></div>
                </div>
              </div>
              
              <!-- Puce -->
              <div class="absolute top-12 left-8">
                <div class="w-12 h-10 rounded-md bg-gradient-to-br from-yellow-200 via-yellow-400 to-yellow-600">
                  <div class="w-full h-full bg-[linear-gradient(90deg,transparent_40%,rgba(255,255,255,0.2)_45%,rgba(255,255,255,0.2)_55%,transparent_60%)] rounded-md"></div>
                </div>
              </div>
              
              <!-- Numéro de carte -->
              <div class="absolute top-24 left-8 right-8">
                <div class="text-white text-xl tracking-wider font-mono backdrop-blur-sm bg-white/5 rounded-lg px-2 py-1">
                  {{ displayCardNumber || '•••• •••• •••• ••••' }}
                </div>
              </div>
              
              <!-- Informations du bas -->
              <div class="absolute bottom-6 left-8 right-8 flex justify-between items-center space-x-4">
                <div class="backdrop-blur-sm bg-white/5 rounded-lg p-2 transition-all duration-300 hover:bg-white/10 flex-1">
                  <div class="text-xs uppercase text-gray-300">Titulaire</div>
                  <div class="font-medium text-white truncate">{{ cardHolder || 'VOTRE NOM' }}</div>
                </div>
                <div class="backdrop-blur-sm bg-white/5 rounded-lg p-2 transition-all duration-300 hover:bg-white/10">
                  <div class="text-xs uppercase text-gray-300">Expire</div>
                  <div class="font-medium text-white">{{ expiryDate || 'MM/YY' }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Formulaire - Prend 60% de l'espace sur desktop -->
        <div class="lg:w-3/5">
          <div class="relative">
            <div class="absolute -inset-1 bg-gradient-to-r from-pink-600 to-purple-600 rounded-2xl blur opacity-25"></div>
            <div class="relative px-6 py-8 bg-white/10 backdrop-blur-xl rounded-xl shadow-2xl">
              <div class="max-w-lg mx-auto">
                <form @submit.prevent="handleSubmit" class="space-y-6">
                  <!-- Numéro de carte -->
                  <div class="space-y-2">
                    <label class="text-sm font-medium text-gray-200">
                      Numéro de carte
                    </label>
                    <input
                      v-model="cardNumber"
                      type="text"
                      maxlength="19"
                      @input="handleCardNumberInput"
                      :class="[
                        'mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400',
                        'focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300',
                        'hover:bg-white/10',
                        errors.cardNumber ? 'border-red-400' : 'border-gray-600'
                      ]"
                      placeholder="1234 5678 9012 3456"
                    />
                    <p v-if="errors.cardNumber" class="mt-1 text-sm text-red-400">
                      {{ errors.cardNumber }}
                    </p>
                  </div>

                  <!-- Nom du titulaire -->
                  <div class="space-y-2">
                    <label class="text-sm font-medium text-gray-200">
                      Nom du titulaire
                    </label>
                    <input
                      v-model="cardHolder"
                      type="text"
                      :class="[
                        'mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400',
                        'focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300',
                        'hover:bg-white/10',
                        errors.cardHolder ? 'border-red-400' : 'border-gray-600'
                      ]"
                      placeholder="JOHN DOE"
                      @input="validateCardHolder"
                    />
                    <p v-if="errors.cardHolder" class="mt-1 text-sm text-red-400">
                      {{ errors.cardHolder }}
                    </p>
                  </div>

                  <div class="grid grid-cols-2 gap-4">
                    <!-- Date d'expiration -->
                    <div class="space-y-2">
                      <label class="text-sm font-medium text-gray-200">
                        Date d'expiration
                      </label>
                      <input
                        v-model="expiryDate"
                        type="text"
                        maxlength="5"
                        @input="handleExpiryInput"
                        :class="[
                          'mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400',
                          'focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300',
                          'hover:bg-white/10',
                          errors.expiryDate ? 'border-red-400' : 'border-gray-600'
                        ]"
                        placeholder="MM/YY"
                      />
                      <p v-if="errors.expiryDate" class="mt-1 text-sm text-red-400">
                        {{ errors.expiryDate }}
                      </p>
                    </div>

                    <!-- CVV -->
                    <div class="space-y-2">
                      <label class="text-sm font-medium text-gray-200">
                        CVV
                      </label>
                      <input
                        v-model="cvv"
                        type="text"
                        maxlength="3"
                        @input="validateCVV"
                        :class="[
                          'mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400',
                          'focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300',
                          'hover:bg-white/10',
                          errors.cvv ? 'border-red-400' : 'border-gray-600'
                        ]"
                        placeholder="123"
                      />
                      <p v-if="errors.cvv" class="mt-1 text-sm text-red-400">
                        {{ errors.cvv }}
                      </p>
                    </div>
                  </div>

                  <button
                    type="submit"
                    :disabled="!isFormValid"
                    :class="[
                      'w-full flex justify-center py-3 px-4 rounded-lg font-medium text-white transition-all duration-300',
                      'bg-gradient-to-r from-purple-600 to-pink-600 hover:from-purple-700 hover:to-pink-700',
                      'focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-purple-500',
                      'transform hover:scale-[1.02]',
                      !isFormValid && 'opacity-50 cursor-not-allowed hover:scale-100'
                    ]"
                  >
                    <span class="relative">
                      Payer maintenant
                      <span class="absolute -bottom-px left-0 w-full h-px bg-gradient-to-r from-transparent via-white/50 to-transparent"></span>
                    </span>
                  </button>
                </form>
              </div>
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

// États
const cardNumber = ref('')
const cardHolder = ref('')
const expiryDate = ref('')
const cvv = ref('')
const errors = ref({})
const cardType = ref('')

// Algorithme de Luhn
const luhnCheck = (number) => {
  const digits = number.replace(/\D/g, '').split('').map(Number)
  let sum = 0
  let isEven = false

  for (let i = digits.length - 1; i >= 0; i--) {
    let digit = digits[i]

    if (isEven) {
      digit *= 2
      if (digit > 9) {
        digit -= 9
      }
    }

    sum += digit
    isEven = !isEven
  }

  return sum % 10 === 0
}

// Détection du type de carte
const detectCardType = (number) => {
  const clean = number.replace(/\D/g, '')
  if (clean.startsWith('4')) {
    return 'visa'
  } else if (/^5[1-5]/.test(clean)) {
    return 'mastercard'
  }
  return ''
}

// Formatage du numéro de carte
const displayCardNumber = computed(() => {
  if (!cardNumber.value) return ''
  return cardNumber.value.replace(/(\d{4})/g, '$1 ').trim()
})

// Validation du numéro de carte
const handleCardNumberInput = (event) => {
  let value = event.target.value.replace(/\s/g, '').replace(/\D/g, '')
  let formattedValue = ''
  
  for (let i = 0; i < value.length; i++) {
    if (i > 0 && i % 4 === 0) {
      formattedValue += ' '
    }
    formattedValue += value[i]
  }
  
  cardNumber.value = formattedValue
  cardType.value = detectCardType(value)
  
  // Validation
  if (value.length === 16) {
    if (!luhnCheck(value)) {
      errors.value.cardNumber = 'Numéro de carte invalide'
    } else {
      errors.value.cardNumber = ''
    }
  } else {
    errors.value.cardNumber = 'Le numéro doit contenir 16 chiffres'
  }
}

// Validation de la date d'expiration
const handleExpiryInput = (event) => {
  let value = event.target.value.replace(/\D/g, '')
  
  if (value.length >= 2) {
    const month = parseInt(value.slice(0, 2))
    const year = value.length > 2 ? parseInt(value.slice(2)) : 0
    
    if (month < 1 || month > 12) {
      errors.value.expiryDate = 'Mois invalide'
    } else {
      const currentDate = new Date()
      const currentYear = currentDate.getFullYear() % 100
      const currentMonth = currentDate.getMonth() + 1
      
      if (year < currentYear || (year === currentYear && month < currentMonth)) {
        errors.value.expiryDate = 'Date expirée'
      } else {
        errors.value.expiryDate = ''
      }
    }
    
    if (value.length >= 2) {
      value = value.slice(0, 2) + '/' + value.slice(2)
    }
  }
  
  expiryDate.value = value
}

// Validation du CVV
const validateCVV = () => {
  const value = cvv.value.replace(/\D/g, '')
  if (value.length !== 3) {
    errors.value.cvv = 'Le CVV doit contenir 3 chiffres'
  } else {
    errors.value.cvv = ''
  }
}

// Validation du titulaire
const validateCardHolder = () => {
  if (cardHolder.value.trim().length < 3) {
    errors.value.cardHolder = 'Le nom du titulaire doit contenir au moins 3 caractères'
  } else {
    errors.value.cardHolder = ''
  }
}

// Validation du formulaire
const isFormValid = computed(() => {
  return cardNumber.value.length === 16 && cardHolder.value.trim().length >= 3 && expiryDate.value.length === 5 && cvv.value.length === 3
})
</script> 