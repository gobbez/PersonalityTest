<template>
  <div class="p-6 max-w-2xl mx-auto">
    <!-- Toggle bar -->
    <div class="flex justify-end mb-4">
      <button @click="toggleDarkMode" class="bg-blue-600 text-white px-3 py-1 rounded">
        {{ isDarkMode ? '🌙 Dark' : '☀️ Light' }}
      </button>
    </div>

    <!-- Title -->
    <h1 class="text-2xl font-bold mb-4">{{ traduzioni.titolo }}</h1>

    <div v-if="!submitted && !showResults">
      <!-- Input known personality @todo 
      <div class="flex gap-2 mb-6">
        <p>{{ traduzioni.testoInput }}</p>
        <input
          v-model="knownPersonality"
          type="text"
          :placeholder="traduzioni.placeholderInput"
          class="border rounded px-3 py-2 flex-1"
        />
        <button @click="useKnownPersonality" class="bg-blue-600 text-white px-4 py-2 rounded">
          {{ traduzioni.pulsanteInvia }}
        </button>
      </div>
      -->

      <!-- Question Form -->
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
                {{ traduzioni.yes }}
              </label>
              <label>
                <input
                  type="radio"
                  :name="`cat-${catIndex}-q-${qIndex}`"
                  value="dipende"
                  v-model="risposte[categoria.nome][qIndex]"
                />
                {{ traduzioni.maybe }}
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
        <!-- Send Button -->
        <button type="submit" class="bg-green-600 text-white px-4 py-2 rounded">
          {{ traduzioni.pulsanteInvia }}
        </button>
      </form>
    </div>

    <!-- Show results -->
    <Risultati v-if="showResults" :punteggi="punteggi" :lingua="lingua" @restart="restartTest" />
  </div>
</template>

<script>
import Risultati from "./Results.vue";

export default {
  name: "PersonalitaForm",
  components: { Risultati },
  props: {
    lingua: {
      type: String,
      default: "it",
    },
  },
  data() {
    return {
      knownPersonality: "",
      skipQuestions: false,
      submitted: false,
      showResults: false,
      isDarkMode: false,
      categorie: [],
      risposte: {},
      punteggi: {},
    };
  },
  computed: {
    traduzioni() {
      const testi = {
        it: {
          titolo: "Test della Personalità",
          testoInput: "Sai già la tua personalità? Scrivila e vai ai risultati!",
          placeholderInput: "Scrivila la tua personalità",
          pulsanteInvia: "Invia",
          yes: "Sì",
          maybe: "Forse",
        },
        en: {
          titolo: "Personality Test",
          testoInput: "Already know your personality? Write it and go to results!",
          placeholderInput: "Write your personality",
          pulsanteInvia: "Submit",
          yes: "Yes",
          maybe: "Maybe",
        },
      };
      return testi[this.lingua] || testi.it;
    },
  },
  created() {
    this.caricaDomande();
  },
  watch: {
    lingua() {
      this.caricaDomande();
    },
  },
  methods: {
    caricaDomande() {
      if (this.lingua === "it") {
        this.categorie = [
          {
            nome: "Apertura all'Esperienza",
            domande: [
              "Mi piace provare cose nuove anche se sono un po’ fuori dalla mia zona di comfort.",
              "Trovo interessante pensare a idee astratte e teorie complesse.",
              "Apprezzo l’arte, la musica, la letteratura che sono insolite o sperimentali.",
              "Mi annoio facilmente se non c’è varietà nella mia vita quotidiana.",
              "Sono curioso/a del mondo, mi piace esplorare culture diverse e punti di vista diversi.",
            ],
          },
          {
            nome: "Coscienziosità",
            domande: [
              "Quando decido qualcosa, tendo a portarla a termine con impegno.",
              "Mi organizzo bene in anticipo per evitare problemi (pianificazione).",
              "Sono una persona affidabile: gli altri contano sul fatto che mantenga le mie promesse.",
              "Tengo ordine e precisione anche nel lavoro o negli impegni più routinari.",
              "Se ho un compito, mi faccio carico del fatto che sia fatto bene piuttosto che velocemente.",
            ],
          },
          {
            nome: "Estroversione",
            domande: [
              "Mi sento energico/a quando sono in mezzo a tante persone.",
              "Preferirei stare in compagnia che da solo/a.",
              "Mi piace parlare con nuovi conoscenti e fare amicizia facilmente.",
              "Mi coinvolgo volentieri in attività sociali, feste, eventi.",
              "Trovo piacere nel fare da leader o nel guidare gruppi.",
            ],
          },
          {
            nome: "Gradevolezza",
            domande: [
              "Mi preoccupo dei sentimenti altrui e cerco di non ferire gli altri.",
              "Sono disposto/a a sacrificare qualcosa per aiutare qualcuno.",
              "Evito conflitti quando possibile e cerco soluzioni pacifiche.",
              "Mi fido che la maggior parte delle persone siano sincere e bene intenzionate.",
              "Sono gentile e comprensivo/a con chi ha difficoltà.",
            ],
          },
          {
            nome: "Nevroticismo",
            domande: [
              "Mi capita di preoccuparmi molto anche per cose piccole.",
              "Provo nervosismo o tensione quando le cose vanno male.",
              "È facile che mi senta ansioso/a in situazioni incerte.",
              "Ho cambiamenti di umore frequenti.",
              "Mi capita di reagire emotivamente in modo esagerato alle critiche.",
            ],
          },
        ];
      } else if (this.lingua === "en") {
        this.categorie = [
          {
            nome: "Openness to Experience",
            domande: [
              "I enjoy trying new things even if they are outside my comfort zone.",
              "I find it interesting to think about abstract ideas and complex theories.",
              "I appreciate art, music, and literature that are unusual or experimental.",
              "I get bored easily if there is no variety in my daily life.",
              "I am curious about the world, I like exploring different cultures and viewpoints.",
            ],
          },
          {
            nome: "Conscientiousness",
            domande: [
              "When I decide something, I tend to carry it out with commitment.",
              "I organize myself well in advance to avoid problems (planning).",
              "I am a reliable person: others count on me to keep my promises.",
              "I keep order and precision even in routine tasks.",
              "If I have a task, I take responsibility to do it well rather than quickly.",
            ],
          },
          {
            nome: "Extraversion",
            domande: [
              "I feel energetic when I am among many people.",
              "I would rather be in company than alone.",
              "I enjoy talking to new acquaintances and making friends easily.",
              "I willingly engage in social activities, parties, events.",
              "I enjoy leading or guiding groups.",
            ],
          },
          {
            nome: "Agreeableness",
            domande: [
              "I care about others' feelings and try not to hurt them.",
              "I am willing to sacrifice something to help someone.",
              "I avoid conflicts when possible and try peaceful solutions.",
              "I trust that most people are sincere and well-intentioned.",
              "I am kind and understanding towards those in difficulty.",
            ],
          },
          {
            nome: "Neuroticism",
            domande: [
              "I often worry even about small things.",
              "I feel nervous or tense when things go wrong.",
              "I find it easy to feel anxious in uncertain situations.",
              "I have frequent mood changes.",
              "I sometimes react emotionally in an exaggerated way to criticism.",
            ],
          },
        ];
      }

      // initialize answers
      this.risposte = {};
      this.categorie.forEach((cat) => {
        this.risposte[cat.nome] = Array(cat.domande.length).fill(null);
      });
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode;
      document.body.classList.toggle("dark-mode", this.isDarkMode);
    },
    restartTest() {
      this.showResults = false;
      this.submitted = false;
      this.skipQuestions = false;
      this.knownPersonality = "";
      this.punteggi = {};
      this.categorie.forEach((cat) => {
        this.risposte[cat.nome] = Array(cat.domande.length).fill(null);
      });
    },
    useKnownPersonality() {
      if (this.knownPersonality.trim() !== "") {
        this.skipQuestions = true;
        this.showResults = true;
        const text = this.lingua === 'en'
          ? `You indicated: ${this.knownPersonality}`
          : `Hai indicato: ${this.knownPersonality}`;
        this.punteggi = { messaggio: text };
      }
    },
    submitForm() {
      for (let cat of this.categorie) {
        if (this.risposte[cat.nome].some((r) => r === null)) {
          alert(
            this.lingua === "it"
              ? "Rispondi a tutte le domande prima di inviare!"
              : "Please answer all questions before submitting!"
          );
          return;
        }
      }
      let risultati = {
        apertura: 0,
        coscienziosita: 0,
        estroversione: 0,
        gradevolezza: 0,
        nevroticismo: 0,
      };
      for (let cat of this.categorie) {
        let score = 0;
        this.risposte[cat.nome].forEach((r) => {
          if (r === "si") score += 1;
          if (r === "dipende") score += 0.5;
        });
        if (cat.nome.includes("Apertura") || cat.nome.includes("Openness"))
          risultati.apertura = score;
        if (
          cat.nome.includes("Coscienziosità") ||
          cat.nome.includes("Conscientiousness")
        )
          risultati.coscienziosita = score;
        if (cat.nome.includes("Estroversione") || cat.nome.includes("Extraversion"))
          risultati.estroversione = score;
        if (
          cat.nome.includes("Gradevolezza") ||
          cat.nome.includes("Agreeableness")
        )
          risultati.gradevolezza = score;
        if (
          cat.nome.includes("Nevroticismo") ||
          cat.nome.includes("Neuroticism")
        )
          risultati.nevroticismo = score;
      }
      this.punteggi = risultati;
      this.submitted = true;
      this.showResults = true;
    },
  },
};
</script>

