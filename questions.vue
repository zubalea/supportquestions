const path = require('path')
const appConfig = require('./src/app.config')

var allowedHosts = process.env.VUE_APP_DEV_ALLOWED_HOSTS.split(',')

module.exports = {

  configureWebpack: {
    devtool: 'source-map',
    devServer: {
      allowedHosts: allowedHosts,
      headers: {
        'Cache-Control': 'no-cache, no-transform',
      },
    },
  },
  chainWebpack(config) {
    // We provide the app's title in Webpack's name field, so that
    // it can be accessed in index.html to inject the correct title.
    config.set('name', appConfig.title)

    // Set up all the aliases we use in our app.
    // config.resolve.alias.clear().merge(require('./aliases.config').webpack)
    config.resolve.alias.set(
      'vue$',
      path.resolve(__dirname, 'node_modules/vue/dist/vue.esm.js')
    )

    // Don't allow importing .vue files without the extension, as
    // it's necessary for some Vetur autocompletions.
    config.resolve.extensions.delete('.vue')

    // Only enable performance hints for production builds,
    // outside of tests.
    config.performance.hints(
      process.env.NODE_ENV === 'production' &&
        !process.env.VUE_APP_TEST &&
        'warning'
    )
  },
  css: {
    // Enable CSS source maps.
    sourceMap: true,
  },
  // Configure Webpack's dev server.
  // https://cli.vuejs.org/guide/cli-service.html
  devServer: {
    proxy: {
      '/api': {
        target: process.env.VUE_APP_API_DJANGO_PROXY_TARGET,
        secure: false,
        // changeOrigin: true,
      },
    },
  },
  transpileDependencies: ['vuetify'],
  pluginOptions: {
    i18n: {
      locale: 'en',
      fallbackLocale: 'en',
      localeDir: 'locales',
      enableInSFC: false,
    },
  },
}
