<template>
  <ForcedChoiceScreen
    :options="options"
    :progress="progress"
    :text="text"
    :instructions="instructions"
    :task="task"
    :comprehension="comprehension"
    :group="group"
    :feedbackTime="-1"
    :question="question"
    >
      
      <template #stimulus>

        <div class="hintstext">
            <p v-if="trialType.includes('training')" >
              <b>{{instructions}}</b>
            </p>
        </div>

        <div v-bind:class = "comprehension?'comprehension':'stimulus'">    
          <br>  
          <br>      
          <div class="text">
            <p>
              {{text}}
            </p>
          </div>
          <br>
          <div v-if="task===true && group==='score'" class="score">
              <p>
              {{emoji(128226)}}  
              The AI says this comment is <b>{{parseFloat(trial.toxicity_score).toFixed(0)}}% likely to be toxic</b>.
              <br>
            </p>
          </div>
        </div>

        <Record
            :data="{
              trialID: trialnumber,
              textID: trial.id, 
              category: trial.category, 
              toxicityScore: trial.toxicity_score,
              phase: trialType,
              group: group,
              correctResponse: trial.correct_response,
              comprehensionResponse: trial.comprehension_vulnerable,
              response: $magpie.measurements.response,
              identityGroup: $magpie.measurements.identity,
              activismParticipation: $magpie.measurements.activism
            }"
          />
      </template>
      
      <!-- During training provide feedback to the participants about their answers -->
      <template  v-if="trialType.includes('training') && task===true" #feedback>
          <div class="feedbacktext">
          <p v-if="trial.correct_response === 'Reject' && $magpie.measurements.response === trial.correct_response">
              Correct! {{emoji(127881)}} 
              <!-- 128076 -  okay symbol  -->
              The comment should be rejected because<b>{{trial.explanation}}</b>.
              <button @click="$magpie.saveAndNextScreen()">Ok</button>
            </p>
            <!-- If the answer was incorrect provide an explanation why -->
            <p v-else-if="trial.correct_response === 'Reject' && $magpie.measurements.response !== trial.correct_response">
              Incorrect... {{emoji(128579)}}
              The comment should be rejected because <b>{{trial.explanation}}</b>.
              <button @click="$magpie.saveAndNextScreen()">Ok</button>
            </p>
            <p v-else-if="trial.correct_response === 'Approve' && $magpie.measurements.response === trial.correct_response">
              Correct! {{emoji(127881)}} 
              <!-- 128076 -  okay symbol  -->
              The comment should be approved because<b>{{trial.explanation}}</b>.
              <button @click="$magpie.saveAndNextScreen()">Ok</button>
            </p>
            <!-- If the answer was incorrect provide an explanation why -->
            <p v-else-if="trial.correct_response === 'Approve' && $magpie.measurements.response !== trial.correct_response">
              Incorrect... {{emoji(128579)}}
              The comment should be approved because <b>{{trial.explanation}}</b>.
              <button @click="$magpie.saveAndNextScreen()">Ok</button>
            </p>
          </div>
        </template> 
        <!-- Skip feedback during experiment phase. Couldn't find a better way to do this -->
        <template v-else #feedback> 
          <Wait :time="1" @done= "$magpie.saveAndNextScreen()" />
        </template>
    </ForcedChoiceScreen>
</template>
<script>


export default {
  name: 'TrialScreen',
  props: {
    trial: {
      type: Object,
      required: true
    },
    trialType: {
      type: String,
      required: true
    },
    trialnumber: {
      type: Number,
      required: true
    },
    progress: {
      type: Number,
      required: true
    },
    options: {
      type: Array,
      required: true
    },
    text: {
      type: String,
      required: true
    },
    instructions: {
      type: String,
      required: false
    },
    task: {
      type: Boolean,
      required: true
    },
    comprehension: {
      type: Boolean,
      required: false
    },
    group: {
      type: String,
      required: true
    },
    question: {
      type: String,
      required: false
    }
  },
  methods: {
    emoji(codePoint) {
      return String.fromCodePoint(codePoint);
    }
  },
};

</script>

<style>


.stimulus {
    border: solid #6D2445 1px;
    background-color: #FAFAFA;
    height:400px; /* or whatever width you want. */
    max-height:400px; /* or whatever width you want. */
    margin-bottom:20px;
    margin-top:20px;
    
  
}

.text {
    font-family: "Open Sans", sans-serif, Helvetica, Arial;
    font-size: 18px;
    font-weight: bold;
    text-align: left;
    padding: 30px;
    color: #373434;
}


.hintstext {
    font-family: "Open Sans", sans-serif, Helvetica, Arial;
    font-size: 28px;
    line-height: 35px;
    font-weight: 300;
    text-align: left;
    padding: 20px;
    color: #373434;
}

.feedbacktext {
    font-family: "Open Sans", sans-serif, Helvetica, Arial;
    font-size: 22px;
    line-height: 35px;
    font-weight: 300;
    text-align: left;
    padding: 20px;
    color: #373434;
}
.comprehension {
  height:400px; /* or whatever width you want. */
  max-height:350px; /* or whatever width you want. */
  margin-bottom:20px;
  margin-top:20px;
  justify-content: center;
  align-items: center;
  background: #fffdfd;
  box-shadow: 1px 1px 10px #6D2445;

  font-family: "Open Sans", sans-serif, Helvetica, Arial;
  font-size: 32px;
  font-weight: 300;
  text-align: center;
  vertical-align: middle;
  padding: 30px;
  color: #373434;
  
}

.score {
    font-family: "Open Sans", sans-serif, Helvetica, Arial;
    font-size: 18px;
    font-style: normal;
    font-weight: 500;
    color: #555454;
    text-align: left;
    padding: 30px;
}
.feedback {
   font-family: "Open Sans", sans-serif, Helvetica, Arial;
    font-size: 20px;
    font-weight: 100;
    text-align: center;
    padding: 30px;
    color: #373434;
    height:350px; /* or whatever width you want. */
    max-height:350px; /* or whatever width you want. */
    margin-bottom:20px;
    margin-top:20px;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 250px;
}

/* to place text and score in two columns */
#block1 {
    float:left;
}

#block2
{
    float:right;
    margin: 10px 20px;
    padding: 20px;
    width: 180px;
}
</style>

