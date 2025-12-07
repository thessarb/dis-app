<template>
    <div class="diabetes card">
        <h3>Heart Attack Risk Assessment</h3>
        <p style="color:var(--muted);margin-bottom:1rem">A quick, non-medical questionnaire based on common risk
            factors. Disclaimer: This is for educational use only and is NOT a substitute for professional medical
            advice.</p>

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
                <label><input type="radio" v-model="form.smoke" value="no" /> Never smoked / Quit more than 12 months
                    ago — 0 points</label>
                <label><input type="radio" v-model="form.smoke" value="yes" /> Yes, I currently smoke or quit less than
                    12 months ago — 5 points</label>
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
            <label>6. Do you engage in moderate or vigorous physical activity for at least 30 minutes, 3+ times/week?
                (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.activity" value="yes" /> Yes, regularly active — 0
                    points</label>
                <label><input type="radio" v-model="form.activity" value="no" /> No, little to no regular activity — 2
                    points</label>
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
                <input type="number" v-model.number="form.weight" placeholder="Weight (kg)"
                    style="width:140px;padding:0.5rem;border-radius:8px;border:1px solid rgba(0,0,0,0.08)" />
                <input type="number" v-model.number="form.height" placeholder="Height (cm)"
                    style="width:140px;padding:0.5rem;border-radius:8px;border:1px solid rgba(0,0,0,0.08)" />
                <div style="font-size:0.95rem;color:var(--muted);">Your BMI: <strong>{{ bmiDisplay }}</strong> — BMI
                    Points: <strong>{{ bmiPoints }}</strong></div>
            </div>
        </section>

        <section class="field">
            <label>9. Do you eat at least five servings of fruits and vegetables daily? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.diet" value="yes" /> Yes, 5 or more servings — 0 points</label>
                <label><input type="radio" v-model="form.diet" value="no" /> No, less than 5 servings — 2 points</label>
            </div>
        </section>

        <div style="margin-top:1rem">
            <button class="btn" :disabled="!allFilled" @click="calculate"
                style="padding:0.5rem 0.75rem;border-radius:8px;border:1px solid rgba(0,0,0,0.08);background:var(--accent);color:#fff">Calculate
                result</button>
        </div>

        <section v-if="showResult" class="result card">
            <h4>Your Heart Attack Risk Score</h4>
            <div class="result-content">
                <div style="display:flex;align-items:center;gap:12px;margin-bottom:0.75rem">
                    <div class="total-score" :style="{ background: currentRiskColor, color: '#fff' }">{{ result.total }}
                    </div>
                    <div style="font-weight:700">Total points</div>
                </div>

                <div>
                    <div style="font-weight:700;margin-bottom:0.5rem">You scored {{ result.total }} points</div>

                    <div v-if="result.total <= 5" style="margin-bottom:0.75rem">
                        <div style="font-weight:700">Low Risk</div>
                        <div>Your current lifestyle and family history indicate a very favorable risk profile. You are
                            likely taking excellent care of your cardiovascular health.</div>

                        <!-- Detailed information toggle -->
                        <div style="margin-top:0.75rem">
                            <button @click="showDetails = !showDetails"
                                style="background:transparent;border:none;color:var(--accent);cursor:pointer;padding:0;font-weight:600">Detailed
                                advice</button>
                            <div v-if="showDetails" style="margin-top:8px;color:var(--muted)">
                                <div>Maintain Excellent Habits: Continue your regular physical activity (aim for at least 150
                                    minutes of moderate-intensity cardio per week). Keep your diet rich in whole foods, fruits,
                                    and vegetables.</div>
                            </div>
                        </div>
                    </div>

                    <div v-else-if="result.total <= 10" style="margin-bottom:0.75rem">
                        <div style="font-weight:700">Low Risk</div>
                        <div>Your risk is low, but you have one or two minor factors that warrant attention.</div>

                        <!-- Detailed information toggle -->
                        <div style="margin-top:0.75rem">
                            <button @click="showDetails = !showDetails"
                                style="background:transparent;border:none;color:var(--accent);cursor:pointer;padding:0;font-weight:600">Detailed
                                advice</button>
                            <div v-if="showDetails" style="margin-top:8px;color:var(--muted)">
                                <ol style="margin-top:0.25rem">
                                    <li style="margin-bottom:0.5rem">Enhance Physical Activity: If you are inactive, start with
                                        a brisk walk 3-5 times a week. If you are already active, consider adding two days of
                                        resistance training.</li>
                                    <li v-if="form.diet === 'no'" style="margin-bottom:0.5rem">Dietary Review (The "5-a-Day"
                                        Rule): You scored points for diet — commit to getting servings of fruits and vegetables
                                        daily. Focus on incorporating more whole grains and lean proteins while reducing
                                        saturated fats and simple sugars.</li>
                                    <li v-if="form.bp === 'yes' || form.chol === 'yes'" style="margin-bottom:0.5rem">Manage
                                        Known Factors: You indicated medication for blood pressure or cholesterol — adhere
                                        strictly to the prescribed regimen.</li>
                                    <li v-if="form.smoke === 'yes'" style="margin-bottom:0.5rem">If you recently quit smoking or
                                        currently smoke, stay vigilant and use support resources to prevent relapse.</li>
                                    <li v-if="bmi < 25">Maintain your healthy BMI range (between 18.5 and 24.9).</li>
                                    <li v-if="bmi >= 25">Set small, sustainable goals
                                        for weight management through a balanced diet and increased activity.</li>
                                </ol>
                            </div>
                        </div>
                    </div>

                    <div v-else-if="result.total <= 15" style="margin-bottom:0.75rem">
                        <div style="font-weight:700">High Risk</div>
                        <div>You have multiple risk factors. While you may have controlled some factors, the cumulative
                            risk is increasing. Consultation is the best thing you should do.</div>
                    </div>

                    <div v-else style="margin-bottom:0.75rem">
                        <div style="font-weight:700">High Risk</div>
                        <div>High risk — consider prompt medical evaluation and targeted interventions.</div>
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
    try { const raw = localStorage.getItem(STORAGE_KEY); if (raw) Object.assign(form, JSON.parse(raw)) } catch (e) { }
}

function saveForm() { try { localStorage.setItem(STORAGE_KEY, JSON.stringify(form)) } catch (e) { } }

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
    total += ({ 'under45': 0, '45-54': 3, '55-64': 6, '65plus': 10 })[form.age] || 0
    total += ({ 'no': 0, 'yes': 5 })[form.smoke] || 0
    total += ({ 'no': 0, 'yes': 4 })[form.family] || 0
    total += ({ 'no': 0, 'yes': 3 })[form.bp] || 0
    total += ({ 'no': 0, 'yes': 3 })[form.chol] || 0
    total += ({ 'yes': 0, 'no': 2 })[form.activity] || 0
    total += ({ 'no': 0, 'yes': 5 })[form.diabetes] || 0
    total += bmiPoints.value
    total += ({ 'yes': 0, 'no': 2 })[form.diet] || 0

    // categorize
    let riskMessage = ''
    if (total <= 5) riskMessage = 'Very Low Risk'
    else if (total <= 10) riskMessage = 'Low Risk'
    else if (total <= 15) riskMessage = 'Moderate Risk'
    else riskMessage = 'High Risk'

    result.value = { total, riskMessage }
}

const allFilled = computed(() => {
    return form.age !== '' && form.smoke !== '' && form.family !== '' && form.bp !== '' && form.chol !== '' && form.activity !== '' && form.diabetes !== '' && form.weight && form.height && form.diet !== ''
})

const currentRiskKey = computed(() => {
    const t = result.value.total || 0
    if (t <= 5) return 'low'
    if (t <= 10) return 'moderate'
    return 'high'
})

const currentRiskColor = computed(() => {
    const k = currentRiskKey.value
    if (k === 'low') return '#2ecc71'
    if (k === 'moderate') return '#f39c12'
    return 'linear-gradient(90deg,#ff4d4d,#dc143c)'
})

const showResult = ref(false)
const showDetails = ref(false)

function calculate() {
    computeResult()
    showResult.value = true
    showDetails.value = false
}

watch(form, () => { saveForm(); showResult.value = false }, { deep: true })
onMounted(() => { loadForm() })
</script>
