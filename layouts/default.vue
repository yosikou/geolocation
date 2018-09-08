<template>
  <v-app>
    <v-toolbar class="deep-purple">
      <v-toolbar-side-icon @click.stop="drawer = !drawer" dark></v-toolbar-side-icon>
      <v-toolbar-title dark>ちゅーとりある</v-toolbar-title>
      <v-toolbar-items>
        <v-btn flat dark>vue</v-btn>
        <v-btn flat dark>vuex</v-btn>
        <v-btn flat dark>Nuxt</v-btn>
      </v-toolbar-items>
    </v-toolbar>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
    <v-navigation-drawer temporary v-model="drawer" :mini-variant="mini" light absolute>
      <v-toolbar flat class="transparent">
        <v-list class="pa-0">
          <v-list-tile avatar>
            <vlist-tile-avatar>
              <img src="https://randomuser.me/api/portraits/men/85.jpg" />
            </vlist-tile-avatar>
            <v-list-tile-content>
              <v-list-tile-title>Jon Leider</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
      </v-toolbar>
      <v-list class="pt-0" dense>
        <v-list-tile>
          <v-list-tile-action>
            <v-icon></v-icon>
          </v-list-tile-action>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-footer fixed app>
      <span>&copy; 2018 DACS co,</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  data () {
    return {
      drawer: false,
      mini: false
    }
  }
}
</script>
