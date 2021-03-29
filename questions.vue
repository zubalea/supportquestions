I get this error in many of .vue files.   Just showing one because they are all the samme error

ERROR START ***********************
error  in ./src/views/common/_timeout.vue?vue&type=style&index=0&lang=sass&module=true&

Syntax Error: SassError: Can't find stylesheet to import.
  ╷
1 │ @import '@/styles/variables.sass'
  │         ^^^^^^^^^^^^^^^^^^^^^^^^^
  ╵
  src/views/common/_timeout.vue 1:9  root stylesheet
ERROR END *****************************
  
**** file: veutify.js   (note @src are aliased to src)
import '@mdi/font/css/materialdesignicons.css'
import Vue from 'vue'
import Vuetify from 'vuetify/lib'
import i18n from '@src/i18n'
  
**** app.vue
<style lang="sass">
@import '~vuetify/src/styles/styles.sass'

@media #{map-get($display-breakpoints, 'md-and-down')}
  .custom-class
    display: none
</style>


**** _timeout.vue
<style lang="sass" module>
.title
  text-align: center
</style>

*** src/styles/variables.sass
// simple test for now
$body-font-family: 'Work Sans', serif;




