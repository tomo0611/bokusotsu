<template>

    <h3 class="text-h5 text-center mt-4">修得済み科目選択</h3>
    <v-card class="mx-auto pa-2 mt-2 bg-white" max-width="700" variant="outlined" rounded="lg">
        <v-list :items="items" class="text-pre-wrap" :activatable="false">
            <template v-for="item in items">
                <v-list-item v-if="item.type === 'subheader'" :key="'category_' + item.id"
                    class="subheader bg-primary-lighter border-sm"
                    v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }">
                    {{ item.title }}
                </v-list-item>
                <v-list-item v-if="item.type === 'item'" :key="'list_' + item.id" :value="item.id"
                    @click="newValue => changeAquired(item)" v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }"
                    class="border-sm">
                    <template v-slot:prepend="{ is_aquired, select }">
                        <v-list-item-action start>
                            <v-checkbox-btn :model-value="item.is_aquired"></v-checkbox-btn>
                        </v-list-item-action>
                    </template>

                    {{ item.title }}
                </v-list-item>
            </template>
        </v-list>
    </v-card>


    <h3 class="text-h5 text-center mt-4">科目区分別単位数</h3>
    <v-card class="mx-auto pa-2 mt-2 bg-white" max-width="500" variant="outlined" rounded="lg">
        <v-list :items="items" class="text-pre-wrap" :activatable="false">
            <template v-for="item in items">
                <v-list-item v-if="item.type === 'subheader'" :key="'cat_' + item.id" class="border-sm"
                    v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }">
                    <div class="d-flex justify-space-between align-center">
                        <p>{{ item.title }}</p>
                        <p v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }" max-width="20">{{
                            caculateAquiredCredits(item.id).toPrecision(3) + '単位' }}</p>
                    </div>
                </v-list-item>
            </template>
        </v-list>
    </v-card>

    <v-card class="mx-auto mt-8 pa-4" max-width="700" variant="outlined" rounded="lg">
        <h5 class="text-h5">卒業研究履修資格（4年次進級要件）</h5>
        <p class="mt-2">0. 総計単位数が105単位以上</p>
        <p :class="'ml-8 ' + A1_totalCredits >= 105 ? 'text-green-darken-1' : 'text-red-lighten-1'">単位数は{{
            A1_totalCredits.toPrecision(3)
        }}単位です。</p>
        <p class="mt-2">1. 基幹教育科目（基礎教育科目を除く）</p>
        <p class="ml-4">外国語科目（英語・初修外国語） 計8単位</p>
        <p class="mt-2">2. 基礎教育科目 基礎物理学実験1B、応用物理学実験 計4単位</p>
        <p class="mt-2">3. 専門科目</p>
        <p class="ml-4">専門導入科目 情報工学基礎演習1・2 計 4 単位</p>
        <p class="ml-4">学科専門科目 情報工学演習1～3、情報工学実験1・2 計10単位</p>
        <p class="ml-4">外国語科目（英語・初修外国語） 計8単位</p>
        <p class="mt-2">4. 上記①～③に記載の科目を除く3年次までに配当の必修科目25単位と総合教養科目10単位、合計35単位から31単位以上</p>
    </v-card>

    <v-card class="mx-auto mt-8 pa-4" max-width="700" variant="outlined" rounded="lg">
        <h5 class="text-h5">卒業資格（卒業要件）</h5>
        <p class="mt-2">1-1. 総合教養科目（選択必修）が10単位以上</p>
        <p class="mt-2">1-2. 初年次教育科目が2単位</p>
        <p class="mt-2">1-3. 情報リテラシー科目が2単位</p>
        <p class="mt-2">1-4. 外国語科目の英語が6単位以上</p>
        <p class="mt-2">1-5-1. 外国語科目の初修外国語が2単位以上</p>
        <p class="mt-2">1-5-2. 初修外国語には2単位コースと4単位コースがある。コースに従い、入門1,2又は入門AB/初級ABのどちらか全てを履修しなければならない。</p>
        <p class="mt-2">1-6. 健康・スポーツ科学科目の講義が2単位</p>
        <p class="mt-2">1-7. 健康・スポーツ科学科目の実習が1単位</p>
        <p class="mt-2">1-8. 基幹教育科目（基礎教育科目除く）が29単位以上</p>
        <p class="mt-2">1-9. 基幹教育科目（基礎教育科目）と専門科目の合計が102単位以上</p>
        <p class="mt-2">1-10. 【無視】基幹教育科目（基礎教育科目除く）と基幹教育科目（基礎教育科目）と専門科目の合計が131単位以上</p>

        <p class="mt-2">2-1. 基幹教育科目（基礎教育科目）の必修科目が20単位</p>
        <p class="mt-2">2-2. 基幹教育科目（基礎教育科目）の選択科目が10単位以上</p>
        <p class="mt-2">3-1. 専門科目の学部共通科目の必修科目が2単位</p>
        <p class="mt-2">3-2. 専門科目の専門導入科目の必修科目が4単位</p>
        <p class="mt-2">3-3. 専門科目の学科専門科目の必修科目が18単位</p>
        <p class="mt-2">3-4. 学部共通科目、専門導入科目、学科専門科目の中の必修でない科目から48単位以上</p>
        <p class="mt-2">3-5. 【無視】学部共通科目、専門導入科目、学科専門科目を合わせて72単位以上を修得</p>
    </v-card>
</template>


<script setup lang="ts">
import { computed, ref, onMounted } from 'vue'

type SubjectCategory = {
    id: string;
    name: string;
    level: string;
    parent: string | null;
};

type SubjectAllocation = {
    id: string;
    name: string;
    credits: float;
    allocated_grade: number;
    evaluation_category: string;
    is_required: boolean;
    level: number;
};

const items = ref([])
const subject_evaluation_category = ref([])
const subject_allocation = ref([])
const aquired_subjects = ref([])

const A1_totalCredits = ref(0);

/**
 * 修得した科目の配列を返却
 * @returns {string[]} - 修得した科目の配列
 */
function getAquiredSubjects(): string[] {
    const ids = [];
    for (const item of items.value) {
        if (item.type == "item" && item.is_aquired) {
            ids.push(item.id);
        }
    }
    return ids;
}
/**
 * 指定した科目が修得済みかどうかを返す
 * @param {string} id - 科目ID
 * @returns {boolean} - 修得済みかどうか
 */
function isAquired(id: string, aquired_subjects: string[] = []): boolean {
    if (aquired_subjects.length == 0) {
        aquired_subjects = getAquiredSubjects();
    }
    for (const aquired_subject of aquired_subjects) {
        if (aquired_subject === id) {
            return true
        }
    }
    return false
}

onMounted(async () => {
    const res = await fetch('/subject_evaluation_category.json')
    const res2 = await fetch('/subject_allocation.json')
    const res3 = await fetch('/aquired_subjects.json')
    if (res.ok && res2.ok && res3.ok) {
        subject_evaluation_category.value = await res.json();
        subject_allocation.value = await res2.json();
        aquired_subjects.value = await res3.json();
        for (const item of subject_evaluation_category.value) {
            items.value.push({ type: 'subheader', id: item.id, title: item.name, level: item.level })
            for (const sa_item of subject_allocation.value) {
                if (sa_item.evaluation_category === item.id) {
                    items.value.push({
                        type: 'item',
                        id: sa_item.id,
                        title: (sa_item.is_required ? '[必修] ' : '') + sa_item.name + ' (' + sa_item.credits.toPrecision(3) + ' 単位, ' + sa_item.allocated_grade + '年次配当)',
                        value: sa_item.id,
                        is_aquired: isAquired(sa_item.id, aquired_subjects.value),
                        is_required: sa_item.is_required,
                        level: item.level
                    })
                }
            }
        }

        A1_totalCredits.value = caculateAquiredCredits('A') + caculateAquiredCredits('B') + caculateAquiredCredits('C');

    }
});

/**
 * 指定した科目カテゴリ以下に属する修得済みの科目の合計単位数を数える
 * @param {string} id - 科目カテゴリのID
 * @returns {float} - 修得済みの科目の合計単位数
 */
function caculateAquiredCredits(id: string, only_required: boolean = false) {
    let sum: float = 0.00;
    let target_subject_category_ids = this.getAllSubjectCategoryIds(id);
    const aquired_subjects = this.getAquiredSubjects();
    for (const item of this.subject_allocation) {
        if (target_subject_category_ids.includes(item.evaluation_category) && this.isAquired(item.id, [])) {
            // 必修のみで計算する場合は必修以外を除外
            if (only_required && !item.is_required) {
                continue;
            }
            sum += item.credits;
        }
    }
    return sum;
}

/**
 * 指定した科目カテゴリ以下に属する全ての科目カテゴリのIDを取得する
 * @param {string} id - 科目カテゴリのID
 * @returns {string[]} - 科目カテゴリのIDの配列
 */
function getAllSubjectCategoryIds(id: string): string[] {
    let target_subject_category_ids = [id];
    for (const item of this.subject_evaluation_category) {
        // idが自分(親)かどうか確かめることにより子を取得する
        if (item.parent === id) {
            target_subject_category_ids.push(item.id);
            target_subject_category_ids.push(...this.getAllSubjectCategoryIds(item.id));
        }
    }
    return target_subject_category_ids;
}

/**
 * 履修済みかどうかの状態を変更するメソッド
 * @param {Object} item - 変更対象のアイテムオブジェクト
 */
function changeAquired(item) {
    console.log('アイテム:', item);

    // 該当するアイテムの is_aquired プロパティを更新
    item.is_aquired = !item.is_aquired;

    // 必要であれば、ここでデータの永続化（APIコールなど）を行う
    // 例: this.saveItemStatus(item.value, newValue);
}
</script>