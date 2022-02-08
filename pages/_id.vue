<template>
  <div>
    <survey v-if="surveyBody" :survey="survey"></survey>
<!--    <span>{{surveyBody}}</span>-->
  </div>
</template>

<script lang="ts">
import {Component, Vue} from 'vue-property-decorator'
import * as SurveyVue from "survey-vue";
import "bootstrap/dist/css/bootstrap.css";
var Survey: any = SurveyVue.Survey;
Survey.cssType = "bootstrap";

SurveyVue.Serializer.addProperty("question", "tag:number");

@Component({
  components: {
    Survey
  },
  async asyncData({ params, query, $axios }){
    console.log('Running hook')
    const surveyBody = await $axios.$get(`https://1kkzlx54m5.execute-api.us-east-1.amazonaws.com/production/survey/${params.id}/body`, {
      headers: {
        Authorization: query.auth
      }
    })
    return { surveyBody }
  }
})
export default class AnswerPage extends Vue {

  json: any = null;

  id = ''
  authToken = ''

  async mounted() {
    // this.id = this.$route.params.id as string
    // this.authToken = this.$route.query.auth as string
  }

  onComplete(e: any) {
    console.log(e)
  }

  get survey() {
    //@ts-ignore
    console.log(this.surveyBody)
    //@ts-ignore
    const model = new SurveyVue.Model(this.surveyBody);
    model.onCompleting.add(this.onComplete)
    return model
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
