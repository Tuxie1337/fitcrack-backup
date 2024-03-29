<!--
   * Author : see AUTHORS
   * Licence: MIT, see LICENSE
-->

<template>
  <v-container class="max500">
    <fc-tile title="PCFG" class="ma-2">
      <v-alert :value="true" type="warning" class="mt-0 mb-1" >
        PCFG files must have a .zip or .rar extension.
      </v-alert>
      <v-data-table
        :headers="headers"
        :items="pcfg.items"
        :loading="loading"
        :rows-per-page-items="[10,25,50]"
        rows-per-page-text="pcfg files per page"
        disable-initial-sort
      >
        <template slot="items" slot-scope="props">
          <td><router-link :to="{name: 'pcfgDetail', params: { id: props.item.id}}">{{ props.item.name }}</router-link></td>
          <td class="text-xs-right">{{ $moment(props.item.time ).format('DD.MM.YYYY HH:mm') }}</td>
          <td class="text-xs-right">
            <a :href="$serverAddr + '/pcfg/' + props.item.id + '/download'" target="_blank">
              <v-btn outline fab small color="primary">
                <v-icon>file_download</v-icon>
              </v-btn>
            </a>
          </td>
          <td class="text-xs-right">
            <v-tooltip top>
              <v-btn icon class="mx-0" @click="deletePcfg(props.item.id)" slot="activator">
                <v-icon color="error">delete</v-icon>
              </v-btn>
              <span>Delete pcfg file</span>
            </v-tooltip>
          </td>
        </template>
      </v-data-table>
      <v-divider></v-divider>
      <file-uploader :url="this.$serverAddr + '/pcfg/add'" @uploadComplete="loadPcfg"></file-uploader>

    </fc-tile>

  </v-container>
</template>

<script>
  import tile from '@/components/tile/fc_tile'
  import FileUploader from "@/components/fileUploader/fileUploader";
  export default {
    name: "pcfgView",
    components: {
      FileUploader,
      'fc-tile': tile,
    },
    mounted: function () {
      this.loadPcfg()
    },
    methods: {
      loadPcfg: function () {
        this.loading = true;
        this.axios.get(this.$serverAddr + '/pcfg', {}).then((response) => {
          this.pcfg = response.data;
          this.loading = false
        })
      },
      deletePcfg: function (id) {
        this.$root.$confirm('Delete', 'Are you sure?', { color: 'primary' }).then((confirm) => {
          this.loading = true;
          this.axios.delete(this.$serverAddr + '/pcfg/' + id).then((response) => {
            this.loadPcfg()
          })
        })
      }
    },
    data: function () {
      return {
        loading: false,
        pcfg: [],
        headers: [
          {
            text: 'Name',
            align: 'left',
            value: 'name'
          },
          {text: 'Added', value: 'time', align: 'right'},
          {text: 'Download', value: 'name', align: 'right'},
          {text: 'Delete', align: 'right'}
        ]
      }
    }
  }
</script>

<style scoped>
  .noEvent {
    pointer-events: none;
    display: inline-block;
  }

  .dz-message {
    cursor: pointer;
    text-align: center;
  }

  .max500 {
    max-width: 600px;
  }

</style>


<style>
  .selectedDict {
    background: rgba(37, 157, 173, 0.85) !important;
    color: white;
  }

  .selectedDict a {
    color: white;
  }

  .clickable {
    cursor: pointer;
  }
</style>
