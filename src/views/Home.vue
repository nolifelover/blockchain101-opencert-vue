<template>
  <div id="uploadWrapper">
    {{ title }}
    <input type="file" @change="uploadFile" ref="file">
    <button @click="submitFile">Upload!</button>
  </div>
</template>

<script>
import {verificationBuilder, verify, openAttestationVerifiers, isValid} from "@govtechsg/oa-verify";
import {getData} from "@govtechsg/open-attestation";
import * as document from "../sample/demo.json";

export default {
  name: "FileVerifier",
  data() {
    return {
      title: "upload opencert file",
      file: null,
      fileJson:null
    }
  },
  methods: {
    uploadFile() {
      this.file = this.$refs.file.files[0];
      const fr = new FileReader();



      fr.onload = e => {
        const result = JSON.parse(e.target.result);
        console.log("event %o", result)
        this.fileJson = {}
        this.fileJson.default = result
      }
      fr.readAsText(this.file);

    },
    async submitFile() {

      //this.customVerifier(this.file)
      //this.customVerifier(this.fileJson)
      //console.log("local document %o",document)
      this.customVerifier(document);
    },
    async customVerifier(_document) {
      //const verify1 = verificationBuilder(openAttestationVerifiers, {network: "rinkeby"});
      const verify1 = verificationBuilder(openAttestationVerifiers, {network: "ropsten"});
      console.log("file content %o",_document)
      const fragments = await verify(_document);
      //console.log("- fragments %o", fragments)
      //console.log(isValid(fragments, ["DOCUMENT_INTEGRITY"])); // output true
      //console.log(isValid(fragments, ["DOCUMENT_STATUS"])); // output false
      //console.log(isValid(fragments, ["ISSUER_IDENTITY"])); // outpute false
      //console.log(isValid(fragments)); // output false
      console.log("- data %o", _document)
      const documentData = getData(_document.default);
      console.log("document data %o", documentData)
    }
  }
}
</script>

<style scoped>
#uploadWrapper{
  padding-top: 15vh;
}
</style>
