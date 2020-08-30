<template>
  <v-app>
    <v-navigation-drawer v-model="drawer" :clipped="clipped" fixed app>
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" color="primary" fixed app>
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <nuxt-link to="/" class="site-title"
        ><v-toolbar-title v-text="title"
      /></nuxt-link>
      <v-col cols="4" offset="1">
        <v-autocomplete
          v-model="model"
          :items="searchResults"
          :loading="isLoading"
          :search-input.sync="search"
          color="white"
          hide-no-data
          hide-selected
          class="mt-4"
          placeholder="Start typing to Search"
          item-text="title"
          item-value="slug"
          return-object
        ></v-autocomplete>
      </v-col>
    </v-app-bar>
    <v-main>
      <v-container>
        <v-snackbar
          v-model="snackbar"
          color="accent"
          multi-line
          right
          timeout="2000"
          top
          vertical
        >
          {{ errText }}
        </v-snackbar>
        <nuxt />
      </v-container>
    </v-main>
  </v-app>
</template>

<style scoped>
.site-title {
  text-decoration: none;
  color: inherit;
}
</style>
<script>
export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: true,
      items: [],
      title: this.$config.siteName,
      isLoading: false,
      model: null,
      search: null,
      searchResults: [],
      searchCache: {},
      errText: null,
      snackbar: false,
    }
  },
  watch: {
    model(val) {
      if (!val) {
        return
      }

      this.model = null
      this.search = ''
      this.searchCache = {}
      this.isLoading = false

      this.$router.replace(val.path)
    },

    search(val) {
      if (!val) {
        return
      }

      if (this.searchCache[val]) {
        this.searchResults = this.searchCache[val]
        return
      }

      if (this.isLoading) {
        return
      }

      this.isLoading = true

      this.$content('blog')
        .only('title')
        .search(val)
        .fetch()
        .then((response) => {
          if (val === this.search) {
            this.searchResults = response
          }

          this.searchCache[val] = response
        })
        .catch((err) => {
          this.errText = err.message
          this.snackbar = true
        })
        .finally(() => {
          this.isLoading = false
        })
    },
  },
  mounted() {
    this.items = this.$config.categories.map((category) => {
      category.title = category.label
      category.to = '/category/' + category.value
      return category
    })
  },
}
</script>
