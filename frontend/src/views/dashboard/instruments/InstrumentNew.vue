<template>
  <div>
    <dashboard>
      <!-- Title -->
      <template v-slot:page_title>
        {{pagetitle}}
      </template>
      <!-- Content -->
      <template v-slot:dashboard_content>
        <alert :message="messageData"></alert>
        <!-- Form -->
        <form v-on:submit.prevent="createInstrument" enctype=multipart/form-data>
          <!-- Row -->
          <div class="row mt-3">

            <!-- Backlink -->
            <div class="col-12 text-left">
              <back-link :url="returnPath" text="Instrumentos"></back-link>
            </div>
            <!-- /.BackLink -->

            <!-- Información del instrumento -->
            <div class="col-12 col-lg-6">
              <!-- Row -->
              <div class="row justify-content-end">
                <!-- Col 12 -->
                <div class="col-12">

                  <dashboard-title title="Informacion del instrumento"></dashboard-title>

                  <!-- Row -->
                  <div class="row">
                    <!-- Nombre -->
                    <div class="col-12">
                      <mdb-input label="Nombre" v-model="name" required />
                    </div>
                    <!-- Código -->
                    <div class="col-lg-6">
                      <mdb-input label="Código" v-model="code" required />
                    </div>

                    <!-- Tipo de instrumento -->
                    <div class="col-lg-6">
                      <div class="form-group">
                        <form-label name="Tipo de instrumento"></form-label>
                        <select class="browser-default custom-select" v-model="instrument_type_id" required>
                          <option disabled selected>Elegir</option>
                          <option v-for="i in instrument_types" :key="i.instrument_type_id" :value="i.instrument_type_id">{{i.name}}</option>
                        </select>
                      </div>
                    </div>

                    <div class="col-12 mt-5">
                      <dashboard-title title="Imagen del instrumento"></dashboard-title>
                    </div>
                    <!-- Imagen -->
                    <div class="col-lg-6">
                      <div class="input-group">
                        <div class="input-group-prepend">
                          <span class="input-group-text" id="image-span"><i class="fas fa-image"></i></span>
                        </div>
                        <div class="custom-file">
                          <input type="file" id="image" name="image" class="custom-file-input" aria-describedby="image-span" required >
                          <label class="custom-file-label" for="image">Elegir imagen</label>
                        </div>
                      </div>
                    </div>
                    <!-- /.Imagen -->

                  </div>
                  <!-- /.Row -->

                </div>
                <!-- /.Col 12 -->

                <!-- Col 12 -->
                <div class="col-12 col-lg-5 mt-4">
                  <button type="submit" class="btn seed-btn-a btn-block waves-effect mx-0">Cargar</button>
                </div>
                <!-- /.Col 12 -->

              </div>
              <!-- /.Row -->
            </div>
            <!-- /.Información del instrumento -->
          </div>
          <!-- /.Row -->
        </form>
        <!-- /.Form -->
      </template>
    </dashboard>
  </div>
</template>

<script>
import axios from 'axios'
import { mdbInput } from 'mdbvue'
import Dashboard from '@/views/Dashboard'
import dashboardTitle from '@/components/dashboard/Title'
import formLabel from '@/components/Label'
import backLink from '@/components/dashboard/buttons/BackLink'
import alert from '@/components/Alert'

export default {
  data () {
    return {
      pagetitle: 'Cargar un nuevo instrumento',
      returnPath: '/instruments',
      instrument: '',
      messageData: {},
      // Form values for select
      instrument_types: {},
      // Docente form information
      name: '',
      code: '',
      instrument_type_id: ''
    }
  },
  created () {
    this.fetchFormData()
  },
  components: {
    mdbInput,
    'dashboard': Dashboard,
    'dashboard-title': dashboardTitle,
    'form-label': formLabel,
    'back-link': backLink,
    'alert': alert
  },
  methods: {
    fetchFormData () {
      const path = '/api/instrument/form_data'
      axios.get(path).then((res) => {
        this.instrument_types = res.data.instrument_types
      }).catch((error) => {
        this.fetchFormData()
        console.log(error)
      })
    },
    restoreValues () {
      this.name = ''
      this.code = ''
      this.instrument_type_id = ''
    },
    createInstrument () {
      const path = '/api/instrument/create'

      var formData = new FormData()
      var imagefile = document.querySelector('#image')
      formData.append('image', imagefile.files[0])
      formData.append('name', this.name)
      formData.append('code', this.code)
      formData.append('instrument_type_id', this.instrument_type_id)

      axios.post(path, formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }).then((res) => {
        if (res.data.status === 'success') {
          this.restoreValues()
        }
        this.messageData = res.data
        var $this = this
        setTimeout(function () {
          $this.messageData = false
        }, 4000)
      }).catch((error) => {
        console.log(error)
      })
    }
  }
}
</script>
