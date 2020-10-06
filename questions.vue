// From the Top Navigation Bar of the Vuetify Material Pro Template

// I am not familiar with the syntax of this type component render
components: {
    AppBarItem: {
      render(h) {
        return h(VHover, {
          scopedSlots: {
            default: ({ hover }) => {
              return h(
                VListItem,
                {
                  attrs: this.$attrs,
                  class: {
                    'black--text': !hover,
                    'white--text secondary elevation-12': hover,
                  },
                  props: {
                    activeClass: '',
                    dark: hover,
                    link: true,
                    ...this.$attrs,
                  },
                },
                this.$slots.default
              )
            },
          },
        })
      },
    },
  },
          
// This is the section of the code that puts the hightlights on the text.  The list comes from a data->profile
// Right now it hovers over each item, we put @click on v-list-item-title. It made the click area smaller than the hover area.
// How can we get the click to come from the full over section of appbaritem?
      <v-list :tile="false" flat nav>
        <template v-for="(p, i) in profile">
          <v-divider v-if="p.divider" :key="`divider-${i}`" class="mb-2 mt-2" />

          <AppBarItem v-else :key="`item-${i}`" :to="p.link">
            <v-list-item-title v-text="p.title" />
          </AppBarItem>
        </template>
      </v-list>
