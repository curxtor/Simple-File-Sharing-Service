<template>
  <p style='font-size: 22px; font-weight: bold;'>Simple file uploader with VueJS</p>
  <p style='font-size: 14px;'>developed by curxtor</p>
  <div class="hello">
    <div class='progressLoad' 
          v-if="loading"
    >
      
    </div>
    <div 
      class='input_box'
      @dragover="dragover"
      @dragleave="dragleave"
      @drop="drop"
      @click="inputClick"
      v-else
    >
      <p style='font-size: 22px;'>Select or drop file</p>
    </div>
    
    <div 
      class='submitButton'
      @click='sendButton'
    >
      <p>Получить ссылку</p>
    </div>
    <input
        type="file"
        name="file"
        id="fileInput"
        class="hidden-input"
        @change="onChange"
        ref="file"
        hidden
        multiple
      />
      
      
    
  </div>
</template>

<script>
import axios from 'axios'
import { useToast } from 'vue-toastification'
export default {
  name: 'HelloWorld',
  data() {
    return {
      uploaded: false,
      files: [],
      progressUploading: 0,
      loading: false
    }
  },
  methods: {
    sendButton() {
      if (this.files.length != 0) {
        const fileInput = document.getElementById('fileInput');
        const files = fileInput.files;
        const formData = new FormData();
        for (let i = 0; i < files.length; i++) {
            formData.append('files[]', files[i]);
        }
        this.loading = true
        axios.post('http://localhost:5000/sharing', formData, {
          onUploadProgress: (progressEvent) => {
            
            const totalLength = progressEvent.lengthComputable ? progressEvent.total : progressEvent.target.getResponseHeader('content-length') || progressEvent.target.getResponseHeader('x-decompressed-content-length');
            let progress = document.querySelector('.progressLoad')
            
            if (totalLength !== null) {
                this.progressUploading = Math.round((progressEvent.loaded * 100) / totalLength)
            }
            console.log(this.progressUploading)
            progress.style.background = `linear-gradient(to right, #84ffc8 ${this.progressUploading}%, white 0%)`
            if (this.progressUploading >= 80) {
              this.progressUploading = 100
              progress.style.background = `linear-gradient(to right, #84ffc8 100%, white 0%)`
              this.loading = false
            }
          }
        })
          .then(res => {
            console.log(res)
            
          })
          .catch(error => {
            console.log(error)
          })
        
      }
      
    },
    inputClick() {
      let input = document.querySelector('.hello input')
      input.click()
    },
    onChange(e) {
      let toast = useToast()
      if (e.target.files.length > 3) {
        toast.error(`You add more then 3 files!`)
      }
      else {
        this.files = this.$refs.file.files;
        let inputText = document.querySelector('.input_box p')
        inputText.innerHTML = "Файл успешно загружен"
        toast.success(`You add a new file with name: ` + this.files[0].name, {
          timeout:4000
        })
      }
      
      
    },
    dragover(e) {
      e.preventDefault();
      e.target.classList.add('input_box-hover')
      this.isDragging = true;
    },
    dragleave(e) {
      this.isDragging = false;
      e.target.classList.remove('input_box-hover')
    },
    drop(e) {
      e.preventDefault();
      this.$refs.file.files = e.dataTransfer.files;
      this.onChange();
      this.isDragging = false;
      e.target.classList.remove('input_box-hover')
    }
  },
  mounted() {
    
  }

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only #8effcc;-->
<style scoped>
.submitButton {
  border-radius: 10px;
  border-color: #42b983;
  border-width: 2px;
  border-style: solid;
  background-color:#ffffff;
  width:20%;
  margin: 0 auto;
  transition: 0.3s;
  cursor:pointer;
}
.submitButton:hover {
  background-color: #42b983;
  transition: 0.3s;
  color:white;
}
.input_box {
  background-color: rgb(201, 255, 218);
  width:50%;
  margin: 0 auto;
  margin-bottom:15px;
  text-align: center;
  border-radius: 10px;
  border-color: #42b983;
  border-width: 2px;
  border-style: solid;
  transition: 0.1s;
  cursor: pointer;
  height: 8vh;
  
}
.input_box:hover {
  transition: 0.3s;
  background-color: #42b983;
  color:white;
}
.hello {
  margin: 0 auto;
  width:50%;
}
.input_box-hover {
  transition: 0.3s;
  background-color: #42b983;
  color:white;
}
a {
  color: #42b983;
}
.progressLoad {
  width:50%;
  margin: 0 auto;
  margin-bottom:15px;
  text-align: center;
  border-radius: 10px;
  border-color: #42b983;
  border-width: 2px;
  border-style: solid;
  transition: 0.1s;
  height: 8vh;
}
</style>
