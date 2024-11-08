<template>
  <div class="min-h-screen bg-gradient-to-br from-gray-900 via-purple-900 to-violet-800 py-6 flex flex-col justify-center sm:py-12">
    <div class="relative px-4 sm:px-6 lg:px-8 max-w-7xl mx-auto w-full">
      <!-- Container principal avec flex sur desktop -->
      <div class="flex flex-col lg:flex-row lg:items-center lg:space-x-8 space-y-8 lg:space-y-0">
        
        <!-- Carte bancaire -->
        <div class="lg:w-2/5">
          <div class="relative group max-w-md mx-auto lg:ml-0">
            <div class="absolute -inset-1 bg-gradient-to-r from-pink-600 to-purple-600 rounded-lg blur opacity-25 group-hover:opacity-100 transition duration-1000 group-hover:duration-200"></div>
            <!-- Conteneur de la carte avec effet 3D -->
            <div class="relative preserve-3d" :class="{ 'rotate-y-180': isCardFlipped }">
              <!-- Face avant -->
              <div class="relative w-full h-56 rounded-xl shadow-2xl overflow-hidden backdrop-blur-sm transition-all duration-500 backface-hidden"
                   :class="[cardType === 'visa' ? 'bg-gradient-to-br from-blue-600 to-blue-800' : cardType === 'mastercard' ? 'bg-gradient-to-br from-red-600 to-orange-600' : 'bg-gradient-to-br from-gray-700 to-gray-900']">
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

              <!-- Face arrière -->
              <div class="absolute inset-0 w-full h-56 rounded-xl shadow-2xl overflow-hidden backdrop-blur-sm transition-all duration-500 backface-hidden rotate-y-180"
                   :class="[cardType === 'visa' ? 'bg-gradient-to-br from-blue-800 to-blue-900' : cardType === 'mastercard' ? 'bg-gradient-to-br from-red-800 to-orange-900' : 'bg-gradient-to-br from-gray-800 to-gray-900']">
                <!-- Bande magnétique -->
                <div class="absolute top-8 w-full h-12 bg-gray-900"></div>
                
                <!-- Zone de signature avec CVV -->
                <div class="absolute top-32 right-8 left-8">
                  <div class="relative h-10 bg-white/10 rounded">
                    <div class="absolute inset-0 bg-white/5"></div>
                    <!-- CVV -->
                    <div class="absolute right-2 top-1/2 -translate-y-1/2 backdrop-blur-sm bg-white/20 rounded px-2 py-1">
                      <div class="font-mono text-white">{{ cvv || '•••' }}</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Formulaire -->
        <div class="lg:w-3/5">
          <div class="relative">
            <div class="absolute -inset-1 bg-gradient-to-r from-pink-600 to-purple-600 rounded-2xl blur opacity-25"></div>
            <div class="relative px-6 py-8 bg-white/10 backdrop-blur-xl rounded-xl shadow-2xl">
              <div class="max-w-lg mx-auto">
                <form @submit.prevent="handleSubmit" class="space-y-6">
                  <!-- Numéro de carte -->
                  <div class="space-y-2">
                    <label class="text-sm font-medium text-gray-200">Numéro de carte</label>
                    <input
                      v-model="cardNumber"
                      type="text"
                      maxlength="19"
                      @input="handleCardNumberInput"
                      class="mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400 focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300 hover:bg-white/10"
                      placeholder="1234 5678 9012 3456"
                    />
                    <p v-if="errors.cardNumber" class="mt-1 text-sm text-red-400">{{ errors.cardNumber }}</p>
                  </div>

                  <!-- Nom du titulaire -->
                  <div class="space-y-2">
                    <label class="text-sm font-medium text-gray-200">Nom du titulaire</label>
                    <input
                      v-model="cardHolder"
                      type="text"
                      class="mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400 focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300 hover:bg-white/10"
                      placeholder="JOHN DOE"
                      @input="validateCardHolder"
                    />
                    <p v-if="errors.cardHolder" class="mt-1 text-sm text-red-400">{{ errors.cardHolder }}</p>
                  </div>

                  <div class="grid grid-cols-2 gap-4">
                    <!-- Date d'expiration -->
                    <div class="space-y-2">
                      <label class="text-sm font-medium text-gray-200">Date d'expiration</label>
                      <input
                        v-model="expiryDate"
                        type="text"
                        maxlength="5"
                        @input="handleExpiryInput"
                        class="mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400 focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300 hover:bg-white/10"
                        placeholder="MM/YY"
                      />
                      <p v-if="errors.expiryDate" class="mt-1 text-sm text-red-400">{{ errors.expiryDate }}</p>
                    </div>

                    <!-- CVV -->
                    <div class="space-y-2">
                      <label class="text-sm font-medium text-gray-200">CVV</label>
                      <input
                        v-model="cvv"
                        type="text"
                        maxlength="3"
                        @focus="handleCVVFocus"
                        @blur="handleCVVBlur"
                        @input="validateCVV"
                        class="mt-1 block w-full rounded-lg bg-white/5 border-transparent backdrop-blur-sm text-white placeholder-gray-400 focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all duration-300 hover:bg-white/10"
                        placeholder="123"
                      />
                      <p v-if="errors.cvv" class="mt-1 text-sm text-red-400">{{ errors.cvv }}</p>
                    </div>
                  </div>

                  <button
                    type="submit"
                    class="w-full py-3 mt-4 bg-gradient-to-r from-purple-600 to-pink-600 rounded-lg text-white font-medium text-sm uppercase transition duration-300 hover:scale-105">
                    Confirmer
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

<script>
export default {
  data() {
    return {
      cardNumber: '',
      cardHolder: '',
      expiryDate: '',
      cvv: '',
      isCardFlipped: false,
      cardType: '',
      errors: {}
    };
  },
  computed: {
    displayCardNumber() {
      return this.cardNumber.replace(/\d{4}(?=.)/g, '$& ');
    }
  },
  methods: {
    validateCardNumber() {
      const cleaned = this.cardNumber.replace(/\s+/g, '');
      const regex = /^[0-9]{16}$/;
      this.errors.cardNumber = !regex.test(cleaned) ? 'Numéro de carte invalide' : '';
      this.cardType = cleaned.startsWith('4') ? 'visa' : cleaned.startsWith('5') ? 'mastercard' : '';
    },
    handleCardNumberInput(event) {
      this.cardNumber = event.target.value.replace(/\s+/g, '').replace(/(\d{4})/g, '$1 ').trim();
      this.validateCardNumber();
    },
    validateCardHolder() {
      this.errors.cardHolder = this.cardHolder.length < 3 ? 'Nom trop court' : '';
    },
    handleExpiryInput(event) {
      const cleaned = event.target.value.replace(/[^0-9]/g, '').slice(0, 4);
      this.expiryDate = cleaned.replace(/(\d{2})(\d{2})/, '$1/$2');
      const [month, year] = this.expiryDate.split('/');
      const currentYear = new Date().getFullYear() % 100;
      if (parseInt(month) < 1 || parseInt(month) > 12 || parseInt(year) < currentYear) {
        this.errors.expiryDate = 'Date invalide';
      } else {
        this.errors.expiryDate = '';
      }
    },
    validateCVV() {
      const regex = /^[0-9]{3}$/;
      this.errors.cvv = !regex.test(this.cvv) ? 'CVV invalide' : '';
    },
    handleCVVFocus() {
      this.isCardFlipped = true;
    },
    handleCVVBlur() {
      this.isCardFlipped = false;
    },
    handleSubmit() {
      this.validateCardNumber();
      this.validateCardHolder();
      this.handleExpiryInput({ target: { value: this.expiryDate } });
      this.validateCVV();
      if (!Object.values(this.errors).some(Boolean)) {
        alert('Carte confirmée avec succès !');
      }
    }
  }
};
</script>

<style scoped>
.preserve-3d {
  transform-style: preserve-3d;
  transition: transform 0.6s;
}
.backface-hidden {
  backface-visibility: hidden;
}
.rotate-y-180 {
  transform: rotateY(180deg);
}
</style>
