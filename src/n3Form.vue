<template>
  <form :class="classObj"  @submit.prevent="noop">
      <slot></slot>
  </form>
</template>

<script>
export default {
  props: {
    type: {
      type: String,
      default: 'horizontal'
    },
    validate: {
      type: Boolean,
      default: false
    },
    result: {
      type: Object,
      twoWay: true
    },
    prefixCls: {
      type: String,
      default: 'n3'
    }
  },

  methods: {
    noop () {
    }
  },

  watch: {
    validate (val) {
      this.$broadcast('n3@openValidate', val)
      if (val) {
        this.result = this._result
      } else {
        this.result = {results: {}, isvaild: true}
      }
    }
  },

  ready () {
    if (!this.validate) {
      this.result = {results: {}, isvaild: true}
    }
    this.$broadcast('n3@openValidate', this.validate)
  },

  computed: {
    classObj () {
      let {prefixCls, type} = this
      let klass = {}

      klass[prefixCls + '-form-horizontal'] = type === 'horizontal'
      klass[prefixCls + '-form-inline'] = type === 'inline'
      klass['clearfix'] = true

      return klass
    }
  },

  events: {
    'n3@validateChange' (val) {
      let name = val.name
      let validateResult = Object.assign({}, this._result)

      if (!validateResult.results)validateResult.results = {}
      validateResult.results[name] = val.result

      validateResult.isvalid = true

      for (let i in validateResult.results) {
        if (!validateResult.results[i]['isvalid']) {
          validateResult.isvalid = false
          break
        }
      }

      this._result = validateResult

      if (this.validate) {
        this.result = this._result
      }
    }
  },

  data () {
    return {
      _result: {results: {}, isvaild: true}
    }
  }
}
</script>
