<template>
    <div class="diabetes card">
        <h3>Cold Severity Assessor</h3>
        <p style="color:var(--muted);margin-bottom:1rem">How bad is your current cold? Estimate symptom severity and
            expected recovery time. If you have difficulty breathing, chest pain, or fever over 103°F (39.4°C), seek
            immediate medical attention.</p>

        <section class="field">
            <label>1. How long have you had noticeable symptoms? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.duration" value="under2" /> Less than 2 days (Onset) — 0
                    points</label>
                <label><input type="radio" v-model="form.duration" value="3-7" /> 3 - 7 days (Peak Symptom Stage) — 3
                    points</label>
                <label><input type="radio" v-model="form.duration" value="8-14" /> 8 - 14 days (Recovery/Lingering) — 1
                    point</label>
            </div>
        </section>

        <section class="field">
            <label>2. How severe are your nasal symptoms? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.nasal" value="mild" /> Mild congestion, manageable — 0
                    points</label>
                <label><input type="radio" v-model="form.nasal" value="moderate" /> Moderate congestion, interferes with
                    sleep/talking — 2 points</label>
                <label><input type="radio" v-model="form.nasal" value="severe" /> Severe blockage/constant dripping,
                    requires constant attention — 4 points</label>
            </div>
        </section>

        <section class="field">
            <label>3. How severe is your cough or sore throat? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.cough" value="mild" /> Mild scratchiness, occasional,
                    non-productive cough — 0 points</label>
                <label><input type="radio" v-model="form.cough" value="moderate" /> Moderate soreness/pain, frequent
                    coughing fits — 2 points</label>
                <label><input type="radio" v-model="form.cough" value="severe" /> Severe pain when swallowing,
                    constant/deep chesty cough — 5 points</label>
            </div>
        </section>

        <section class="field">
            <label>4. How much do fatigue and body aches affect you? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.fatigue" value="mild" /> Slightly tired, but can manage daily
                    tasks — 0 points</label>
                <label><input type="radio" v-model="form.fatigue" value="moderate" /> Moderate fatigue, struggle to
                    concentrate or work — 3 points</label>
                <label><input type="radio" v-model="form.fatigue" value="severe" /> Severe exhaustion, debilitating body
                    aches, unable to leave bed — 6 points</label>
            </div>
        </section>

        <section class="field">
            <label>5. Have you had a fever? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.fever" value="none" /> No fever, or temperature below 100°F
                    (37.8°C) — 0 points</label>
                <label><input type="radio" v-model="form.fever" value="low" /> Low-grade fever (100°F - 102°F) — 3
                    points</label>
                <label><input type="radio" v-model="form.fever" value="high" /> Moderate/High fever (Over 102°F) — 5
                    points</label>
            </div>
        </section>

        <div style="margin-top:12px">
            <button class="btn-accent" :disabled="!allFilled" @click="calculate">Calculate result</button>
        </div>

        <section v-if="showResult" class="result card">
            <h4>Cold Severity Score</h4>
            <div class="result-content">
                <div style="display:flex;align-items:center;gap:12px;margin-bottom:0.75rem">
                    <div class="total-score" :style="{ background: currentRiskColor, color: '#fff' }">{{ result.total }}
                    </div>
                    <div style="font-weight:700">Total points</div>
                </div>

                <div>
                    <div style="font-weight:700;margin-bottom:0.5rem">You scored {{ result.total }} points</div>

                    <div style="margin-bottom:0.75rem">
                        <template v-if="result.total <= 5">
                            <div style="font-weight:700">Mild Cold / End Stage</div>
                            <div style="margin-top:6px">Your symptoms are light, localized, or you are nearing the very
                                end of your illness. You have likely passed the peak contagiousness period.</div>

                            <div style="margin-top:0.75rem">
                                <button @click="showDetails = !showDetails"
                                    style="background:transparent;border:none;color:var(--accent);cursor:pointer;padding:0;font-weight:600">Detailed
                                    advice</button>
                                <div v-if="showDetails" style="margin-top:8px;color:var(--muted)">
                                    <ol style="margin:6px 0 0 18px">
                                        <li>Hydration Focus: Drink plenty of fluids — aim for water, herbal tea
                                            (chamomile, ginger), or clear broths. Avoid excessive caffeine and alcohol,
                                            which can be dehydrating.</li>
                                        <li>Rest and Sleep Hygiene: Continue to get 7-9 hours of quality sleep. Do not
                                            push yourself with strenuous activity. Take an extra half-hour in bed if
                                            needed.</li>
                                        <li>Symptom Relief: Use saline nasal spray 2-3 times a day to keep nasal
                                            passages moist. A single dose of a mild pain reliever (like acetaminophen or
                                            ibuprofen) can be used for minor headache or body ache.</li>
                                        <li>Preventing Spread: Maintain good hand hygiene. Continue to cough/sneeze into
                                            your elbow or a tissue.</li>
                                    </ol>
                                </div>
                            </div>
                        </template>

                        <template v-else-if="result.total <= 12">
                            <div style="font-weight:700">Moderate Cold</div>
                            <div style="margin-top:6px">You are most likely in the peak symptom stage. Symptoms are
                                disruptive, but manageable at home.</div>

                            <div style="margin-top:0.75rem">
                                <button @click="showDetails = !showDetails"
                                    style="background:transparent;border:none;color:var(--accent);cursor:pointer;padding:0;font-weight:600">Detailed
                                    advice</button>
                                <div v-if="showDetails" style="margin-top:8px;color:var(--muted)">
                                    <ol style="margin:6px 0 0 18px">
                                        <li>Prioritize Bed Rest: Stay home from work/school if possible. If you must work, keep
                                            it light and remote. Elevate your head with an extra pillow when sleeping to help
                                            drain sinuses and ease coughing.</li>
                                        <li>Over-the-Counter (OTC) Management: Use acetaminophen or ibuprofen as needed to
                                            control body aches and low-grade fever.</li>
                                        <li>Moist Air Therapy: Use a cool-mist humidifier in your bedroom, making sure to clean
                                            it daily. Take hot, steamy showers 2-3 times a day.</li>
                                        <li>Monitoring: Keep a basic log of your temperature and pain levels. If symptoms
                                            dramatically worsen or you spike a high fever, seek higher-level care.</li>
                                    </ol>
                                </div>
                            </div>
                        </template>

                        <template v-else>
                            <div style="font-weight:700">Severe Cold / Flu-like Symptoms</div>
                            <div style="margin-top:6px">Interpretation: The severity suggests a significant systemic
                                illness (potentially the flu or a bad cold) that is heavily impacting your body. Please
                                seek professional help immediately.</div>
                        </template>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script setup>
import { reactive, ref, computed, onMounted, watch } from 'vue'

const STORAGE_KEY = 'coldForm'

const defaultForm = {
    duration: '',
    nasal: '',
    cough: '',
    fatigue: '',
    fever: '',
}

const form = reactive({ ...defaultForm })
const result = ref({ total: 0 })

function loadForm() {
    try {
        const raw = localStorage.getItem(STORAGE_KEY)
        if (raw) Object.assign(form, JSON.parse(raw))
    } catch (e) { }
}

function saveForm() {
    try { localStorage.setItem(STORAGE_KEY, JSON.stringify(form)) } catch (e) { }
}

function computeResult() {
    let total = 0

    // duration
    const durPts = ({ 'under2': 0, '3-7': 3, '8-14': 1 })[form.duration] || 0
    total += durPts

    // nasal
    const nasalPts = ({ 'mild': 0, 'moderate': 2, 'severe': 4 })[form.nasal] || 0
    total += nasalPts

    // cough/sore throat
    const coughPts = ({ 'mild': 0, 'moderate': 2, 'severe': 5 })[form.cough] || 0
    total += coughPts

    // fatigue
    const fatiguePts = ({ 'mild': 0, 'moderate': 3, 'severe': 6 })[form.fatigue] || 0
    total += fatiguePts

    // fever
    const feverPts = ({ 'none': 0, 'low': 3, 'high': 5 })[form.fever] || 0
    total += feverPts

    result.value = { total }

    // set message and estimate
    let riskMessage = ''
    if (total <= 5) riskMessage = 'Mild Cold / End Stage — Recovery expected within 1-3 days. Focus on hydration and rest.'
    else if (total <= 12) riskMessage = 'Moderate Cold — Expect another 4-7 days until full recovery. Manage discomfort and rest.'
    else if (total <= 18) riskMessage = 'Severe Cold / Flu-like Symptoms — Full recovery may take 7-10+ days. Seek medical advice if worsening.'
    else riskMessage = 'Extremely Severe — Illness may last 10+ days. Consult a healthcare provider.'

    result.value.riskMessage = riskMessage
}

const riskNotes = computed(() => [
    { key: 'mild', label: 'Mild (0 - 5)', text: 'Light symptoms or near end stage.', estimate: 'Recovery estimate: 1-3 days', color: '#2ecc71' },
    { key: 'moderate', label: 'Moderate (6 - 12)', text: 'Peak symptom stage, moderate impact.', estimate: 'Recovery estimate: 4-7 days', color: '#f39c12' },
    { key: 'severe', label: 'Severe (13 - 18)', text: 'Severe or flu-like symptoms.', estimate: 'Recovery estimate: 7-10+ days', color: '#e74c3c' },
    { key: 'extreme', label: 'Extremely Severe (19 - 24)', text: 'High intensity symptoms — seek medical advice.', estimate: 'Recovery estimate: 10+ days', color: 'linear-gradient(90deg,#c4121a,#8b0f12)' }
])

const allFilled = computed(() => {
    return form.duration !== '' && form.nasal !== '' && form.cough !== '' && form.fatigue !== '' && form.fever !== ''
})

const currentRiskKey = computed(() => {
    const t = result.value.total || 0
    if (t <= 5) return 'mild'
    if (t <= 12) return 'moderate'
    if (t <= 18) return 'severe'
    return 'extreme'
})

const currentRiskColor = computed(() => {
    const k = currentRiskKey.value
    if (k === 'mild') return '#2ecc71'
    if (k === 'moderate') return '#f39c12'
    if (k === 'severe') return 'linear-gradient(90deg,#ff4d4d,#dc143c)'
    return 'linear-gradient(90deg,#c4121a,#8b0f12)'
})

// UI state: show result only after clicking calculate
const showResult = ref(false)
const showDetails = ref(false)

function calculate() {
    computeResult()
    showResult.value = true
    showDetails.value = false
    saveForm()
    showDetails.value = false
}

// save form when it changes, but don't auto-compute results
watch(form, () => { saveForm() }, { deep: true })

onMounted(() => { loadForm() })
</script>
