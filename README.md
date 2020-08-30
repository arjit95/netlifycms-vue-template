# netlifycms-vue
> A simple template built with  [Vue.js](https://vuejs.org), [Netlify CMS](https://netlifycms.org), [Nuxt Content](https://content.nuxtjs.org), [Vuetify](https://vuetifyjs.com)

## Build Setup

```bash
# install dependencies
$ yarn install

# serve with hot reload at localhost:3000
$ yarn dev

# build for production and launch server
$ yarn build
$ yarn start

# generate static project
$ yarn generate

# local development
# run in root directory
$ npx netlify-cms-proxy-server
# in a separate terminal
$ yarn dev
```

For detailed explanation on how things work, check out [Nuxt.js docs](https://nuxtjs.org).

## Editing Content
- Edit posts by visiting `yourdomain.com/admin`.
- Add more categories in `nuxt.config.js` and `static/admin/config.yml`
- Add new authors from `yourdomain.com/admin/#/collections/authors`
- Edit site name in `nuxt.config.js`

### TODO
- Use common method across each page to fetch content.
