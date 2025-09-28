<template>
  <div class="p-6 max-w-2xl mx-auto">
    <!-- Barra toggle -->
    <div class="flex justify-end mb-4">
      <button @click="toggleDarkMode" class="bg-blue-600 text-white px-3 py-1 rounded">
        {{ isDarkMode ? '🌙 Dark' : '☀️ Light' }}
      </button>
    </div>

    <!-- Titolo -->
    <h1 class="text-2xl font-bold mb-4">Test della Personalità</h1>

    <!-- Input personalità già nota -->
    <div v-if="!submitted && !showResults">
      <div class="flex gap-2 mb-6">
        <p>Sai già la tua personalità? Scrivila e vai ai risultati!</p>
        <input
          v-model="knownPersonality"
          type="text"
          placeholder="Scrivila la tua personalità"
          class="border rounded px-3 py-2 flex-1"
        />
        <button @click="useKnownPersonality" class="bg-blue-600 text-white px-4 py-2 rounded">
          Invia
        </button>
      </div>

      <!-- Form domande -->
      <form @submit.prevent="submitForm" v-if="!skipQuestions">
        <div v-for="(categoria, catIndex) in categorie" :key="catIndex" class="mb-6">
          <br></br><br></br>
          <h2 class="font-semibold mb-2">{{ categoria.nome }}</h2>
          <div v-for="(domanda, qIndex) in categoria.domande" :key="qIndex" class="mb-2">
            <p>{{ domanda }}</p>
            <div class="flex gap-4">
              <label>
                <input
                  type="radio"
                  :name="`cat-${catIndex}-q-${qIndex}`"
                  value="si"
                  v-model="risposte[categoria.nome][qIndex]"
                />
                Sì
              </label>
              <label>
                <input
                  type="radio"
                  :name="`cat-${catIndex}-q-${qIndex}`"
                  value="dipende"
                  v-model="risposte[categoria.nome][qIndex]"
                />
                Dipende
              </label>
              <label>
                <input
                  type="radio"
                  :name="`cat-${catIndex}-q-${qIndex}`"
                  value="no"
                  v-model="risposte[categoria.nome][qIndex]"
                />
                No
              </label>
            </div>
          </div>
        </div>

        <br></br><br></br>
        <!-- Pulsante invio -->
        <button type="submit" class="bg-green-600 text-white px-4 py-2 rounded">
          Invia Risposte
        </button>
      </form>
    </div>

    <!-- Mostra i risultati -->
    <Risultati v-if="showResults" :punteggi="punteggi" @restart="restartTest" />
  </div>
</template>

<script>
import Risultati from './Results.vue'

export default {
  name: 'PersonalitaForm',
  components: { Risultati },
  data() {
    return {
      knownPersonality: '',
      skipQuestions: false,
      submitted: false,
      showResults: false,
      isDarkMode: false,
      categorie: [
        {
          nome: "Apertura all'Esperienza",
          domande: [
            'Mi piace provare cose nuove anche se sono un po’ fuori dalla mia zona di comfort.',
            'Trovo interessante pensare a idee astratte e teorie complesse.',
            'Apprezzo l’arte, la musica, la letteratura che sono insolite o sperimentali.',
            'Mi annoio facilmente se non c’è varietà nella mia vita quotidiana.',
            'Sono curioso/a del mondo, mi piace esplorare culture diverse e punti di vista diversi.',
          ],
        },
        {
          nome: 'Coscienziosità',
          domande: [
            'Quando decido qualcosa, tendo a portarla a termine con impegno.',
            'Mi organizzo bene in anticipo per evitare problemi (pianificazione).',
            'Sono una persona affidabile: gli altri contano sul fatto che mantenga le mie promesse.',
            'Tengo ordine e precisione anche nel lavoro o negli impegni più routinari.',
            'Se ho un compito, mi faccio carico del fatto che sia fatto bene piuttosto che velocemente.',
          ],
        },
        {
          nome: 'Estroversione',
          domande: [
            'Mi sento energico/a quando sono in mezzo a tante persone.',
            'Preferirei stare in compagnia che da solo/a.',
            'Mi piace parlare con nuovi conoscenti e fare amicizia facilmente.',
            'Mi coinvolgo volentieri in attività sociali, feste, eventi.',
            'Trovo piacere nel fare da leader o nel guidare gruppi.',
          ],
        },
        {
          nome: 'Gradevolezza',
          domande: [
            'Mi preoccupo dei sentimenti altrui e cerco di non ferire gli altri.',
            'Sono disposto/a a sacrificare qualcosa per aiutare qualcuno.',
            'Evito conflitti quando possibile e cerco soluzioni pacifiche.',
            'Mi fido che la maggior parte delle persone siano sincere e bene intenzionate.',
            'Sono gentile e comprensivo/a con chi ha difficoltà.',
          ],
        },
        {
          nome: 'Nevroticismo',
          domande: [
            'Mi capita di preoccuparmi molto anche per cose piccole.',
            'Provo nervosismo o tensione quando le cose vanno male.',
            'È facile che mi senta ansioso/a in situazioni incerte.',
            'Ho cambiamenti di umore frequenti.',
            'Mi capita di reagire emotivamente in modo esagerato alle critiche.',
          ],
        },
      ],
      risposte: {},
      punteggi: {},
    }
  },
  created() {
    this.categorie.forEach((cat) => {
      this.risposte[cat.nome] = Array(cat.domande.length).fill(null)
    })
  },
  methods: {
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode
      document.body.classList.toggle('dark-mode', this.isDarkMode)
    },
    restartTest() {
        this.showResults = false
        this.submitted = false
        this.skipQuestions = false
        this.knownPersonality = ''
        this.punteggi = {}
        this.categorie.forEach(cat => {
            this.risposte[cat.nome] = Array(cat.domande.length).fill(null)
        })
    },
    useKnownPersonality() {
      if (this.knownPersonality.trim() !== '') {
        this.skipQuestions = true
        this.showResults = true
        this.punteggi = { messaggio: `Hai indicato: ${this.knownPersonality}` }
      }
    },
    submitForm() {
        for (let cat of this.categorie) {
            if (this.risposte[cat.nome].some((r) => r === null)) {
                alert('Rispondi a tutte le domande prima di inviare!')
                return
            }
        }
        let risultati = {
            apertura: 0,
            coscienziosita: 0,
            estroversione: 0,
            gradevolezza: 0,
            nevroticismo: 0,
        }

        for (let cat of this.categorie) {
            let score = 0
            this.risposte[cat.nome].forEach((r) => {
                if (r === 'si') score += 1
                if (r === 'dipende') score += 0.5
            })

            if (cat.nome.includes("Apertura")) risultati.apertura = score
            if (cat.nome.includes("Coscienziosità")) risultati.coscienziosita = score
            if (cat.nome.includes("Estroversione")) risultati.estroversione = score
            if (cat.nome.includes("Gradevolezza")) risultati.gradevolezza = score
            if (cat.nome.includes("Nevroticismo")) risultati.nevroticismo = score
        }
        this.punteggi = risultati
        this.submitted = true
        this.showResults = true
    },
  },
}
</script>
