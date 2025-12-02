<template>
  <div class="diabetes card">
    <h3>Hypertension Risk Assessment</h3>
    <p style="color:var(--muted);margin-bottom:1rem">Evaluate your risk factors for developing High Blood Pressure (Hypertension). Disclaimer: educational use only.</p>

    <section class="field">
      <label>1. What is your age? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.age" value="under40" /> Under 40 years — 0 points</label>
        <label><input type="radio" v-model="form.age" value="40-55" /> 40 - 55 years — 3 points</label>
        <label><input type="radio" v-model="form.age" value="56-69" /> 56 - 69 years — 6 points</label>
        <label><input type="radio" v-model="form.age" value="70plus" /> 70 years or older — 10 points</label>
      </div>
    </section>

    <section class="field">
      <label>2. Do your parents or siblings have a history of high blood pressure? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.family" value="none" /> No known family history — 0 points</label>
        <label><input type="radio" v-model="form.family" value="one" /> One first-degree relative — 4 points</label>
        <label><input type="radio" v-model="form.family" value="multiple" /> Both parents or multiple relatives — 7 points</label>
      </div>
    </section>

    <section class="field">
      <label>3. Calculate your BMI — enter weight and height (metric). (required)</label>
      <div style="display:flex;gap:8px;align-items:center;flex-wrap:wrap">
        <input type="number" v-model.number="form.weight" placeholder="Weight (kg)" style="width:140px;padding:0.5rem;border-radius:8px;border:1px solid rgba(0,0,0,0.08)" />
        <input type="number" v-model.number="form.height" placeholder="Height (cm)" style="width:140px;padding:0.5rem;border-radius:8px;border:1px solid rgba(0,0,0,0.08)" />
        <div style="font-size:0.95rem;color:var(--muted);">Your BMI: <strong>{{ bmiDisplay }}</strong> — BMI Points: <strong>{{ bmiPoints }}</strong></div>
      </div>
    </section>

    <section class="field">
      <label>4. How often do you add salt to meals or eat processed foods? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.sodium" value="rare" /> Rarely/never — 0 points</label>
        <label><input type="radio" v-model="form.sodium" value="sometimes" /> Sometimes (3-4x/week) — 3 points</label>
        <label><input type="radio" v-model="form.sodium" value="regular" /> Regularly/add processed foods — 6 points</label>
      </div>
    </section>

    <section class="field">
      <label>5. Do you engage in moderate physical activity 30 minutes, 5+ times/week? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.activity" value="yes" /> Yes — 0 points</label>
        <label><input type="radio" v-model="form.activity" value="no" /> No — 5 points</label>
      </div>
    </section>

    <section class="field">
      <label>6. How often do you consume alcoholic beverages? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.alcohol" value="rare" /> Rarely/never — 0 points</label>
        <label><input type="radio" v-model="form.alcohol" value="occasion" /> Occasionally (1-2/week) — 2 points</label>
        <label><input type="radio" v-model="form.alcohol" value="regular" /> Regularly (>2/day) — 4 points</label>
      </div>
    </section>

    <section class="field">
      <label>7. Do you regularly experience high stress or get less than 6 hours sleep/night? (required)</label>
      <div class="options">
        <label><input type="radio" v-model="form.stress" value="no" /> No — 0 points</label>
        <label><input type="radio" v-model="form.stress" value="yes" /> Yes — 3 points</label>
      </div>
    </section>

    <section v-if="allFilled" class="result card">
      <h4>Hypertension Risk Score</h4>
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

const STORAGE_KEY = 'hypertensionForm'

const defaultForm = {
  age: '',
  family: '',
  weight: null,
  height: null,
  sodium: '',
  activity: '',
  alcohol: '',
  stress: '',
}

const form = reactive({ ...defaultForm })
const result = ref({ total: 0, riskMessage: '' })

function loadForm() { try { const raw = localStorage.getItem(STORAGE_KEY); if (raw) Object.assign(form, JSON.parse(raw)) } catch (e) {} }
function saveForm() { try { localStorage.setItem(STORAGE_KEY, JSON.stringify(form)) } catch (e) {} }

const bmi = computed(() => {
  const w = Number(form.weight) || 0
  const h = Number(form.height) || 0
  if (!w || !h) return 0
  const m = h / 100
  return +(w / (m * m)).toFixed(1)
})

const bmiDisplay = computed(() => bmi.value > 0 ? bmi.value : '--')
const bmiPoints = computed(() => {
  const v = bmi.value
  if (v === 0) return 0
  if (v < 25) return 0
  if (v < 30) return 3
  return 6
})

function computeResult() {
  let total = 0
  total += ({ 'under40':0, '40-55':3, '56-69':6, '70plus':10 })[form.age] || 0
  total += ({ 'none':0, 'one':4, 'multiple':7 })[form.family] || 0
  total += bmiPoints.value
  total += ({ 'rare':0, 'sometimes':3, 'regular':6 })[form.sodium] || 0
  total += ({ 'yes':0, 'no':5 })[form.activity] || 0
  total += ({ 'rare':0, 'occasion':2, 'regular':4 })[form.alcohol] || 0
  total += ({ 'no':0, 'yes':3 })[form.stress] || 0

  let riskMessage = ''
  if (total <= 10) riskMessage = 'Low Risk — maintain healthy habits.'
  else if (total <= 20) riskMessage = 'Moderate Risk — consider lifestyle changes and monitoring.'
  else if (total <= 30) riskMessage = 'High Risk — measure BP and consult healthcare professional.'
  else riskMessage = 'Very High Risk — immediate medical evaluation recommended.'

  result.value = { total, riskMessage }
}

const riskNotes = computed(() => [
  { key: 'low', label: 'Low (0 - 10)', text: 'Low short-term risk.', color: '#2ecc71' },
  { key: 'moderate', label: 'Moderate (11 - 20)', text: 'Moderate risk — preventive action recommended.', color: '#f1c40f' },
  { key: 'high', label: 'High (21 - 30)', text: 'High risk — urgent evaluation advised.', color: '#f39c12' },
  { key: 'veryhigh', label: 'Very High (31 - 41)', text: 'Very high risk — immediate medical attention.', color: 'linear-gradient(90deg,#c4121a,#8b0f12)' },
])

const allFilled = computed(() => {
  return form.age !== '' && form.family !== '' && form.weight && form.height && form.sodium !== '' && form.activity !== '' && form.alcohol !== '' && form.stress !== ''
})

const currentRiskKey = computed(() => {
  const t = result.value.total || 0
  if (t <= 10) return 'low'
  if (t <= 20) return 'moderate'
  if (t <= 30) return 'high'
  return 'veryhigh'
})

const currentRiskColor = computed(() => {
  const k = currentRiskKey.value
  if (k === 'low') return '#2ecc71'
  if (k === 'moderate') return '#f1c40f'
  if (k === 'high') return '#f39c12'
  return 'linear-gradient(90deg,#c4121a,#8b0f12)'
})

watch(form, () => { saveForm(); computeResult() }, { deep: true })
onMounted(() => { loadForm(); computeResult() })
</script>
