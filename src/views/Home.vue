<template>
  <div id="contentWrapper">
    <div id="uploadWrapper">
      <input type="file" name="file" id="file" @change="uploadFile" ref="file" />
      <label id="file-select-btn" for="file">Select a certification file</label>
    </div>
    <div v-if="isLoading" id="loadingWrapper">
      <img src="../assets/loading.gif" />
      <div id="loadingState">{{state}}</div>
    </div>
    <div v-if="isCertReady" id="certRenderWrapper">
      <div id="certContentWrapper">
        <p>This is to certify that</p>
        <p id="studentName">{{ certData.recipient.name}}</p>
        <p>has successfully completed the</p>
        <p id="courseName">{{ certData.recipient.course }}</p>
        <p id="issueDate">Issued on : {{ certData.issuedOn }}</p>
        <p id="issuer">By : {{ certData.issuers[0].name }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import { verify, isValid } from "@govtechsg/opencerts-verify";
import { getData } from "@govtechsg/open-attestation";
import { utils } from "@govtechsg/oa-verify";
import * as document from "../sample/demo.json";

const providerOptions = {
  network: "ropsten",
  providerType: "infura",
  url: "https://ropsten.infura.io/v3/d0def9a28dfb4337bd7315a2a9bcc22c",
  apiKey: "d0def9a28dfb4337bd7315a2a9bcc22c",
}

const provider = utils.generateProvider(providerOptions);

const ropstenVerify = verify({
  provider: provider,
});

export default {
  name: "FileVerifier",
  data() {
    return {
      file: null,
      fileJson: null,
      state:'',
      isCertValid:false,
      isLoading:false,
      isCertReady:false,
      certData:null
    };
  },
  methods: {
    async uploadFile() {

      this.isLoading = true;
      this.state = 'File Reading..';
      this.file = this.$refs.file.files[0];
      const fr = new FileReader();

      fr.onload = async (e) => {

        // 1. extract data from cert
        this.state = 'Extracting file..';
        const result = JSON.parse(e.target.result.trim());
        const data = getData(result);
        console.log("certData %o", data);
        this.certData = data;

        // 2. verify cert
        this.state = 'Verifying file..';
        const fragments = await ropstenVerify(
          result
        );
        this.isCertValid = isValid(fragments)

        // 3. render
        this.state = this.isCertValid ? 'Cert is valid':'Cert is not valid';
        setTimeout(async ()=>{
          await this.render()
        },2000)

      };

      fr.readAsBinaryString(this.file)
    },
    async render(){
      this.isLoading = false
      this.isCertReady = true
    }
  },
};
</script>

<style scoped>
#contentWrapper{
  margin: auto;
  text-align: center;
}

#uploadWrapper {
  padding-top: 10vh;
}

#file{
  display: none;
}

#file-select-btn{
  padding: 0.75rem 2rem;
  border-radius: 0.75rem;
  background-color: black;
  color: white;
}

#loadingWrapper{
  margin-top: 3rem;
}

#loadingWrapper img{
  width: 36px;
}

#certRenderWrapper{
  margin:auto;
  margin-top: 3rem;
  width: 800px;
  height: 400px;
  align-content: center;
  border: 1px solid black;
  padding: 30px;
  color: white;
  background: #00c6ff; /* fallback for old browsers */
  background: -webkit-linear-gradient(45deg, #00c6ff, #0072ff); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(45deg, #00c6ff, #0072ff); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */;
}

#certContentWrapper{
  margin-top: 50px;
}
</style>
