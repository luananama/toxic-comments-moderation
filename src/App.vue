<template>
  <Experiment title="Moderation of online comments">

  <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Instructions  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

    <!--------------------------------------------------- sensitive content warning ---------------------------------------------------->
    <InstructionScreen>
      <div class="instructionstext">
        <img src="../public/images/warning.png" alt="warning" class="center"  width="100" />
        <p>  
        The following experiment contains text that can be upsetting or triggering to some people. While we tried to keep these texts to a minimum necessary, please only continue participating if you are not affected by profanity, insults or controversial comments.
        </p>
        <p>
        <i>The experiment duration is between 10 and 15 minutes.</i>
        </p>
        <!-- <button @click="$magpie.nextScreen('experiment')">jump to experiment</button> -->
      </div>
    </InstructionScreen>

    <!----------------------------------------------------- description of task -- ---------------------------------------------------->
    <InstructionScreen :title="'Instructions'">
        <div class="instructionstext">
          <p>
            It's easy to be mean on the internet. Today you will take the role of a <b>moderator</b> and try to keep online conversations civilized, nice, and with no mean comments! {{emoji(129303)}}
          </p>

          <p> 
            Your task is to read <b>real comments</b> from online conversations on platforms like twitter, reddit, or wikipedia talk, and decide if they should be published online (<b>APPROVE</b>), or deleted (<b>REJECT</b>).
          </p>  
          <p v-if="group=='score'"> 
            {{emoji(128187 )}} To help you, an AI assistant will show you how likely it is that a comment is <b>toxic</b>. You can use this score to help you make a decision, but don't rely too much on it. 
            <br>
            An additional question will ask whether you think the AI got close to predicting the correct answer (based on the likelihood score). 
          </p> 
        </div>
    </InstructionScreen>

    <InstructionScreen :title="'Instructions'">
        <div class="instructionstext">
          <p> 
            {{emoji(9995)}} Every now and then you will be asked to <b>answer a question</b> about the comment you just read, so always pay attention to the text! This is the question:
          </p>
          <p>
            <i>"{{comprehension_question}}"</i>
          </p>
          <p>
            For example, in a comment making fun of a blind person, the implied group is people with disabilities, and the answer is "<i>yes</i>".
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
          <li>comments that don't make sense, or are just trying to provoke a reaction (trolling) </li>
        </ul>
      </p>
      

      {{emoji(128173)}} Note: A comment can contain <b>bad words</b> (profanity), and not be considered toxic. 
      
      <p>
        Let's continue to a short training phase, to properly understand what you have to do!
      </p>
    </div>
    </InstructionScreen>

    
    <InstructionScreen :title="'Practice'">
      <div class="instructionstext">
        <p>
			  Now you will read a few practice questions. Read the instructions and the feedback carefully, in order to understand the task. 
        <br>
        When you are ready, click the "NEXT" button to <b>start the practice session</b>!
			  </p>
      </div>
		</InstructionScreen>
    

    <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Training  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->

    <!-- Practice trials -->
    <template v-for="(trial, i) of practice_trials">
      <!-- The task, where the participant can make a decision  -->
      <TrialScreen v-if="group==='score'"
        :trial="trial"
        :key="'training-score-' + i"
        :trialType="'training-task'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['Approve', 'Reject']"
        :text="trial.text"
        :instructions="'Notice the TOXICITY SCORE. Reject the comment if you think it is toxic, approve it otherwise.'"
        :question="emoji(128718)"
        :task="true"
        :group=group
      />

      <TrialScreen v-else
        :trial="trial"
        :key="'training-no-score-' + i"
        :trialType="'training-task'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="['Approve', 'Reject']"
        :text="trial.text"
        :instructions="'Reject the comment if you think it is toxic, approve it otherwise.'"
        :question="emoji(128718)"
        :task="true"
        :group=group
      />
      
          
      <!--------------------------------------------------- comprehension question  ---------------------------------------------------->

      <!-- comprehension question is provided with a higher probability during the practice phase -->
      <TrialScreen v-if="Math.random() < 0.8"
        :trial="trial"
        :key="'training-comprehension-' + i"
        :trialType="'training-comprehension'"
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
    <Screen :title="'Experiment'" label="experiment">
			Practice round is over! In the next phase you will get no feedback. Read each comment carefully and make a decision according to what you think is correct.
			<br>
			<br>
			When you are ready, <b>click the button to start the experiment</b>.
			<button @click="$magpie.nextScreen();">
				Start
			</button>
		</Screen>

  <!-- Trials -->
    <template v-for="(trial, i) of main_trials" >
      <!-- The task, where the participant can make a decision  -->
      <TrialScreen
        :trial="trial"
        :key="'experiment-' + i"
        :trial-type="'experiment-task'"
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
        :key="'experiment-manipulation-check-' + i"
        :trial-type="'experiment-agree'"
        :trialnumber="i"
        :progress="i/main_trials.length"
        :options="['Yes', 'No']"
        :question="'Do you think the AI prediction was close?'"
        :text="trial.text"
        :task="true"
        :group=group
      />

  <!-- Comprehension question is provided with a lower probability in the experiment phase -->
  <TrialScreen v-if="Math.random() < 0.2"
        :trial="trial"
        :key="'experiment-comprehension-' + i"
        :trialType="'experiment-comprehension'"
        :trialnumber="i"
        :progress="i / main_trials.length"
        :options="['Yes', 'No']"
        :text=comprehension_question
        :task="false"
        :comprehension="true"
        :group=group
      />
    </template>

    <InstructionScreen :title="'Almost done!'">
      <div class="instructionstext">
        <p>
			  <b>Please stay with us just a bit longer! {{emoji(9749)}}</b>
        <br>
        On the next screen you can fill out some personal information. If you study at the Osnabrueck University, you can earn 1 VP for your participation. 
        In order to claim that, please submit your <b>Matrikelnummer</b> and <b>full name</b>.  
        <br>
			  </p>
      </div>
		</InstructionScreen>
      
  <!-- <PostTestScreen/> -->
    <PostTestScreen :education="false" :age="false" :gender="false" :comments="false" :languages="false">
      <template #default>
        <label>Full name (only for claiming VP hours)<input type="text" v-model=$magpie.measurements.name></label>
        <label>Matrikelnummer (only for claiming VP hours)<input type="text" v-model=$magpie.measurements.matrikelnr></label>
        <label>Native languages<input type="text" v-model=$magpie.measurements.languages></label>
        <label>Age <br><input v-model="$magpie.measurements.age" type="number" max="110" min="18"/></label>
        <br>
        <label>Do you belong to any identity groups? 
            <DropdownInput :options="[
                      '',
                      'I do not belong to any identity groups.',
                      'LGBTQ',
                      'People with disabilities',
                      'Racial/Ethnic minority',
                      'Religious minority',
                      'Neurodivergent people',
                      'Gender minority',
                      'Other'
                      ]"
                    :response.sync="$magpie.measurements.identity"/>
        </label>
        <label>Do you regularly participate in activist events or keep up with activist content?
            <DropdownInput :options="[
                      '',
                      'Yes',
                      'No'
                      ]"
                    :response.sync="$magpie.measurements.activism"/>
        </label>
      </template>
    </PostTestScreen>
 
    <!-- <DebugResultsScreen /> -->
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import practice from '../trials/training.csv';
import main from '../trials/main_new.csv';
import TrialScreen from './TrialScreen.vue';
import _ from 'lodash';

const practice_trials = _.sampleSize(_.shuffle(practice), 3); 
const main_trials = _.sampleSize(_.shuffle(main), 40);

// whether the participant will be shown the toxicity score or not
const group = _.sample(['score', 'no_score']);
const comprehension_question = "Does the comment mention any of the following (derogative terms not included): women, LGBTQ people, a specific race, ethnicity or religion, people with disabilities, neurodivergent people?";

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