<template>
  <div class="question">
    <h3>What is your age group?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="age1" name="age" value="under35">
      <label for="age1">Under 35</label>
      <input type="radio" id="age2" name="age" value="35-44">
      <label for="age2">35 to 44 years old</label>
      <input type="radio" id="age3" name="age" value="45-54">
      <label for="age3">45 to 54 years old</label>
      <input type="radio" id="age4" name="age" value="55-64">
      <label for="age4">55 to 64 years old</label>
      <input type="radio" id="age5" name="age" value="65plus">
      <label for="age5">65+</label>
    </div>
  </div>

  <div class="question">
    <h3>What is your gender?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="male" name="gender" value="male">
      <label for="male">Male</label>
      <input type="radio" id="female" name="gender" value="female">
      <label for="female">Female</label>
    </div>
  </div>

  <div class="question">
    <h3>Have either of your parents, or any of your brothers or sisters been diagnosed with diabetes (type 1 or type 2)?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="historyyes" name="historywithdiabetes" value="yes">
      <label for="historyyes">Yes</label>
      <input type="radio" id="historyno" name="historywithdiabetes" value="no">
      <label for="historyno">No</label>
    </div>
  </div>

  <div class="question">
    <h3>Have you ever been found to have high blood glucose (sugar) (for example, in a health examination, during an illness, or during pregnancy)?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="glucoseyes" name="glucosehistory" value="yes">
      <label for="glucoseyes">Yes</label>
      <input type="radio" id="glucoseno" name="glucosehistory" value="no">
      <label for="glucoseno">No</label>
    </div>
  </div>

  <div class="question">
    <h3>Are you currently taking medication for high blood pressure?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="bpmedyes" name="bpmedication" value="yes">
      <label for="bpmedyes">Yes</label>
      <input type="radio" id="bpmedno" name="bpmedication" value="no">
      <label for="bpmedno">No</label>
    </div>
  </div>

  <div class="question">
    <h3>Do you currently smoke cigarettes or any other tobacco products on a daily basis?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="smokeyes" name="smoking" value="yes">
      <label for="smokeyes">Yes</label>
      <input type="radio" id="smokeno" name="smoking" value="no">
      <label for="smokeno">No</label>
    </div>
  </div>

  <div class="question">
    <h3>How often do you eat vegetables or fruit?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="vegfruityes" name="vegfruit" value="everyday">
      <label for="vegfruityes">Every day</label>
      <input type="radio" id="vegfruitno" name="vegfruit" value="noteveryday">
      <label for="vegfruitno">Not every day</label>
    </div>
  </div>

  <div class="question">
    <h3>On average, would you say you do at least 2.5 hours of physical activity per week (for example, 30 minutes a day on 5 or more days a week)?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="radio" id="activityyes" name="physicalactivity" value="yes">
      <label for="activityyes">Yes</label>
      <input type="radio" id="activityno" name="physicalactivity" value="no">
      <label for="activityno">No</label>
    </div>
  </div>

  <div class="question">
    <h3>What is your waist measurement (in cm)?</h3>
    <div style="display: flex; flex-direction: column;">
      <input type="number" id="waistMeasurement" name="waistMeasurement" placeholder="Enter waist measurement">
      <label for="waistMeasurement">Waist Measurement (cm)</label>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)
function increment() {
  count.value+=2
}
</script>
