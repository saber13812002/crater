<template>
  <form @submit.prevent="saveEmailConfig()">
    <div class="row">
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.driver') }}</label>
        <span class="text-danger"> *</span>
        <base-select
          v-model="mailConfigData.mail_driver"
          :invalid="$v.mailConfigData.mail_driver.$error"
          :options="mailDrivers"
          :searchable="true"
          :allow-empty="false"
          :show-labels="false"
          @input="onChangeDriver"
        />
        <div v-if="$v.mailConfigData.mail_driver.$error">
          <span v-if="!$v.mailConfigData.mail_driver.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
        </div>
      </div>
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.host') }}</label>
        <span class="text-danger"> *</span>
        <base-input
          :invalid="$v.mailConfigData.mail_host.$error"
          v-model.trim="mailConfigData.mail_host"
          type="text"
          name="mail_host"
          @input="$v.mailConfigData.mail_host.$touch()"
        />
        <div v-if="$v.mailConfigData.mail_host.$error">
          <span v-if="!$v.mailConfigData.mail_host.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
        </div>
      </div>
    </div>
    <div class="row my-2">
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.port') }}</label>
        <span class="text-danger"> *</span>
        <base-input
          :invalid="$v.mailConfigData.mail_port.$error"
          v-model.trim="mailConfigData.mail_port"
          type="text"
          name="mail_port"
          @input="$v.mailConfigData.mail_port.$touch()"
        />
        <div v-if="$v.mailConfigData.mail_port.$error">
          <span v-if="!$v.mailConfigData.mail_port.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
          <span v-if="!$v.mailConfigData.mail_port.numeric" class="text-danger">
            {{ $tc('validation.numbers_only') }}
          </span>
        </div>
      </div>
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.encryption') }}</label>
        <span class="text-danger"> *</span>
        <base-select
          v-model.trim="mailConfigData.mail_encryption"
          :invalid="$v.mailConfigData.mail_encryption.$error"
          :options="encryptions"
          :searchable="true"
          :show-labels="false"
          @input="$v.mailConfigData.mail_encryption.$touch()"
        />
        <div v-if="$v.mailConfigData.mail_encryption.$error">
          <span v-if="!$v.mailConfigData.mail_encryption.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
        </div>
      </div>
    </div>
    <div class="row my-2">
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.from_mail') }}</label>
        <span class="text-danger"> *</span>
        <base-input
          :invalid="$v.mailConfigData.from_mail.$error"
          v-model.trim="mailConfigData.from_mail"
          type="text"
          name="from_mail"
          @input="$v.mailConfigData.from_mail.$touch()"
        />
        <div v-if="$v.mailConfigData.from_mail.$error">
          <span v-if="!$v.mailConfigData.from_mail.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
          <span v-if="!$v.mailConfigData.from_mail.email" class="text-danger">
            {{ $tc('validation.email_incorrect') }}
          </span>
        </div>
      </div>
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.from_name') }}</label>
        <span class="text-danger"> *</span>
        <base-input
          :invalid="$v.mailConfigData.from_name.$error"
          v-model.trim="mailConfigData.from_name"
          type="text"
          name="name"
          @input="$v.mailConfigData.from_name.$touch()"
        />
        <div v-if="$v.mailConfigData.from_name.$error">
          <span v-if="!$v.mailConfigData.from_name.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
        </div>
      </div>
    </div>
    <div class="row my-2">
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.ses_key') }}</label>
        <span class="text-danger"> *</span>
        <base-input
          :invalid="$v.mailConfigData.mail_ses_key.$error"
          v-model.trim="mailConfigData.mail_ses_key"
          type="text"
          name="mail_ses_key"
          @input="$v.mailConfigData.mail_ses_key.$touch()"
        />
        <div v-if="$v.mailConfigData.mail_ses_key.$error">
          <span v-if="!$v.mailConfigData.mail_ses_key.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
        </div>
      </div>
      <div class="col-md-6 my-2">
        <label class="form-label">{{ $t('wizard.mail.ses_secret') }}</label>
        <span class="text-danger"> *</span>
        <base-input
          :invalid="$v.mailConfigData.mail_ses_secret.$error"
          v-model.trim="mailConfigData.mail_ses_secret"
          type="password"
          name="mail_ses_secret"
          show-password
          @input="$v.mailConfigData.mail_ses_secret.$touch()"
        />
        <div v-if="$v.mailConfigData.mail_ses_secret.$error">
          <span v-if="!$v.mailConfigData.mail_ses_secret.required" class="text-danger">
            {{ $tc('validation.required') }}
          </span>
        </div>
      </div>
    </div>
    <base-button
      :loading="loading"
      class="pull-right mt-4"
      icon="save"
      color="theme"
      type="submit"
    >
      {{ $t('general.save') }}
    </base-button>
  </form>
</template>
<script>
import MultiSelect from 'vue-multiselect'
import { validationMixin } from 'vuelidate'
const { required, email, numeric } = require('vuelidate/lib/validators')

export default {
  components: {
    MultiSelect
  },
  mixins: [validationMixin],
  props: {
    configData: {
      type: Object,
      require: true,
      default: Object
    },
    loading: {
      type: Boolean,
      require: true,
      default: false
    },
    mailDrivers: {
      type: Array,
      require: true,
      default: Array
    }
  },
  data () {
    return {
      mailConfigData: {
        mail_driver: '',
        mail_host: '',
        mail_port: null,
        mail_ses_key: '',
        mail_ses_secret: '',
        mail_encryption: 'tls',
        from_mail: '',
        from_name: ''
      },
      encryptions: ['tls', 'ssl', 'starttls']
    }
  },
  validations: {
    mailConfigData: {
      mail_driver: {
        required
      },
      mail_host: {
        required
      },
      mail_port: {
        required,
        numeric
      },
      mail_ses_key: {
        required
      },
      mail_ses_secret: {
        required
      },
      mail_encryption: {
        required
      },
      from_mail: {
        required,
        email
      },
      from_name: {
        required
      }
    }
  },
  mounted () {
    for (const key in this.mailConfigData) {
      if (this.configData.hasOwnProperty(key)) {
        this.mailConfigData[key] = this.configData[key]
      }
    }
  },
  methods: {
    async saveEmailConfig () {
      this.$v.mailConfigData.$touch()
      if (!this.$v.mailConfigData.$invalid) {
        this.$emit('submit-data', this.mailConfigData)
      }

      return false
    },
    onChangeDriver () {
      this.$v.mailConfigData.mail_driver.$touch()
      this.$emit('on-change-driver', this.mailConfigData.mail_driver)
    }
  }
}
</script>
