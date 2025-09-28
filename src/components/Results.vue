<template>
  <div class="risultati-box">
    <h2>{{ translations.risultati[lingua] }}</h2>

    <img
      :src="immaginePersonalita"
      :alt="translations.altImmagine[lingua]"
      class="mx-auto mb-4 object-contain small-img"
    />

    <div class="personalita" v-if="hasMessage">
      <!-- Known personality -->
      <h3>{{ translations.tuaPersonalita[lingua] }}</h3>
      <p>
        <strong>{{ punteggi.messaggio }}</strong>
      </p>
    </div>

    <div class="personalita" v-else>
      <!-- Calculate personality -->
      <h3>{{ translations.tuaPersonalita[lingua] }}</h3>
      <p>
        <strong>{{ tipoPersonalita }}</strong>
      </p>
      <p>
        <i>{{ descrizionePersonalita }}</i>
      </p>
    </div>

    <br />

    <h3>{{ translations.punteggi[lingua] }}</h3>
    <ul>
      <li>{{ translations.aperturaEsperienza[lingua] }}: {{ safePunteggi.apertura }}</li>
      <li>{{ translations.coscienziosita[lingua] }}: {{ safePunteggi.coscienziosita }}</li>
      <li>{{ translations.estroversione[lingua] }}: {{ safePunteggi.estroversione }}</li>
      <li>{{ translations.gradevolezza[lingua] }}: {{ safePunteggi.gradevolezza }}</li>
      <li>{{ translations.nevroticismo[lingua] }}: {{ safePunteggi.nevroticismo }}</li>
    </ul>

    <button @click="$emit('restart')" class="bg-blue-600 text-white px-4 py-2 rounded mt-4">
      🔄 {{ translations.nuovoTest[lingua] }}
    </button>
  </div>
</template>

<script>
export default {
  name: 'Risultati',
  props: {
    punteggi: {
      type: Object,
      default: () => ({}),
    },
    lingua: {
      type: String,
      default: 'it',
    },
  },
  data() {
    return {
      translations: {
        risultati: { it: 'Risultati', en: 'Results' },
        altImmagine: { it: 'Immagine personalità', en: 'Personality image' },
        tuaPersonalita: { it: 'La tua personalità:', en: 'Your personality:' },
        punteggi: { it: 'Punteggi:', en: 'Scores:' },
        aperturaEsperienza: { it: "Apertura all'Esperienza", en: 'Openness to Experience' },
        coscienziosita: { it: 'Coscienziosità', en: 'Conscientiousness' },
        estroversione: { it: 'Estroversione', en: 'Extraversion' },
        gradevolezza: { it: 'Gradevolezza', en: 'Agreeableness' },
        nevroticismo: { it: 'Nevroticismo', en: 'Neuroticism' },
        nuovoTest: { it: 'Nuovo Test', en: 'New Test' },
      },
      mapDescrizioni: {
        INTJ: {
          it: 'Architetto – strategico, analitico, indipendente.',
          en: 'Architect – strategic, analytical, independent.',
        },
        INTP: {
          it: 'Logico – pensatore astratto e curioso.',
          en: 'Logician – abstract thinker, curious.',
        },
        ENTJ: {
          it: 'Comandante – leader deciso e pianificatore.',
          en: 'Commander – decisive leader, planner.',
        },
        ENTP: {
          it: 'Inventore – energico, innovativo, ama sfide intellettuali.',
          en: 'Inventor – energetic, innovative, loves intellectual challenges.',
        },
        INFJ: {
          it: 'Consigliere – idealista, empatico, cerca significato.',
          en: 'Counselor – idealistic, empathetic, meaning seeker.',
        },
        INFP: {
          it: 'Medico – sensibile, creativo, guidato dai valori.',
          en: 'Healer – sensitive, creative, value-driven.',
        },
        ENFJ: {
          it: 'Protagonista – carismatico, altruista, motiva gli altri.',
          en: 'Protagonist – charismatic, altruistic, motivates others.',
        },
        ENFP: {
          it: 'Attivista – entusiasta, spontaneo, ama connessioni profonde.',
          en: 'Campaigner – enthusiastic, spontaneous, loves deep connections.',
        },
        ISTJ: {
          it: 'Ispettore – pratico, ordinato, affidabile.',
          en: 'Inspector – practical, orderly, reliable.',
        },
        ISFJ: {
          it: 'Protettore – leale, attento, orientato agli altri.',
          en: 'Protector – loyal, caring, other-oriented.',
        },
        ESTJ: {
          it: 'Direttore – pragmatico, organizzato, efficiente.',
          en: 'Executive – pragmatic, organized, efficient.',
        },
        ESFJ: {
          it: 'Fornitore – socievole, collaborativo, responsabile.',
          en: 'Provider – sociable, cooperative, responsible.',
        },
        ISTP: {
          it: 'Virtuoso – pratico, sperimentale, indipendente.',
          en: 'Virtuoso – practical, experimental, independent.',
        },
        ISFP: {
          it: 'Avventuriero – flessibile, artistico, ama la libertà.',
          en: 'Adventurer – flexible, artistic, loves freedom.',
        },
        ESTP: {
          it: 'Imprenditore – energico, realistico, orientato all’azione.',
          en: 'Entrepreneur – energetic, realistic, action-oriented.',
        },
        ESFP: {
          it: 'Intrattenitore – gioioso, spontaneo, amichevole.',
          en: 'Entertainer – joyful, spontaneous, friendly.',
        },
      },
    }
  },
  computed: {
    safePunteggi() {
      const p = this.punteggi || {}
      return {
        apertura: Number(p.apertura) || 0,
        coscienziosita: Number(p.coscienziosita) || 0,
        estroversione: Number(p.estroversione) || 0,
        gradevolezza: Number(p.gradevolezza) || 0,
        nevroticismo: Number(p.nevroticismo) || 0,
      }
    },
    hasMessage() {
      return !!(this.punteggi && (this.punteggi.messaggio || this.punteggi.message))
    },
    tipoPersonalita() {
      const { apertura, coscienziosita, estroversione, gradevolezza } = this.safePunteggi
      const EorI = estroversione >= 3 ? 'E' : 'I'
      const NorS = apertura >= 3 ? 'N' : 'S'
      const ForT = gradevolezza >= 3 ? 'F' : 'T'
      const JorP = coscienziosita >= 3 ? 'J' : 'P'
      return EorI + NorS + ForT + JorP
    },
    descrizionePersonalita() {
      const tipo = this.tipoPersonalita
      const lang = this.lingua === 'en' ? 'en' : 'it'
      let descrizione =
        (this.mapDescrizioni[tipo] && this.mapDescrizioni[tipo][lang]) ||
        (lang === 'it'
          ? 'Profilo unico e difficile da incasellare.'
          : 'Unique profile, hard to classify.')

      if (this.safePunteggi.nevroticismo >= 3) {
        descrizione +=
          lang === 'it'
            ? ' Tende a essere più sensibile emotivamente e soggetto a stress.'
            : ' Tends to be emotionally sensitive and prone to stress.'
      } else {
        descrizione +=
          lang === 'it'
            ? ' Mostra stabilità emotiva e resilienza.'
            : ' Shows emotional stability and resilience.'
      }
      return descrizione
    },
    immaginePersonalita() {
      const immagini = {
        INTJ: 'images/intj.png',
        INTP: 'images/intp.png',
        ENTJ: 'images/entj.png',
        ENTP: 'images/entp.png',
        INFJ: 'images/infj.png',
        INFP: 'images/infp.png',
        ENFJ: 'images/enfj.png',
        ENFP: 'images/enfp.png',
        ISTJ: 'images/istj.png',
        ISFJ: 'images/isfj.png',
        ESTJ: 'images/estj.png',
        ESFJ: 'images/esfj.png',
        ISTP: 'images/istp.png',
        ISFP: 'images/isfp.png',
        ESTP: 'images/estp.png',
        ESFP: 'images/esfp.png',
      }
      if (this.punteggi && this.punteggi.tipo && immagini[this.punteggi.tipo]) {
        return immagini[this.punteggi.tipo]
      }
      return immagini[this.tipoPersonalita] || '/images/default.png'
    },
  },
}
</script>
