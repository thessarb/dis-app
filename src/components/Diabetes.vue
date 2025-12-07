<template>
    <div class="diabetes card">
        <h3>Diabetes risk questionnaire</h3>

        <section class="field">
            <label>Your age group (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.ageGroup" value="under35" /> Under 35 years (0 points)</label>
                <label><input type="radio" v-model="form.ageGroup" value="35-44" /> 35 to 44 years (2 points)</label>
                <label><input type="radio" v-model="form.ageGroup" value="45-54" /> 45 to 54 years (4 points)</label>
                <label><input type="radio" v-model="form.ageGroup" value="55-64" /> 55 to 64 years (6 points)</label>
                <label><input type="radio" v-model="form.ageGroup" value="65plus" /> 65 years or over (8 points)</label>
            </div>
        </section>

        <section class="field">
            <label>Where were you born? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.birthplace" value="asia" /> Asia (including Indian
                    sub-continent), Middle East, North Africa, Southern Europe (2 points)</label>
                <label><input type="radio" v-model="form.birthplace" value="other" /> Other (0 points)</label>
            </div>
        </section>

        <section class="field">
            <label>Your gender (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.gender" value="female" /> Female (0 points)</label>
                <label><input type="radio" v-model="form.gender" value="male" /> Male (3 points)</label>
            </div>
        </section>

        <section class="field">
            <label>Have any close family members (parents or siblings) been diagnosed with diabetes? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.historywithdiabetes" value="no" /> No (0 points)</label>
                <label><input type="radio" v-model="form.historywithdiabetes" value="yes" /> Yes (3 points)</label>
            </div>
        </section>

        <section class="field">
            <label>Have you ever been told you had high blood glucose (for example during an exam, illness or
                pregnancy)? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.glucosehistory" value="no" /> No (0 points)</label>
                <label><input type="radio" v-model="form.glucosehistory" value="yes" /> Yes (6 points)</label>
            </div>
        </section>

        <section class="field">
            <label>Are you currently on medication for high blood pressure? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.bpmedication" value="no" /> No (0 points)</label>
                <label><input type="radio" v-model="form.bpmedication" value="yes" /> Yes (2 points)</label>
            </div>
        </section>

        <section class="field">
            <label>Do you currently smoke any form of tobacco daily? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.smoking" value="no" /> No (0 points)</label>
                <label><input type="radio" v-model="form.smoking" value="yes" /> Yes (2 points)</label>
            </div>
        </section>

        <section class="field">
            <label>How frequently do you eat vegetables or fresh fruit? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.vegfruit" value="everyday" /> Every day (0 points)</label>
                <label><input type="radio" v-model="form.vegfruit" value="noteveryday" /> Not every day (1
                    point)</label>
            </div>
        </section>

        <section class="field">
            <label>On average, do you complete at least 2.5 hours of physical activity each week (for example 30 minutes
                5 or more days)? (required)</label>
            <div class="options">
                <label><input type="radio" v-model="form.physicalactivity" value="yes" /> Yes (0 points)</label>
                <label><input type="radio" v-model="form.physicalactivity" value="no" /> No (2 points)</label>
            </div>
        </section>

        <section class="field">
            <label for="waistMeasurement">What is your waist measurement in centimetres? (required)</label>
            <div style="font-size:0.9rem;color:var(--muted);">Measure your waist below the ribs (around the navel) while
                standing.</div>
            <input id="waistMeasurement" type="number" v-model.number="form.waistMeasurement"
                placeholder="Enter waist measurement (cm)" />
            <div style="font-size:0.9rem;color:var(--muted);">Points will be calculated from this measurement.</div>
        </section>

        <div style="margin-top:12px">
            <button class="btn-accent" :disabled="!allFilled" @click="calculate">Calculate result</button>
        </div>

        <section v-if="showResult" class="result card">
            <h4>Result</h4>
            <div class="result-content">
                <div style="display:flex;align-items:center;gap:12px;margin-bottom:0.75rem">
                    <div class="total-score" :style="{ background: currentRiskColor, color: '#fff' }">{{ result.total }}
                    </div>
                    <div style="font-weight:700">Total points</div>
                </div>

                <div>
                    <div style="font-weight:700;margin-bottom:0.5rem">You scored {{ result.total }} points</div>

                    <!-- Main advice depending on score -->
                    <div style="margin-bottom:0.75rem">
                        <template v-if="result.total <= 5">
                            <div style="font-weight:700">Low Risk</div>
                            <ul style="margin:0;padding-left:18px">
                                <li>You are at a low risk of developing Type 2 Diabetes within the next 5 years
                                    (approximately 1 person in every 100).</li>
                                <li>Maintenance and Sustained Healthy Habits is recommended</li>
                            </ul>
                        </template>

                        <template v-else-if="result.total <= 11">
                            <div style="font-weight:700">Intermediate Risk</div>
                            <div>
                                You are at an intermediate risk of developing Type 2 Diabetes within the next 5 years
                                <span v-if="result.total >= 6 && result.total <= 8"> (approximately 1 in 50)</span>
                                <span v-else-if="result.total >= 9 && result.total <= 11"> (approximately 1 in 30)</span>
                            </div>

                            <!-- Detailed information toggle -->
                            <div style="margin-top:0.75rem">
                                <button @click="showDetails = !showDetails"
                                    style="background:transparent;border:none;color:var(--accent);cursor:pointer;padding:0;font-weight:600">Detailed
                                    advice</button>
                                <div v-if="showDetails" style="margin-top:8px;color:var(--muted)">
                                    <ol style="margin:6px 0 0 18px">
                                        <li>Print out your score (or the assessment details) and take it to your General Practitioner or relevant healthcare professional.</li>
                                        <li>This is the ideal time for preventive intervention; your doctor can help confirm the risk and recommend appropriate blood tests.</li>
                                    </ol>
                                </div>
                            </div>
                        </template>

                        <template v-else>
                            <div style="font-weight:700">High Risk</div>
                            <ul style="margin:0;padding-left:18px">
                                <li>Risk Interpretation: You are at a high risk of developing Type 2 Diabetes within the
                                    next 5 years, or you may have undiagnosed Type 2 Diabetes (ranging from
                                    approximately 1 in 14 for a score of 12-15, to 1 in 3 for a score of 20+).</li>
                                <li>Immediate medical evaluation is necessary.</li>
                            </ul>
                        </template>
                    </div>

                    <div style="margin-top:10px;color:var(--muted);font-size:0.95rem">The overall score may overestimate
                        the risk of
                        diabetes in those aged less than 25 years.</div>
                </div>
            </div>
        </section>
    </div>
</template>

<script setup>
import { reactive, ref, computed, onMounted, watch } from 'vue'

const STORAGE_KEY = 'diabetesForm'

const defaultForm = {
    ageGroup: '',
    gender: '',
    descent: '',
    birthplace: '',
    historywithdiabetes: '',
    glucosehistory: '',
    bpmedication: '',
    smoking: '',
    vegfruit: '',
    physicalactivity: '',
    waistMeasurement: null,
}

const form = reactive({ ...defaultForm })
const result = ref({ total: 0, breakdown: [] })

function loadForm() {
    try {
        const raw = localStorage.getItem(STORAGE_KEY)
        if (raw) {
            const parsed = JSON.parse(raw)
            Object.assign(form, parsed)
        }
    } catch (e) {
        // ignore
    }
}

function saveForm() {
    try {
        localStorage.setItem(STORAGE_KEY, JSON.stringify(form))
    } catch (e) {
        // ignore
    }
}

function computeResult() {
    // compute points according to tables
    const breakdown = []
    let total = 0

    // age
    const agePoints = {
        under35: 0,
        '35-44': 2,
        '45-54': 4,
        '55-64': 6,
        '65plus': 8,
    }[form.ageGroup] || 0
    total += agePoints
    breakdown.push({ label: 'Age', points: agePoints })

    // gender
    const genderPoints = form.gender === 'male' ? 3 : 0
    total += genderPoints
    breakdown.push({ label: 'Gender', points: genderPoints })

    // descent (3a)
    const descentPoints = form.descent === 'yes' ? 2 : 0
    total += descentPoints
    breakdown.push({ label: 'Descent', points: descentPoints })

    // birthplace (3b) - points only for Asia/MiddleEast etc
    const birthplacePoints = form.birthplace === 'asia' ? 2 : 0
    total += birthplacePoints
    breakdown.push({ label: 'Birthplace', points: birthplacePoints })

    // family history
    const familyPoints = form.historywithdiabetes === 'yes' ? 3 : 0
    total += familyPoints
    breakdown.push({ label: 'Family history', points: familyPoints })

    // high blood glucose
    const glucosePoints = form.glucosehistory === 'yes' ? 6 : 0
    total += glucosePoints
    breakdown.push({ label: 'High blood glucose', points: glucosePoints })

    // blood pressure meds
    const bpPoints = form.bpmedication === 'yes' ? 2 : 0
    total += bpPoints
    breakdown.push({ label: 'BP medication', points: bpPoints })

    // smoking
    const smokePoints = form.smoking === 'yes' ? 2 : 0
    total += smokePoints
    breakdown.push({ label: 'Smoking', points: smokePoints })

    // veg/fruit
    const vegPoints = form.vegfruit === 'noteveryday' ? 1 : 0
    total += vegPoints
    breakdown.push({ label: 'Veg & fruit', points: vegPoints })

    // physical activity
    const activityPoints = form.physicalactivity === 'no' ? 2 : 0
    total += activityPoints
    breakdown.push({ label: 'Physical activity', points: activityPoints })

    // waist points: rules differ depending on descent/birthplace
    const waist = Number(form.waistMeasurement) || 0
    let waistPoints = 0

    // determine whether to use Asian/Indigenous thresholds
    const useAsianThreshold = form.descent === 'yes' || form.birthplace === 'asia'

    if (form.gender === 'male') {
        if (useAsianThreshold) {
            if (waist < 90) waistPoints = 0
            else if (waist <= 100) waistPoints = 4
            else waistPoints = 7
        } else {
            if (waist < 102) waistPoints = 0
            else if (waist <= 110) waistPoints = 4
            else waistPoints = 7
        }
    } else if (form.gender === 'female') {
        if (useAsianThreshold) {
            if (waist < 80) waistPoints = 0
            else if (waist <= 90) waistPoints = 4
            else waistPoints = 7
        } else {
            if (waist < 88) waistPoints = 0
            else if (waist <= 100) waistPoints = 4
            else waistPoints = 7
        }
    }

    total += waistPoints
    breakdown.push({ label: 'Waist', points: waistPoints, waist })

    // risk categorization
    let riskLabel = ''
    let riskMessage = ''
    if (total <= 5) {
        riskLabel = 'Low risk (0 to 5)'
        riskMessage = 'Approximately one person in every 100 may develop diabetes.'
    } else if (total <= 8) {
        riskLabel = 'Intermediate risk (6 to 8)'
        riskMessage = 'Approximately one person in every 50 may develop diabetes.'
    } else {
        riskLabel = 'High risk (9 or more)'
        riskMessage = 'You may be at increased risk of type 2 diabetes. Discuss your score and your individual risk with your doctor. Improving your lifestyle may help reduce your risk of developing type 2 diabetes.'
    }

    result.value = { total, breakdown, riskLabel, riskMessage }
}

const allFilled = computed(() => {
    return (
        form.ageGroup !== '' &&
        form.birthplace !== '' &&
        form.gender !== '' &&
        form.historywithdiabetes !== '' &&
        form.glucosehistory !== '' &&
        form.bpmedication !== '' &&
        form.smoking !== '' &&
        form.vegfruit !== '' &&
        form.physicalactivity !== '' &&
        (Number(form.waistMeasurement) > 0)
    )
})

const currentRiskKey = computed(() => {
    const t = result.value.total || 0
    if (t <= 5) return 'low'
    if (t <= 11) return 'intermediate'
    return 'high'
})

const currentRiskColor = computed(() => {
    const key = currentRiskKey.value
    if (key === 'low') return '#2ecc71'
    if (key === 'intermediate') return '#f39c12'
    return 'linear-gradient(90deg, #ff4d4d, #dc143c)'
})

// UI state: show result only after user clicks Calculate
const showResult = ref(false)
const showDetails = ref(false)

function calculate() {
    computeResult()
    showResult.value = true
    showDetails.value = false
    saveForm()
}

// save form when it changes, but don't auto-compute results
watch(form, (newVal) => {
    saveForm()
}, { deep: true })

onMounted(() => {
    loadForm()
})
</script>
