<template>
  <div class="col-12">
    <div class="card contain-card text-black-50 bg-white mb-4">
      <div class="card-header">Announcements</div>
      <div class="card-body pt-0">
        <table class="table table-striped">
          <thead>
            <tr>
                <th scope="col">Title</th>
                <th scope="col" class="d-none d-md-table-cell">Service</th>
                <th scope="col" class="d-none d-md-table-cell">Begins</th>
                <th scope="col"></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="message in $store.getters.messages" v-bind:key="message.id">
              <td>{{message.title}}</td>
              <td class="d-none d-md-table-cell">
                <router-link :to="serviceLink(service(message.service))">{{serviceName(service(message.service))}}</router-link>
              </td>
              <td class="d-none d-md-table-cell">{{niceDate(message.start_on)}}</td>
              <td class="text-right">
                <div v-if="$store.state.admin" class="btn-group">
                  <a @click.prevent="editMessage(message, edit)" href="#" class="btn btn-outline-secondary"><i class="fas fa-exclamation-triangle"></i> Edit</a>
                  <a @click.prevent="deleteMessage(message)" href="#" class="btn btn-danger"><font-awesome-icon icon="times" /></a>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <FormMessage v-if="$store.state.admin" :edit="editChange" :in_message="message"/>
  </div>
</template>

<script>
  import Api from "../../API"
  import FormMessage from "../../forms/Message";

  export default {
  name: 'DashboardMessages',
    components: {FormMessage},
    data () {
    return {
      edit: false,
      message: {}
    }
  },
  methods: {
    editChange(v) {
      this.message = {}
      this.edit = v
    },
    editMessage(m, mode) {
      this.message = m
      this.edit = !mode
    },
    service (id) {
      return this.$store.getters.serviceById(id) || {}
    },
    serviceName (service) {
      return service.name || "Global Message"
    },
    async deleteMessage(m) {
      let c = confirm(`Are you sure you want to delete message '${m.title}'?`)
      if (c) {
        await Api.message_delete(m.id)
        const messages = await Api.messages()
        this.$store.commit('setMessages', messages)
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
