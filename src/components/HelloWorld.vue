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
      <p>Select or drop file</p>
    </div>
    
    <div 
      class='submitButton'
      @click='sendButton'
    >
      <p>Get URL</p>
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
      <div class='copyLink' @click='copyLink' v-if='this.downloadLink != ""'>
        {{this.downloadLink}}
        <svg style='width: 1vw;' xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512"><!--!Font Awesome Free 6.5.1 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license/free Copyright 2024 Fonticons, Inc.--><path d="M208 0H332.1c12.7 0 24.9 5.1 33.9 14.1l67.9 67.9c9 9 14.1 21.2 14.1 33.9V336c0 26.5-21.5 48-48 48H208c-26.5 0-48-21.5-48-48V48c0-26.5 21.5-48 48-48zM48 128h80v64H64V448H256V416h64v48c0 26.5-21.5 48-48 48H48c-26.5 0-48-21.5-48-48V176c0-26.5 21.5-48 48-48z"/></svg>
      </div>
      
      
    
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
      loading: false,
      downloadLink: ""
    }
  },
  methods: {
    sendButton() {
      if (this.files.length != 0) {
        this.loading = true
        const fileInput = document.getElementById('fileInput');
        const files = fileInput.files;
        const formData = new FormData();
        for (let i = 0; i < files.length; i++) {
            formData.append('files[]', files[i]);
        }
        
        axios.post('http://'+ location.host +':5000/sharing', formData, {
          onUploadProgress: (progressEvent) => {
            let progress = document.querySelector('.progressLoad')
            const { loaded, total } = progressEvent;
            let percent = Math.floor((loaded * 100) / total);
            this.progressUploading = percent
            if (this.loading == true) {
              progress.style.background = `linear-gradient(to right, #84ffc8 ${this.progressUploading}%, white 0%)`
            }
            
            
          }
        })
          .then(res => {
            this.downloadLink = res.data.message
            let progress = document.querySelector('.progressLoad')
            progress.style.background = `linear-gradient(to right, #84ffc8 100%, white 0%)`
            this.loading = false
            
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
        inputText.innerHTML = "File is selected"
        toast.success(`You add a new file with name: ` + this.files[0].name, {
          timeout:4000
        })
      } 
    },
    copyLink() {
      let toast = useToast()
      document.body.insertAdjacentHTML('beforeend', `<input id="temp-copy" value = ${this.downloadLink}>`);
      let copyText = document.getElementById('temp-copy');
      copyText.select();
      document.execCommand("copy");
      copyText.remove();
      toast.success("URL successfully copied!", {timeout: 2000})
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
  margin-bottom: 5px;
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
.input_box p {
    font-size: 2.5vh;
  }
.copyLink {
  border: 2px solid #000000;
  display: inline-block;
  padding: 6px 7px 6px 7px;
  border-radius:5px;
  transition: 0.3s;
  cursor: pointer;
}
.copyLink:hover {
  background-color: #000000;
  color: white;
  transition: 0.3s;
  svg {
    fill:white;
  }
}
@media (max-width: 768px) {
  .input_box {
    width: 100%;
    height: 7vh;
  }
  .progressLoad {
    width: 100%;
    height: 7vh;
  }
  .input_box p {
    font-size: 2vh;
  }
  .submitButton {
    width:50%;

  }
  .hello {
    width: 100%;
  }
}
</style>
