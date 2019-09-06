<template lang="pug">
.ion-page
  ion-header
    ion-toolbar(color='primary')
      ion-buttons(slot='start')
        ion-menu-button

      ion-title
        i.fas.fa-sun.fa-2x.ion-margin-end
        | Hyvästi Julma Maailma

      ion-buttons(slot='end')
        ion-button(@click="openModal")
          //- ion-button(@click='presentPopover($event)')
          ion-icon(slot='icon-only', name='more')


  ion-content
    .about-header
      img(src='/assets/img/white-alchemy-sulphuric.svg', alt='ionic logo')

    .about-info(padding='')
      h2 An Anionic Groupthink Circus

      ion-list(lines='none')
        ion-item
          //- ion-icon(name='infinite', slot='start')
          i.fas.fa-chess-queen.fa-lg.ion-margin-end
          ion-label Essa Data Querida

          ion-datetime(displayformat='MMM DD, YYYY', max='2056', :value='conferenceStart')

        ion-item
          i.fas.fa-chess-knight.fa-lg.ion-margin-end
          ion-label
            | Onde Acontece

          ion-select
            ion-select-option(value='madison' selected) San Pavlov

            ion-select-option(value='austin') Manaus Quarenta Graus

            ion-select-option(value='chicago') Rio de Dinheiro

            ion-select-option(value='seattle') Brasília Sicília

      p
        i.fas.fa-chess.fa-3x.fa-pull-left
        | Nemore nominati eu eum, praesent quaerendum qui ut. Eam quot ipsum omittantur ex, no expetenda dissentiunt quo. An dicunt sapientem ocurreret mea, dicta platonem nam ad. Duo fuisset repudiandae at, prima philosophia an sea.
      p
        | Nemore nominati eu eum, praesent quaerendum qui ut. Eam quot ipsum omittantur ex, no expetenda dissentiunt quo. An dicunt sapientem ocurreret mea, dicta platonem nam ad. Duo fuisset repudiandae at, prima philosophia an sea.
</template>

<script>
export default {
  name: 'Modal',
  props: {
    title: { type: String, default: 'Holy Modal Rounders' },
  },
  data() {
    return {
      content: 'Eripuit interesset at qui, quis discere ei sea, ea nec graece postulant. Eligendi apeirian ei sea. Ei movet bonorum est, pri no putent voluptatum, justo complectitur at vis. Sumo fugit ei vim, qui meliore partiendo liberavisse ad, eam tempor postulant complectitur id. Mea ad utinam nullam virtute, ne assum adversarium mei.',
    }
  },
}
</script>


<script lang="ts">
  import { Component, Prop, Vue } from 'vue-property-decorator';
  import { mapGetters } from 'vuex';

  @Component({
    computed: mapGetters(['conferenceStart'])
  })
  export default class About extends Vue {

    presentPopover(event){
      console.log('click', event)
    }
  }
</script>

<style scoped>
.about-header {
  background-color: #434954;
  padding: 16px;
  width: 100%;
  min-height: 150px;
  text-align: center;
}

.about-header img {
  max-width: 200px;
  min-height: 115px;
  margin-left: -15px;
  padding: 25px 0 20px 0;
}

.about-info p {
  color: #697072;
  text-align: left;
}

.about-info ion-icon {
  width: 20px;
}

.md .about-info [text-right],
.wp .about-info [text-right] {
  margin-right: 0;
}

.ios .about-info {
  text-align: center;
}

.ios .about-info ion-icon {
  width: auto;
  margin-right: 10px;
}
</style>
