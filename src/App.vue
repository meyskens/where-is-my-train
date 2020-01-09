<template>
  <div class="page-container">
    <md-app md-mode="reveal">
      <md-app-toolbar class="md-primary">
        <span class="md-title">Where is my train?</span>
      </md-app-toolbar>

      <md-app-content>
        <router-view/>
      </md-app-content>
    </md-app>
    <footer>
      <span>This site is built by Maartje Eyskens, data from NMBS/SNCB Train Map </span>
      <a href="https://github.com/meyskens/where-is-my-train">Code on Github</a>
    </footer>
  </div>
</template>
<style>
  footer {
    margin: 20px;
  }
</style>