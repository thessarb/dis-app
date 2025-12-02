<template>
  <div class="diabetes card">
    <h3>Heart Attack Risk Assessment</h3>
    <p style="color:var(--muted);margin-bottom:1rem">A quick, non-medical questionnaire based on common risk factors. Disclaimer: This is for educational use only and is NOT a substitute for professional medical advice.</p>

    <section class="field">
      <label>1. What is your age? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.age" value="under45" /> Under 45 years — 0 points</label>
        <label><input type="radio" v-model="form.age" value="45-54" /> 45 - 54 years — 3 points</label>
        <label><input type="radio" v-model="form.age" value="55-64" /> 55 - 64 years — 6 points</label>
        <label><input type="radio" v-model="form.age" value="65plus" /> 65 years or older — 10 points</label>
      </div>
    </section>

    <section class="field">
      <label>2. Do you currently smoke cigarettes or have you quit in the last 12 months? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.smoke" value="no" /> Never smoked / Quit more than 12 months ago — 0 points</label>
        <label><input type="radio" v-model="form.smoke" value="yes" /> Yes, I currently smoke or quit less than 12 months ago — 5 points</label>
      </div>
    </section>

    <section class="field">
      <label>3. Has a parent or sibling had a heart attack before age 60? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.family" value="no" /> No — 0 points</label>
        <label><input type="radio" v-model="form.family" value="yes" /> Yes — 4 points</label>
      </div>
    </section>

    <section class="field">
      <label>4. Are you currently taking medication for high blood pressure? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.bp" value="no" /> No — 0 points</label>
        <label><input type="radio" v-model="form.bp" value="yes" /> Yes — 3 points</label>
      </div>
    </section>

    <section class="field">
      <label>5. Have you ever been told you have high cholesterol, or are you taking statins? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.chol" value="no" /> No — 0 points</label>
        <label><input type="radio" v-model="form.chol" value="yes" /> Yes — 3 points</label>
      </div>
    </section>

    <section class="field">
      <label>6. Do you engage in moderate or vigorous physical activity for at least 30 minutes, 3+ times/week? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.activity" value="yes" /> Yes, regularly active — 0 points</label>
        <label><input type="radio" v-model="form.activity" value="no" /> No, little to no regular activity — 2 points</label>
      </div>
    </section>

    <section class="field">
      <label>7. Have you ever been diagnosed with Type 1 or Type 2 Diabetes? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.diabetes" value="no" /> No — 0 points</label>
        <label><input type="radio" v-model="form.diabetes" value="yes" /> Yes — 5 points</label>
      </div>
    </section>

    <section class="field">
      <label>8. Calculate your BMI for scoring — enter height and weight (metric). (required)</label>
      <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
        <input type="number" v-model.number="form.weight" placeholder="Weight (kg)" style="width:140px;padding:0.5rem;border-radius:8px;border:1px solid rgba(0,0,0,0.08)" />
        <input type="number" v-model.number="form.height" placeholder="Height (cm)" style="width:140px;padding:0.5rem;border-radius:8px;border:1px solid rgba(0,0,0,0.08)" />
        <div style="font-size:0.95rem;color:var(--muted);">Your BMI: <strong>{{ bmiDisplay }}</strong> — BMI Points: <strong>{{ bmiPoints }}</strong></div>
      </div>
    </section>

    <section class="field">
      <label>9. Do you eat at least five servings of fruits and vegetables daily? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.diet" value="yes" /> Yes, 5 or more servings — 0 points</label>
        <label><input type="radio" v-model="form.diet" value="no" /> No, less than 5 servings — 2 points</label>
      </div>
    </section>

    <section v-if="allFilled" class="result card">
      <h4>Your Heart Attack Risk Score</h4>
      <div class="result-content">
        <div style="display:flex;align-items:center;gap:12px;margin-bottom:0.75rem">
          <div class="total-score" :style="{ background: currentRiskColor, color: '#fff' }">{{ result.total }}</div>
          <div style="font-weight:700">Total points</div>
        </div>

        <div>
          <div style="font-weight:700;margin-bottom:0.5rem">You scored {{ result.total }} points</div>
          <div style="margin-bottom:0.75rem">{{ result.riskMessage }}</div>

          <div style="margin-top:0.75rem">
            <div class="note-box">
              <div class="note-list">
                <div v-for="note in riskNotes" :key="note.key" :class="['note-item', { active: note.key === currentRiskKey }]">
                  <div class="note-badge" :style="{ background: note.color }"></div>
                  <div>
                    <div style="font-weight:700">{{ note.label }}</div>
                    <div style="color:var(--muted);">{{ note.text }}</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { reactive, ref, computed, onMounted, watch } from 'vue'

const STORAGE_KEY = 'heartForm'

const defaultForm = {
  age: '',
  smoke: '',
  family: '',
  bp: '',
  chol: '',
  activity: '',
  diabetes: '',
  weight: null,
  height: null,
  diet: '',
}

const form = reactive({ ...defaultForm })
const result = ref({ total: 0, riskMessage: '' })

function loadForm() {
  try { const raw = localStorage.getItem(STORAGE_KEY); if (raw) Object.assign(form, JSON.parse(raw)) } catch (e) {}
}

function saveForm() { try { localStorage.setItem(STORAGE_KEY, JSON.stringify(form)) } catch (e) {} }

const bmi = computed(() => {
  const w = Number(form.weight) || 0
  const h = Number(form.height) || 0
  if (!w || !h) return 0
  const m = h / 100
  return +(w / (m * m)).toFixed(1)
})

const bmiDisplay = computed(() => bmi.value > 0 ? bmi.value : '--')

// Assumed BMI scoring (inferred): <25 -> 0, 25-29.9 -> 2, 30-34.9 -> 4, 35+ -> 6
const bmiPoints = computed(() => {
  const v = bmi.value
  if (v === 0) return 0
  if (v < 25) return 0
  if (v < 30) return 2
  if (v < 35) return 4
  return 6
})

function computeResult() {
  let total = 0
  total += ({ 'under45':0, '45-54':3, '55-64':6, '65plus':10 })[form.age] || 0
  total += ({ 'no':0, 'yes':5 })[form.smoke] || 0
  total += ({ 'no':0, 'yes':4 })[form.family] || 0
  total += ({ 'no':0, 'yes':3 })[form.bp] || 0
  total += ({ 'no':0, 'yes':3 })[form.chol] || 0
  total += ({ 'yes':0, 'no':2 })[form.activity] || 0
  total += ({ 'no':0, 'yes':5 })[form.diabetes] || 0
  total += bmiPoints.value
  total += ({ 'yes':0, 'no':2 })[form.diet] || 0

  // categorize
  let riskMessage = ''
  if (total <= 5) riskMessage = 'Very Low Risk'
  else if (total <= 10) riskMessage = 'Low Risk'
  else if (total <= 15) riskMessage = 'Moderate Risk'
  else riskMessage = 'High Risk'

  result.value = { total, riskMessage }
}

const riskNotes = computed(() => [
  { key: 'verylow', label: 'Very Low (0 - 5)', text: 'Very low short-term risk.', color: '#2ecc71' },
  { key: 'low', label: 'Low (6 - 10)', text: 'Low risk — maintain healthy habits.', color: '#f1c40f' },
  { key: 'moderate', label: 'Moderate (11 - 15)', text: 'Moderate risk — consider lifestyle changes.', color: '#f39c12' },
  { key: 'high', label: 'High (16+)', text: 'High risk — consult a healthcare provider.', color: 'linear-gradient(90deg,#ff4d4d,#dc143c)' },
])

const allFilled = computed(() => {
  return form.age !== '' && form.smoke !== '' && form.family !== '' && form.bp !== '' && form.chol !== '' && form.activity !== '' && form.diabetes !== '' && form.weight && form.height && form.diet !== ''
})

const currentRiskKey = computed(() => {
  const t = result.value.total || 0
  if (t <= 5) return 'verylow'
  if (t <= 10) return 'low'
  if (t <= 15) return 'moderate'
  return 'high'
})

const currentRiskColor = computed(() => {
  const k = currentRiskKey.value
  if (k === 'verylow') return '#2ecc71'
  if (k === 'low') return '#f1c40f'
  if (k === 'moderate') return '#f39c12'
  return 'linear-gradient(90deg,#ff4d4d,#dc143c)'
})

watch(form, () => { saveForm(); computeResult() }, { deep: true })
onMounted(() => { loadForm(); computeResult() })
</script>
