 <v-menu :close-on-content-click="false" offset-x>
        <template v-slot:activator="{ on, attrs }">
          <v-btn v-bind="attrs" x-small class="ml-5 pt-4 pb-4" v-on="on">
            <v-icon small>
              mdi-dots-vertical
            </v-icon>
          </v-btn>
        </template>
        <v-card class="mt-0 mb-0">
          <v-list>
            <v-list-item
              v-for="(item, index) in dashEmitList"
              :key="index"
              style="min-height: 30px;"
              color="blue"
              @click="dotsmenu(item.emitname)"
            >
              <v-list-item-title>{{ item.title }}</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-card>
      </v-menu>
