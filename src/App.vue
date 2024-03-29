<template>
  <Experiment title="Moderation of online comments">

  <!-- *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*  Instructions  *~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~*~~~* -->
  <!-- 
  <Screen>
      <Slide>
        <div class="instructionstext">
          <p>  
          This experiment has finished.
          </p>
        </div>
      </Slide>
    </Screen>
   -->
    <!--------------------------------------------------- sensitive content warning ---------------------------------------------------->
    <Screen>
      <Slide>
        <div class="instructionstext">
          <img src="../public/images/warning.png" alt="warning" class="center"  width="100" />
          <p>  
            In this experiment you will be asked to moderate real online comments, some of which can be <b>upsetting or triggering</b> to some people. 
            Please only continue participating if you are not affected by profanity, insults or controversial comments.
          </p>
          <p>
            The personal information required at the end of the experiment is completely <b>voluntary</b>, will remain <b>confidential</b> and will not be associated with your name.  
          </p>
          <p>
          <i>The experiment duration is between 10 and 15 minutes. </i>
          </p>
          <p>
            <b>
            By clicking the button below, you consent to the terms above and agree to participate in the experiment. 
            </b>
          </p>
          <button @click="$magpie.nextScreen()">I consent</button>
        </div>
      </Slide>
    </Screen>
    
    <!----------------------------------------------------- description of task -- ---------------------------------------------------->
    <InstructionScreen :title="'Instructions'">
        <div class="instructionstext">
          <p>
            It's easy to be mean on the internet. Today you will take the role of a <b>moderator</b> and try to keep online conversations civilized, nice, and with no mean comments! {{emoji(129303)}}
          </p>

          <p> 
            Your task is to read <b>real comments</b> from online conversations on platforms like twitter, reddit, or wikipedia talk, and decide if they should be published online ({{emoji(9989)}} <b>APPROVE</b>), or deleted ({{ emoji(10060) }} <b>REJECT</b>).
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
    <!-- "['Approve', 'Reject']" -->
    <!-- Practice trials -->
    <template v-for="(trial, i) of practice_trials">
      <!-- The task, where the participant can make a decision  -->
      <TrialScreen v-if="group==='score'"
        :trial="trial"
        :key="'training-score-' + i"
        :trialType="'training-task'"
        :trialnumber="i"
        :progress="i / practice_trials.length"
        :options="[emoji(9989)+' Approve', emoji(10060) + ' Reject']"
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
        :options="[emoji(9989)+' Approve', emoji(10060) + ' Reject']"
        :text="trial.text"
        :instructions="'Reject the comment if you think it is toxic, approve it otherwise.'"
        :question="emoji(128718)"
        :task="true"
        :group=group
      />
      
      <TrialScreen v-if="group==='score'"
        :trial="trial"
        :key="'training-manipulation-check-' + i"
        :trial-type="'training-agree'"
        :trialnumber="i"
        :progress="i/practice_trials.length"
        :options="['Yes', 'No']"
        :instructions="'Answer the question provided about the AI prediction score.'"
        :question="'Do you think the AI prediction was close?'"
        :text="trial.text"
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
        :instructions="'Answer the question below based on the comment you read before. This question will only show up sometimes.'"
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
        :options="[emoji(9989)+' Approve', emoji(10060) + ' Reject']"
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
        On the next screen you can fill out some personal information. If you study at the Osnabrueck University, you can earn 1/2 VP for your participation. 
        In order to claim that, please submit your <b>Matrikelnummer</b> and <b>full name</b>.  
        <br>
			  </p>
      </div>
		</InstructionScreen>
      
    <PostTestScreen :education="false" :age="false" :gender="false" :comments="false" :languages="false">
      <template #default>
        <label>Full name (only for claiming VP hours)<input type="text" v-model=$magpie.measurements.name></label>
        <label>Matrikelnummer (only for claiming VP hours)<input type="text" v-model=$magpie.measurements.matrikelnr></label>
        <label>Native language(s)<input type="text" v-model=$magpie.measurements.languages></label>
        <label>Age <br><input v-model="$magpie.measurements.age" type="number" max="110" min="18"/></label>
        <br>  
        <label>Gender
            <DropdownInput :options="[
                      '',
                      'Non-binary',
                      'Female',
                      'Male',
                      'Other',
                      ]"
                    :response.sync="$magpie.measurements.gender"/>
        </label>
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
        <label>Have you ever moderated online content, either as a volunteer or for remuneration?
            <DropdownInput :options="[
                      '',
                      'Yes',
                      'No'
                      ]"
                    :response.sync="$magpie.measurements.activism"/>
        </label>
      </template>
    </PostTestScreen>
    <SubmitResultsScreen />
  </Experiment>
</template>

<script>
// Load data from csv files as javascript arrays with objects
import practice from '../trials/training.csv';
import main from '../trials/main_new.csv';
import TrialScreen from './TrialScreen.vue';
import _ from 'lodash';

// used to select an equal number of trials from each category combination
const reject_not_toxic = main.filter(function(row) {
    return row['correct_response'] == 'Reject' && row['category'] == 'not_toxic';
});
const reject_toxic = main.filter(function(row) {
        return row['correct_response'] == 'Reject' && row['category'] == 'toxic';
    });
const approve_not_toxic = main.filter(function(row) {
    return row['correct_response'] == 'Approve' && row['category'] == 'not_toxic';
});
const approve_toxic = main.filter(function(row) {
    return row['correct_response'] == 'Approve' && row['category'] == 'toxic';
});

// whether the participant will be shown the toxicity score or not
const group = _.sample(['score', 'no_score']);
const comprehension_question = "Does the comment mention any of the following: LGBTQ people, a specific race, an ethnic or religious group?";

export default {
  name: 'App',
  components: {TrialScreen},
  methods: {
    emoji(codePoint) {
      return String.fromCodePoint(codePoint);
    },
  },

  data() {
    return {
      main_trials: _.shuffle(_.concat( _.sampleSize(reject_not_toxic, 10), _.sampleSize(reject_toxic, 10), _.sampleSize(approve_not_toxic, 10), _.sampleSize(approve_toxic, 10))),
      practice_trials: _.sampleSize(_.shuffle(practice), 3), 
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
