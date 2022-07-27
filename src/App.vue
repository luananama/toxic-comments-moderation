<template>
  <Experiment title="Moderation of online comments">

  <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Instructions  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

    <!-- sensitive content warning -->
    <InstructionScreen :title="'Warning'">
      <div class="instructionstext">
        The present experiment contains words that can be perceived as offensive or triggering by some. Although the amount of such words has been kept to a minimum necessary for the task, please only continue participating if you are not upset by profanity, insults or controversial statements.
      </div>
    </InstructionScreen>

    <!-- describe the task -->
    <InstructionScreen :title="'Instructions'">
        <div class="instructionstext">
          <p>
            It's easy to be mean on the internet. Today you will take the role of a <b>moderator</b> and try to keep online conversations civilized, nice and with no mean comments.
          </p>

          <p> 
            Your task is to read comments from online conversations on platforms like twitter, reddit, or wikipedia talk, and decide if they should be published online (<b>APPROVE</b>), or deleted (<b>REJECT</b>) 
          </p>  
          <p> 
            To help you, an AI will <i>sometimes</i> show you how likely it is that a comment is <b>toxic</b>. Use this score, but don't rely too much on it, as it doesn't always work as it should.
          </p>
          <p> 
            One more thing: every now and then you will be asked to <b>answer a question</b> (<i>"{{comprehension_question}}"</i>) about the comment you just read, so always pay attention to the text! 
          </p>
        </div>
    </InstructionScreen>

 
 <!-- You will also randomly receive a comprehension question refering to the content of the comment you just read, which you must answer without looking at the text again. -->
        
    <InstructionScreen :title="'Instructions'">
    <div contenteditable="true" class="instructionstext">
      <p>
        Here are some examples of comments that are toxic and we want to reject:
        <ul>
          <li>a hate comment</li>
          <li>a comment that attacks a person, group or minority</li>
          <li>a comment that dismisses someone's personal experience, especially in an insulting way, or if the person is part of a minority group</li>
          <li>a nonsensical comment that adds nothing productive to the conversation</li>
          <li>a sexist, racist comment, or one that contains harmful stereotypes about a minority group</li>
        </ul>
      </p>
    
      Note: A comment can contain <b>profanity</b>, and not be considered toxic. 
      
      <p>
        Let's continue to a short training phase, to properly understand the task!
      </p>
    </div>
    </InstructionScreen>

    
    <InstructionScreen :title="'Practice'">
      <div class="instructionstext">
        <p>
			  Now you will read a series of practice questions. Read the instructions and the feedback carefully, in order to understand the task. 
        <br>
        When you are ready, click the button to <b>start the practice session</b>!
			  </p>
      </div>
		</InstructionScreen>
    

    <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Training  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

    <!-- Practice trials -->
    <template v-for="(trial, i) of practice_trials">
      <!-- Provide the plain text for the participant to read before the task -->

       <TrialScreen
        :trial="trial"
        :key="'training-' + i"
        :trialType="'training'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['I have read the text']"
        :text="trial.text"
        :instructions="'Read the text below.'"
        :task="false"
      />
      
      <!-- Comprehension question is provided with a higher probability than in the experiment phase -->
      <TrialScreen v-if="Math.random() < 0.8"
        :trial="trial"
        :key="'training-' + i"
        :trialType="'training'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['Yes', 'No']"
        :text=comprehension_question
        :instructions="'Answer the question below based on the text you previously read.'"
        :task="false"
        :comprehension="true"
      />

      <!-- The task, where the participant can make a decision  -->
      <TrialScreen
        :trial="trial"
        :key="'training-' + i"
        :trialType="'training'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['Approve', 'Reject']"
        :text="trial.text"
        :instructions="'Notice the TOXICITY SCORE. Approve or reject the comment.'"
        :task="true"
      />
    </template>


    <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Experiment  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

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
     <TrialScreen
        :trial="trial"
        :key="'experiment-' + i"
        :trial-type="'experiment'"
        :trialnumber="i"
        :progress="i/main_trials.length"
        :options="['I have read the text']"
        :text="trial.text"
        :task="false"
      />

      
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
      />

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
      />
    </template>

    <PostTestScreen />

    <DebugResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import practice from '../trials/training.csv';
import main from '../trials/main.csv';
import TrialScreen from './TrialScreen.vue';
import _ from 'lodash';

export default {
  name: 'App',
  components: {TrialScreen},
  data() {
    // randomly sample n items for the practice phase
    const practice_trials = _.sampleSize(_.shuffle(practice), 1);
    const main_trials = _.sampleSize(_.shuffle(main), 6);

    const comprehension_question = "Does the comment reference a group that is considered vulnerable, disadvantaged, or often discriminated against?";
    // 
    return {
      practice_trials,
      main_trials,
      comprehension_question,
      // Expose lodash.range to template above
      range: _.range
      
    };
  },
};

</script>
<style>

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