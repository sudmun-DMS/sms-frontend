<template>
  <div id="app">
<div class="container">

  <div style="position: fixed; z-index: -98; width: 100%; height: 100%; background-color:rgba(255, 255, 255,0)">
  
<div class="dms-app">
    <label for="from" ><b>Enter DOC ID</b></label>

    <br />
    <input class="input" type="text" v-on:keyup.enter="submitDoc(docId)" v-model="docId" name="docID" id="docID" style="height: 30px; width: 250px;"/>
    <br />
    <button class="button" id="document-search-submit" v-on:click="submitDoc(docId)">Submit</button>
    <br /><br />
    <div  >
    <div v-if="isError" id="errorMSG">
   {{msg}}
    </div>
    <div v-if="!isError" >
    <div class="" id="docIdResult">
    DOC ID: = {{resultMsg.docID}}
    </div>
    <div class="" id="link">
    link: = {{resultMsg.link}}
    </div>
    <div class="" id="status">
    Status: = {{resultMsg.status}}
    </div>
    <div class="" id="user">
    User: = {{resultMsg.user}}
    </div>
    <div class="" id="description" >
    Description: = {{resultMsg.description}}
    </div>
    </div>
    </div>
</div>



  </div>
  <div style="position: fixed; z-index: -99; width: 100%; opacity: .3; height: 100%; background-color:rgba(255, 255, 255,0)">
    <!-- <iframe frameborder="0" height="100%" width="100%"
      src="https://youtube.com/embed/F2HqJhiogfY?autoplay=1&loop=1&&controls=0&showinfo=0&autohide=1">
    </iframe>
    -->
  </div>




<vue-progress-bar></vue-progress-bar>
  </div></div>
</template>

<script>
import Axios from 'axios'
export default {
  
  name: 'app',
  data () {
    return {
       msg: 'Welcome to DMS App',
       docId:'',
       resultMsg : {
         docID:'',
         link:'',
         user:'',
         status:'',
         description:''
       },
       isError:true
    }
  },
  mounted () {
    //  [App.vue specific] When App.vue is finish loading finish the progress bar
    this.$Progress.finish()
  },
  methods: {
    success (docData) {
      //this.msg = "DOC ID: = " + docData.data.body.id + "link: =" + docData.data.body.link
      this.resultMsg.docID = docData.data.body.id 
      this.resultMsg.link = docData.data.body.link
      this.resultMsg.user = docData.data.body.user
      this.resultMsg.status = docData.data.body.status
      this.resultMsg.description = docData.data.body.description
    },
    failed (docData) {
      let errorCode = docData.data.header.code
      switch (errorCode){
        case 404: 
          this.msg = "DOC ID NOT FOUND" 
          break;
        case 500: 
          this.msg = "Please check Database connection" 
          break;
        default: 
          this.msg = "Error. Please check with administrator";
      }
      
    },
    submitDoc (id) {
      this.$Progress.start()
      if (!id.match(/\S/)){
         this.msg = "Please Enter DOC ID!!!"
          this.$Progress.fail()
      }
      else 
      {
        let baseURL = "http://188.166.218.202"         
        Axios.get(baseURL + "/document/"+id)
        .then( (response)=>{
          let returnCode = response.data.header.code
          if (returnCode == "200") {
            this.success (response)
            this.isError = false
          } else {
            this.isError = true
            this.failed (response)
            }
          console.log(response)
          this.$Progress.finish()
        })
        .catch(error => {
          console.log(error)
           this.$Progress.fail()
          alert(error)
        })
      
      }
      
    }
    
  }

}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color:black;
}

.dms-app{
  margin-top: 60px;
}

h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
