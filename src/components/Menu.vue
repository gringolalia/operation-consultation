<template lang="pug">
ion-menu(contentid='menu-content')
  ion-header
    ion-toolbar(color='primary')
      ion-title
        i.material-icons.ion-margin-end fastfood
        | Speisekarte

  ion-content(lines="none")
    ion-list
      ion-list-header
        | Petiscos e Rabiscos

      ion-menu-toggle(auto-hide='false', v-for='p in appPages', :key='p.title')
        ion-item(button='', @click='navigate(p.url)')
          ion-icon(slot='start', :name='p.icon')

          ion-label
            | {{p.title}}

    ion-list(v-if='loggedIn')
      ion-list-header
        | Não É da Sua Conta

      ion-menu-toggle(auto-hide='false')
        ion-item(button='', @click="navigate('/account')")
          ion-icon(slot='start', name='person')

          ion-label
            | Não É da Sua Conta

      ion-menu-toggle(auto-hide='false')
        ion-item(button='', @click="navigate('/support')")
          ion-icon(slot='start', name='help')

          ion-label
            | Apoio Técnico

      ion-menu-toggle(auto-hide='false')
        ion-item(button='', @click='logout()')
          ion-icon(slot='start', name='log-out')

          ion-label
            | Sair

    ion-list(v-if='!loggedIn')
      ion-list-header
        | Não É da Sua Conta

      ion-menu-toggle(auto-hide='false')
        ion-item(button='', @click="navigate('/login')")
          ion-icon(slot='start', name='log-in')

          ion-label
            | Entrar com Senha

      ion-menu-toggle(auto-hide='false')
        ion-item(button='', @click="navigate('/support')")
          ion-icon(slot='start', name='help')

          ion-label
            | Apoio Técnico

      ion-menu-toggle(auto-hide='false')
        ion-item(button='', @click="navigate('/signup')")
          ion-icon(slot='start', name='code-working')

          ion-label
            | Inscrever-se

    ion-list
      ion-list-header
        | Tutorial

      ion-menu-toggle(auto-hide='false')
        ion-item(button='', @click='openTutorial()')
          ion-icon(slot='start', name='hammer')

          ion-label Aprendizagem ao Gogo
</template>

<script lang="ts">
  import { Component, Vue } from 'vue-property-decorator';
  import { mapGetters } from 'vuex';

  @Component({
     computed: {
       loggedIn: function() {
         return this.$store.state.user.isAuthenticated;
       }
     }

  })
  export default class Menu extends Vue {
    appPages = [
      {
        title: 'Horário',
        url: '/tabs/schedule',
        name: 'tabs.schedule',
        icon: 'link'
      },
      {
        title: 'Palestrantes',
        url: '/tabs/speakers',
        icon: 'infinite'
      },
      {
        title: 'Mappa Mundi',
        url: '/tabs/map',
        icon: 'planet'
      },
      {
        title: 'Nobres & Pobres',
        url: '/tabs/about',
        icon: 'airplane'
      }
    ];

    navigate(url: string){
      // this.$router.directionOverride = 0;
      this.$router.push(url)
    }
    openTutorial(){
      this.$store.state.user.hasSeenTutorial = false;
      this.$router.push('tutorial')
    }
  }
</script>
