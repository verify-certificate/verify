<template>
  <div class="validate-container mt-3">
      <h5><strong>Webinar of MAED students in Supervision and Administration</strong></h5>
      <label for="exampleFormControlInput1" class="form-label" >Code</label>
      <input class="form-control form-control-lg" type="text" aria-label=".form-control-lg example" v-model="form.code">
      <template v-if="err">
        <div class="alert alert-danger mt-1" role="alert">{{errMessage}}</div>
      </template>

      

      <template v-if="verified.name.length !== 0">
        <div class="alert alert-success mt-1" role="alert" style="font-size:12px;text-align:left">

          <h5 class="card-title">Verified!</h5>
          
          <h6 class="card-title"><strong>Name</strong> : {{verified.name}}</h6>
            <h6 class="card-subtitle"><strong>Email</strong> : {{verified.email}}</h6>
            <p class="card-text m-0"><strong>Course</strong> : {{verified.course}}</p>
            <p class="card-text m-0"><strong>Major</strong> : {{verified.major}}</p>
            <p class="card-text m-0"><strong>A.Y.</strong> : {{verified.annual_year}}</p>
            <p class="card-text m-0"><strong>Date given</strong> : {{verified.given_date}}</p>
            <p class="card-text m-0"><strong>Professor</strong> : {{verified.professor}}</p>
            </div>


      </template>    
      <template v-if="loading">
        <div class="spinner-grow" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
      </template>
      <template v-else>
        <button type="button" @click="validateCode" class="btn btn-success m-1">Verify</button>
      </template>  
  </div>
</template>

<script>
import {axios} from '@bundled-es-modules/axios'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      loading : false,
      err : false,
      errMessage : "Please enter code",
      form: {
        code : ""
      },
      data : [],
      verified : {
        name : "",
        email : "",
        given_date : "",
        course : "",
        major : "",
        annual_year : "",
        professor : ""
      }
    }
  },

  methods : {
    initForm(){
      return {
        name : "",
        email : "",
        given_date : "",
        course : "",
        major : "",
        annual_year : "",
        prefessor : ""
      }
    },
    validateCode(){
      const self = this;
// console.log("init")
//           this.getData().then((response) => {
//               console.log(response)
//           })
          
//         console.log("after")
      if(this.form.code.length !== 0){
        self.loading = true;
        
        this.getData().then((response) => {
              const codes = response.map((i) => i.code);

              // console.log(codes)
              if(codes.includes(self.form.code)){
                  self.err = false;

                  const dataIndex = codes.indexOf(self.form.code);

                  // console.log(dataIndex)
                  // console.log(response[dataIndex])

                  self.verified = response[dataIndex]


              }else{
                self.err = true;
                self.errMessage = "Invalid code";
                self.verified =  self.initForm();
              }

               self.loading = false;
          })
      }else{
        this.err = true;
        this.errMessage = "Please enter code";
      }
      
      // alert(1)
    },

    async getData(){
      const googleSheet = "https://spreadsheets.google.com/feeds/cells/1Q7T5S2L2k2vRh2GdibqnneOCD7TE4VE82wIqOFca6BU/1/public/full?alt=json";
          const gr = {}
          const data = []
          return await new Promise((resolve) => {
               axios.get(googleSheet).then((response) => {
                // console.log(response.data.feed.entry)

                if(response.data.feed.entry.length > 0){
                  response.data.feed.entry.forEach(element => {

                    const {col,row,$t} = element.gs$cell

                    console.log(element.gs$cell)


                    const column = parseInt(col);
                      const parsedRow = parseInt(row);

                      if(parsedRow === 1 && column <=8){
                        gr[$t] = "";
                      }

                      if(parsedRow > 1 && column <=8){
                        const row = gr;

                          const rowKeys = Object.keys(row);
                          const rowKey = rowKeys[column-1];
                          
                          row[rowKey]= $t

                          if(column === 8){
                            console.log(JSON.parse(JSON.stringify(row)))
                            data.push(JSON.parse(JSON.stringify(row)))
                          }
                      }
                    
                  });
                }

                resolve(data)

              })
          })

          
          


    }
  }
}
</script>

<style scoped>


</style>
