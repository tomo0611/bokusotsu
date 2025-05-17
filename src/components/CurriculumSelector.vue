<template>
    <div class="d-flex flex-sm-row flex-column justify-center align-center ga-4 mt-4">
        <v-select label="入学年度" :items="years" v-model="defaultYear" style="max-width: 120px; min-width: 120px;" dense
            hide-details></v-select>
        <v-select label="入学学期" :items="semesters" v-model="defaultSemester" style="max-width: 120px; min-width: 120px;"
            dense hide-details></v-select>
        <v-select label="教育課程" :items="curriculums" v-model="defaultCurriculum"
            style="max-width: 240px; min-width: 120px;" dense hide-details></v-select>
    </div>
</template>

<script>
export default {
    data: () => ({
        years: ['2022'],
        defaultYear: '2022',
        semesters: ['前期'],
        defaultSemester: '前期',
        curriculums: ['公大 学部・学域 工 情報'],
        defaultCurriculum: '公大 学部・学域 工 情報',
    }),
}
</script>