<template>
  <div id="uploadWrapper">
    {{ title }}
    <input type="file" @change="uploadFile" ref="file" />
    <button @click="submitFile">Upload!</button>
  </div>
</template>

<script>
// import {verificationBuilder, verify, openAttestationVerifiers, isValid} from "@govtechsg/oa-verify";
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
      title: "upload opencert file",
      file: null,
      fileJson: null,
    };
  },
  methods: {
    uploadFile() {
      this.file = this.$refs.file.files[0];
      const fr = new FileReader();
      
      fr.onload = async (e) => {
        const result = JSON.parse(e.target.result.trim());
        console.log("event %o", result);
        this.fileJson = result;
        //const verify1 = verificationBuilder(openAttestationVerifiers, {network: "rinkeby"});
        /* const verify1 = verificationBuilder(openAttestationVerifiers, {
          network: "ropsten",
        });*/
        console.log("file content %o", result);
        console.log("data %o", result);
        const fragments = await ropstenVerify(
          result
        );
        //console.log()
        console.log("- fragments %o", fragments)
        //console.log(isValid(fragments, ["DOCUMENT_INTEGRITY"])); // output true
        //console.log(isValid(fragments, ["DOCUMENT_STATUS"])); // output false
        //console.log(isValid(fragments, ["ISSUER_IDENTITY"])); // outpute false
        //console.log(isValid(fragments)); // output false
        // console.log("- data %o", _document);
        const documentData = getData(result);
        console.log("document data %o", documentData);
        console.log(isValid(fragments));
        // this.fileJson.default = result;
      };
      fr.readAsBinaryString(this.file)
      //fr.readAsText(this.file);
    },
    async submitFile() {
      //this.customVerifier(this.file)
      console.log("this.fileJson %o", this.fileJson)
      //this.customVerifier(this.fileJson.toJSON())
      //console.log("local document %o",document)
      //this.customVerifier(document);
    },
    async customVerifier(_document) {
      //const verify1 = verificationBuilder(openAttestationVerifiers, {network: "rinkeby"});
      /* const verify1 = verificationBuilder(openAttestationVerifiers, {
        network: "ropsten",
      });*/
      console.log("file content %o", _document);
      console.log("data %o", _document);
      const fragments = await ropstenVerify(
        _document
      );
      //console.log()
      console.log("- fragments %o", fragments)
      //console.log(isValid(fragments, ["DOCUMENT_INTEGRITY"])); // output true
      //console.log(isValid(fragments, ["DOCUMENT_STATUS"])); // output false
      //console.log(isValid(fragments, ["ISSUER_IDENTITY"])); // outpute false
      //console.log(isValid(fragments)); // output false
      // console.log("- data %o", _document);
      const documentData = getData(_document.default);
      console.log("document data %o", documentData);
    },
  },
};
</script>

<style scoped>
#uploadWrapper {
  padding-top: 15vh;
}
</style>
