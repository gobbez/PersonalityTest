<template>
  <div class="risultati-box">
    <h2>Risultati</h2>

    <div class="personalita">
      <h3>La tua personalità:</h3>
      <p>
        <strong>{{ tipoPersonalita }}</strong>
      </p>
      <p><i>{{ descrizionePersonalita }}</i></p>
    </div>

    <br></br>

    <h3>Punteggi:</h3>
    <ul>
      <li>Apertura all'Esperienza: {{ punteggi.apertura }}</li>
      <li>Coscienziosità: {{ punteggi.coscienziosita }}</li>
      <li>Estroversione: {{ punteggi.estroversione }}</li>
      <li>Gradevolezza: {{ punteggi.gradevolezza }}</li>
      <li>Nevroticismo: {{ punteggi.nevroticismo }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'Risultati',
  props: ['punteggi'],
  computed: {
    tipoPersonalita() {
      const { apertura, coscienziosita, estroversione, gradevolezza, nevroticismo } = this.punteggi

      // Costruzione lettere MBTI-like
      const EorI = estroversione >= 3 ? 'E' : 'I'
      const NorS = apertura >= 3 ? 'N' : 'S'
      const ForT = gradevolezza >= 3 ? 'F' : 'T'
      const JorP = coscienziosita >= 3 ? 'J' : 'P'

      return EorI + NorS + ForT + JorP
    },
    descrizionePersonalita() {
      const mapDescrizioni = {
        INTJ: 'Architetto – strategico, analitico, indipendente.',
        INTP: 'Logico – pensatore astratto e curioso.',
        ENTJ: 'Comandante – leader deciso e pianificatore.',
        ENTP: 'Inventore – energico, innovativo, ama sfide intellettuali.',
        INFJ: 'Consigliere – idealista, empatico, cerca significato.',
        INFP: 'Medico – sensibile, creativo, guidato dai valori.',
        ENFJ: 'Protagonista – carismatico, altruista, motiva gli altri.',
        ENFP: 'Attivista – entusiasta, spontaneo, ama connessioni profonde.',
        ISTJ: 'Ispettore – pratico, ordinato, affidabile.',
        ISFJ: 'Protettore – leale, attento, orientato agli altri.',
        ESTJ: 'Direttore – pragmatico, organizzato, efficiente.',
        ESFJ: 'Fornitore – socievole, collaborativo, responsabile.',
        ISTP: 'Virtuoso – pratico, sperimentale, indipendente.',
        ISFP: 'Avventuriero – flessibile, artistico, ama la libertà.',
        ESTP: 'Imprenditore – energico, realistico, orientato all’azione.',
        ESFP: 'Intrattenitore – gioioso, spontaneo, amichevole.',
      }

      const tipo = this.tipoPersonalita
      let descrizione = mapDescrizioni[tipo] || 'Profilo unico e difficile da incasellare.'

      // Influenza del nevroticismo
      if (this.punteggi.nevroticismo >= 3) {
        descrizione += ' Tende a essere più sensibile emotivamente e soggetto a stress.'
      } else {
        descrizione += ' Mostra stabilità emotiva e resilienza.'
      }

      return descrizione
    },
  },
}
</script>
