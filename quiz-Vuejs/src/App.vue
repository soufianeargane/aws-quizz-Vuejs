<script setup>
import { ref, computed } from 'vue'

// save wrong answers in an array to show them at the end with the correct answer and explanation
const quizStart = ref(false)

const currentStep = ref(1)
var wrongAnswers = []
function startQuiz() {
  quizStart.value = true
  currentStep.value = 2
}
const questions = ref([
  {
    question: 'Why is AWS more economical than traditional data centers for applications with varying compute workloads?',
    answer: 2,
    options: [
      'Amazon EC2 costs are billed on a monthly basis', 
      'Users retain full administrative access to their Amazon EC2 instances', 
      'Amazon EC2 instances can be launched on demand when needed.', 
      'Users can permanently run enough instances to handle peak workloads'],
    explanation: 'The ability to launch instances on demand when needed allows users to launch and terminate instances in response to a varying workload. This is a more economical practice than purchasing enough on-premises servers to handle the peak load.',
    selected : null
  },
  {
    question: 'Which AWS service would simplify the migration of a database to AWS ?',
    answer: 1,
    options: ['AWS Storage Gateway', 
    'AWS Database Migration Service (AWS DMS)', 
    'Amazon EC2.', 'Amazon AppStream 2.0'],
    explanation: 'AWS DMS helps users migrate databases to AWS quickly and securely. The source database remains fully operational during the migration, minimizing downtime to applications that rely on the database. AWS DMS can migrate data to and from most widely used commercial and open-source databases',
    selected : null
  }
])
// for (let i = questions.value.length - 1; i > 0; i--) {
//   const j = Math.floor(Math.random() * (i + 1));
//   [questions.value[i], questions.value[j]] = [questions.value[j], questions.value[i]];
// }
questions.value = questions.value.sort(() => Math.random() - 0.5);

const quizCompleted = ref(false)
const currentQuestion = ref(0)
const score = computed(()=>{
  let value = 0
  questions.value.forEach((question)=>{
    if(question.answer == question.selected){
      value++
    }else{
      wrongAnswers.push(question)
      console.log(wrongAnswers)
    }
  })
  return value
})

const getCurrentQuestion = computed(()=>{
  let question = questions.value[currentQuestion.value]
  question.index = currentQuestion.value
  return question
})

const completedPercentage = computed(() => {
  return (currentQuestion.value + 1) / questions.value.length * 100
})

const SetAnswer = e=>{
  questions.value[currentQuestion.value].selected = e.target.value
  e.target.value = null
}

const NextQuestion = ()=>{
  if(currentQuestion.value < questions.value.length - 1){
    currentQuestion.value++
  }else{
    quizCompleted.value = true
    currentStep.value = 3
  }
}

</script>
<template>

    <main class="app">
      welcome to the quiz
      <div class="stepper-header">
        <div class="step">
          <div :class="{ active: currentStep === 1}">1</div>
          <p>instructions</p>
        </div>
        <div class="step">
          <div :class="{ active: currentStep === 2}" >2</div>
          <p>quiz</p>
        </div>
        <div class="step">
          <div :class="{ active: currentStep === 3}">3</div>
          <p>result</p>
        </div>
      </div>
      <div v-if="! quizStart">
        <button @click="startQuiz">Start Quiz</button>
      </div>
      <div v-if="quizStart">

      <div v-if="! quizCompleted" class="quizz">
        <div class="quizz-area">
          <div class="question">
            <div class="progress-bar">
            <progress :value="completedPercentage" max="100"></progress>
            <span>{{ currentQuestion + 1 }}/{{ questions.length }}</span>
          </div>
            <h2>{{getCurrentQuestion.question}}</h2> 
            <div class="options">
              <div class="option" v-for="(option, index) in getCurrentQuestion.options" :key="index">
                <input type="radio" :value="index" :name="getCurrentQuestion.index" @change="SetAnswer" 
                v-model="getCurrentQuestion.selected"
                />
                <label>{{option}}</label>
              </div>
            </div>
          </div>
          <button
          @click="NextQuestion"
          :disabled="! getCurrentQuestion.selected">
            Next
          </button>
        </div>
      </div>
      <div v-if="quizCompleted">
        <h1>Quiz Completed</h1>
        <h2>Score: {{score}}/{{questions.length}}</h2>
        <div
        v-if="wrongAnswers.length > 0">

        <h2>Wrong Answers</h2>
        <ul>
          <li v-for="(question, index) in wrongAnswers" :key="index">
            <p>{{question.question}}</p>
            <p>Correct Answer: {{question.options[question.answer]}}</p>
            <p>Explanation: {{question.explanation}}</p>
          </li>
        </ul>
      </div>
      </div>
    </div>
    </main>


</template>
<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Montserrat', sans-serif;
  }
  body {
    background-color: #231733;
    color: white;
  }

  .progress-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin: 1rem 0;
}

progress {
  width: 100%;
  height: 1.5rem;
}

progress::-webkit-progress-bar {
  background-color: #eee;
}

progress::-webkit-progress-value {
  background-color: #4caf50;
}

.stepper-header{
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 1rem auto;
  width: 300px;
}
.step div{
  width: 40px;
  height: 40px;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #48572e;
}

.step div.active{
  background-color: #374dce;
}


</style>