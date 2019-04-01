<template>
  <div>
    <slot>
      <label :class="options.cssBtnClass" :for="options.inputID" ref="startUpload">
        {{ options.buttonText }}
      </label>
    </slot>
    <input
      :accept="accept"
      :id="options.inputID"
      :multiple="multiple"
      @change="doUpload($event.target.files)"
      class="form-control"
      style="display: none;"
      type="file"
    />
  </div>
</template>

<script>
  export default {
    props: {
      options: {
        type: Object,
        default: {
          url: '/upload',
          inputID: 'uploader-for-files',
          cssBtnClass: 'btn btn-success',
          btnText: 'Select file'
        }
      },
      multiple: {
        type: Boolean,
        default: false
      },
      imagesOnly: {
        type: Boolean,
        default: false
      },
      mimeTypes: {
        type: String,
        default: ''
      },
    },
    mounted: function mounted() {
      var $this = this;
      if ($this.$refs.startUpload != null && $this.allowToUpload) {
        $this.$refs.startUpload.addEventListener('click', function (e) {
          e.preventDefault();
          var hiddenInput = document.getElementById($this.inputID);
          hiddenInput.click();
        });
      }
    },
    data: function data() {
      return {
        allowToUpload: true
      };
    },
    methods: {
      doUpload: function (files) {
        var $this = this;

        if (!files) {
          return;
        }

        this.allowToUpload = false;
        var formData = new FormData();

        for (var i in files) {
          var file = files[i];
          if ($this.multiple) {
            formData.append('files[]', file, file.name);
          } else {
            formData.append('file', file, file.name);
          }
        }

        return this.$axios.$post(options.url, formData);
      },
      clear: function clear() {
        document.getElementById(this._uid).value = '';
      },
    },
    computed: {
      accept: function accept() {
        if (this.imagesOnly) {
          return 'image/*';
        }
        if (this.mimeTypes.length > 0) {
          return this.mimeTypes;
        }
        return '*';
      }
    }
  }
</script>
