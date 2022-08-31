<template>
  <Experiment title="Moderation of online comments">

  <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Instructions  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

    <!--------------------------------------------------- sensitive content warning ---------------------------------------------------->
    <InstructionScreen>
      <div class="instructionstext">
        <img src="../public/images/warning.png" alt="warning" class="center"  width="100" />
        <br>  
        The following experiment contains text that can be upsetting or triggering to some people. While we tried to keep these texts to a minimum necessary, please only continue participating if you are not affected by profanity, insults or controversial comments.
      </div>
    </InstructionScreen>

    <!----------------------------------------------------- description of task -- ---------------------------------------------------->
    <InstructionScreen :title="'Instructions'">
        <div class="instructionstext">
          <p>
            It's easy to be mean on the internet. Today you will take the role of a <b>moderator</b> and try to keep online conversations civilized, nice, and with no mean comments.
          </p>

          <p> 
            Your task is to read real comments from online conversations on platforms like twitter, reddit, or wikipedia talk, and decide if they should be published online (<b>APPROVE</b>), or deleted (<b>REJECT</b>).
          </p>  
          <p v-if="group=='score'"> 
            To help you, an AI assistant will show you how likely it is that a comment is <b>toxic</b>. You can use this score to help you make a decision, but don't rely too much on it.
          </p>
          <p> 
            One more thing: every now and then you will be asked to <b>answer a question</b> (<i>"{{comprehension_question}}"</i>) about the comment you just read, so always pay attention to the text! 
          </p>
        </div>
    </InstructionScreen>
         
    <InstructionScreen :title="'Instructions'">
    <div contenteditable="true" class="instructionstext">
      <p>
        Here are some categories of comments that are <b>toxic</b> and we want to reject:
        <ul>
          <li>mean, hateful comments</li>
          <li>sexist, racist, ableist comments</li>
          <li>comments that contain harmful stereotypes</li>
          <li>comments that attack a person or a minority group</li>
          <li>comments that threaten someone</li>
          <li>comments that want to hurt someone by mocking how they feel or what they experienced.</li>
          <li>comments that don't make sense, or that is just trying to provoke a reaction (trolling) </li>
        </ul>
      </p>
    
      Note: A comment can contain <b>bad words</b> (profanity), and not be considered toxic. 
      
      <p>
        Let's continue to a short training phase, to properly understand what you have to do!
      </p>
    </div>
    </InstructionScreen>

    
    <InstructionScreen :title="'Practice'">
      <div class="instructionstext">
        <p>
			  Now you will read a series of practice questions. Read the instructions and the feedback carefully, in order to understand the task. 
        <br>
        When you are ready, click the "NEXT" button to <b>start the practice session</b>!
			  </p>
      </div>
		</InstructionScreen>
    

    <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Training  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

    <!-- Practice trials -->
    <template v-for="(trial, i) of practice_trials">
      <!-- Provide the plain text for the participant to read before the task -->

       <!-- <TrialScreen
        :trial="trial"
        :key="'training-' + i"
        :trialType="'training'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['I have read the text']"
        :text="trial.text"
        :instructions="'Read the text below.'"
        :task="false"
        :group=group
      /> -->

      <!-- The task, where the participant can make a decision  -->
      <TrialScreen v-if="group==='score'"
        :trial="trial"
        :key="'training-' + i"
        :trialType="'training'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['Approve', 'Reject']"
        :text="trial.text"
        :instructions="'Notice the TOXICITY SCORE. Reject the comment if you think it is toxic, approve it otherwise.'"
        :question="' '"
        :task="true"
        :group=group
      />

      <TrialScreen v-else
        :trial="trial"
        :key="'training-' + i"
        :trialType="'training'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['Approve', 'Reject']"
        :text="trial.text"
        :instructions="'Reject the comment if you think it is toxic, approve it otherwise.'"
        :question="'Moderate the comment:'"
        :task="true"
        :group=group
      />
      
          
      <!--------------------------------------------------- comprehension question  ---------------------------------------------------->

      <!-- comprehension question is provided with a higher probability than in the experiment phase -->
      <TrialScreen v-if="Math.random() < 0.8"
        :trial="trial"
        :key="'training-' + i"
        :trialType="'training'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['Yes', 'No']"
        :text=comprehension_question
        :instructions="'Answer the question below based on the comment you read before.'"
        :task="false"
        :comprehension="true"
        :group=group
      />
    </template>
      

    <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Experiment  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

    <!-- The experiment phase  -->
    <Screen :title="'Experiment'">
			Practice round is over! The next questions will provide no feedback. Read each one carefully and make a decision according to what you think is correct.
			<br>
			<br>
			When you are ready, <b>click the button to start the experiment</b>.
			<button @click="$magpie.nextScreen();">
				Start
			</button>
		</Screen>

  <!-- Trials -->
    <template v-for="(trial, i) of main_trials" >
      <!-- Provide the plain text for the participant to read before the task -->     
     <!-- <TrialScreen
        :trial="trial"
        :key="'experiment-' + i"
        :trial-type="'experiment'"
        :trialnumber="i"
        :progress="i/main_trials.length"
        :options="['I have read the text']"
        :text="trial.text"
        :task="false"
        :group=group
      /> -->

      
      
      <!-- The task, where the participant can make a decision  -->
      <TrialScreen
        :trial="trial"
        :key="'experiment-' + i"
        :trial-type="'experiment'"
        :trialnumber="i"
        :progress="i/main_trials.length"
        :options="['Approve', 'Reject']"
        :text="trial.text"
        :task="true"
        :question="emoji(128718)"
        :group=group
      />

      <TrialScreen v-if="group==='score'"
        :trial="trial"
        :key="'experiment-' + i"
        :trial-type="'experiment'"
        :trialnumber="i"
        :progress="i/main_trials.length"
        :options="['Yes', 'No']"
        :question="'Do you agree with the score?'"
        :text="trial.text"
        :task="true"
        :group=group
      />

    </template>
    

<!-- Comprehension question is provided with a higher probability than in the experiment phase -->
      <TrialScreen v-if="Math.random() < 0.2"
        :trial="trial"
        :key="'experiment-' + i"
        :trialType="'experiment'"
        :trialnumber="i"
        :progress="i / main_trials.length"
        :options="['Yes', 'No']"
        :text=comprehension_question
        :task="false"
        :comprehension="true"
        :group=group
      />

    <PostTestScreen />

    <DebugResultsScreen />
    <!-- <SubmitResultsScreen /> -->
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import practice from '../trials/training.csv';
import main from '../trials/main_new.csv';
import TrialScreen from './TrialScreen.vue';
import _ from 'lodash';
const practice_trials = _.sampleSize(_.shuffle(practice), 1);
const main_trials = _.sampleSize(_.shuffle(main), 10);
// whether the participant will be shown the toxicity score or not
const group = _.sample(['score', 'no_score']);
const comprehension_question = "Does the comment reference a group that is considered vulnerable, disadvantaged, or often discriminated against?";

export default {
  name: 'App',
  components: {TrialScreen},
  methods: {
    emoji(codePoint) {
      return String.fromCodePoint(codePoint);
    }
  },
  data() {
    // randomly sample n items for the practice phase
    
    return {
      practice_trials,
      main_trials,
      group,
      comprehension_question,
      // Expose lodash.range to template above
      range: _.range
      
    };
  },
};

</script>
<style>

.center {
  display: block;
  margin-left: auto;
  margin-right: auto;
}

.instructionstext {
    font-family: "Open Sans", sans-serif, Helvetica, Arial;
    font-size: 18px;
    line-height: 30px;
    font-weight: 300;
    text-align: left;
    padding: 20px;
    color: #373434;
}

/* @font-face {
  font-family: "OpenSans";
  src: local("OpenSans"),
   url(./fonts/OpenSans-Regular.ttf) format("truetype");
}  */

@import url(https://fonts.googleapis.com/css2?family=Open+Sans);

</style>