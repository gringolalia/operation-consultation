<template lang="pug">
.ion-page
  ion-header
    ion-toolbar(color='primary')
      ion-buttons(slot='start')
        ion-menu-button

      ion-segment(@ionchange='updateSegment')
        ion-segment-button(value='all', checked="segment === 'all'") All

        ion-segment-button(value='favorites', checked="segment === 'favorites'") Favorites

      ion-buttons(slot='end')
        ion-button(@click='presentFilter')
          ion-icon(slot='icon-only', name='options')

    ion-toolbar(color='primary')
      ion-searchbar(v-model='queryText', @ionchange='updateSearchTerm', placeholder='Search')

  ion-content
    ion-list(v-show='allGrouped.length > 0')
      ion-item-group(v-for='group in allGrouped', :key='group.id')
        ion-item-divider(sticky='')
          ion-label {{group.startTime | dateFormat(&quot;h:mm a&quot;)}}

        ion-item-sliding(v-for='session in group.sessions', :key='session.id', :track='session.tracks[0] | lowercase')
          ion-item(button='', @click='goToSessionDetail(session)')
            ion-label
              h3 {{session.name}}

              p
                | {{session.dateTimeStart | dateFormat(&quot;h:mm a&quot;)}} &mdash; {{session.dateTimeEnd | dateFormat(&quot;h:mm a&quot;)}}: {{session.location}}

          ion-item-options
            ion-item-option(color='favorite', @click='addFavorite($event, session)', v-if="segment === 'all'") Favorite

            ion-item-option(color='danger', @click="removeFavorite($event, session, 'Remove Favorite')", v-if="segment === 'favorites'") Remove

    ion-list-header(v-show='allGrouped.length === 0') No Sessions Found

    ion-fab(slot='fixed', vertical='bottom', horizontal='end', ref='fab')
      ion-fab-button(ref='fabButton')
        ion-icon(name='share')

      ion-fab-list(ref='fabList', side='top')
        ion-fab-button(color='vimeo', @click="openSocial('Vimeo')")
          ion-icon(name='logo-vimeo')

        ion-fab-button(color='google', @click="openSocial('Google+')")
          ion-icon(name='logo-googleplus')

        ion-fab-button(color='twitter', @click="openSocial('Twitter')")
          ion-icon(name='logo-twitter')

        ion-fab-button(color='facebook', @click="openSocial('Facebook')")
          ion-icon(name='logo-facebook')
</template>

<style scoped>
ion-item-sliding[track="ionic"] ion-label {
  border-left: 2px solid var(--ion-color-primary);
  padding-left: 10px;
}
ion-item-sliding[track="angular"] ion-label {
  border-left: 2px solid var(--ion-color-angular);
  padding-left: 10px;
}
ion-item-sliding[track="communication"] ion-label {
  border-left: 2px solid var(--ion-color-communication);
  padding-left: 10px;
}
ion-item-sliding[track="tooling"] ion-label {
  border-left: 2px solid var(--ion-color-tooling);
  padding-left: 10px;
}
ion-item-sliding[track="services"] ion-label {
  border-left: 2px solid var(--ion-color-services);
  padding-left: 10px;
}
ion-item-sliding[track="design"] ion-label {
  border-left: 2px solid var(--ion-color-design);
  padding-left: 10px;
}
ion-item-sliding[track="workshop"] ion-label {
  border-left: 2px solid var(--ion-color-workshop);
  padding-left: 10px;
}
ion-item-sliding[track="food"] ion-label {
  border-left: 2px solid var(--ion-color-food);
  padding-left: 10px;
}
ion-item-sliding[track="documentation"] ion-label {
  border-left: 2px solid var(--ion-color-documentation);
  padding-left: 10px;
}
ion-item-sliding[track="navigation"] ion-label {
  border-left: 2px solid var(--ion-color-navigation);
  padding-left: 10px;
}
</style>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import { Session, SessionGroup } from "../store/modules/sessions";
import { parse as parseDate } from "date-fns";
import SessionListFilter from "./SessionListFilter.vue";

@Component
export default class SessionList extends Vue {
  $refs!: {
    fab: HTMLIonFabElement;
  };
  segment = "all";
  groupedByStartTime(sessions: Session[]) {
    return sessions
      .sort(
        (a, b) =>
          parseDate(a.dateTimeStart).valueOf() -
          parseDate(b.dateTimeStart).valueOf()
      )
      .reduce(
        (groups, session) => {
          let starterHour = parseDate(session.dateTimeStart);
          starterHour.setMinutes(0);
          starterHour.setSeconds(0);
          const starterHourStr = starterHour.toJSON();

          const foundGroup = groups.find(
            group => group.startTime === starterHourStr
          );
          if (foundGroup) {
            foundGroup.sessions.push(session);
          } else {
            groups.push({
              startTime: starterHourStr,
              sessions: [session]
            });
          }
          return groups;
        },
        [] as SessionGroup[]
      );
  }

  get allGrouped() {
    if (this.segment === "all") {
      return this.groupedByStartTime(this.$store.getters.allFiltered);
    } else {
      return this.groupedByStartTime(this.$store.getters.favoritesFiltered);
    }
  }

  get queryText() {
    return this.$store.state.sessions.searchText;
  }

  async addFavorite(event: MouseEvent, session: Session) {
    if (
      this.$store.state.sessions.favoriteSessions.indexOf(session.id) !== -1
    ) {
      // woops, they already favorited it! What shall we do!?
      // prompt them to remove it
      this.removeFavorite(event, session, "Favorite already added");
    } else {
      // remember this session as a user favorite
      this.$store.dispatch("addFavorite", session.id);

      // create an alert instance
      const alert = await this.$ionic.alertController.create({
        header: "Favorite Added",
        buttons: [
          {
            text: "OK",
            handler: () => {
              // close the sliding item
              const slidingItem = (event.target as HTMLElement).closest(
                "ion-item-sliding"
              );
              (<any>slidingItem).close();
            }
          }
        ]
      });
      // now present the alert on top of all other content
      await alert.present();
    }
  }

  async removeFavorite(event: MouseEvent, session: Session, title: string) {
    const alert = await this.$ionic.alertController.create({
      header: title,
      message: "Would you like to remove this session from your favorites?",
      buttons: [
        {
          text: "Cancel",
          handler: () => {
            // they clicked the cancel button, do not remove the session
            // close the sliding item and hide the option buttons
            const slidingItem = (event.target as HTMLElement).closest(
              "ion-item-sliding"
            );
            (<any>slidingItem).close();
          }
        },
        {
          text: "Remove",
          handler: () => {
            // they want to remove this session from their favorites
            this.$store.dispatch("removeFavorite", session.id);

            // close the sliding item and hide the option buttons
            const slidingItem = (event.target as HTMLElement).closest(
              "ion-item-sliding"
            );
            (<any>slidingItem).close();
          }
        }
      ]
    });
    // now present the alert on top of all other content
    await alert.present();
  }

  goToSessionDetail(session: Session) {
    this.$router.push({
      name: "session-detail",
      params: { sessionId: session.id.toString() }
    });
  }
  async presentFilter() {
    const modal = await this.$ionic.modalController.create({
      component: SessionListFilter,
      componentProps: {
        excludedTracks: this.$store.state.sessions.trackFilters,
        allTracks: this.$store.getters.allTracks
      }
    });
    await modal.present();

    const { data } = await modal.onWillDismiss();
    if (data) {
      this.$store.dispatch("updateTrackFilters", data);
    }
  }
  updateSegment(e: CustomEvent) {
    this.segment = e.detail.value;
  }
  updateSearchTerm(e: CustomEvent) {
    this.$store.dispatch("setSearchText", e.detail.value);
  }
  async openSocial(network: string) {
    const loading = await this.$ionic.loadingController.create({
      message: `Posting to ${network}`,
      duration: Math.random() * 1000 + 500
    });
    await loading.present();
    await loading.onWillDismiss();
    this.$refs.fab.close();
  }
}
</script>
