<template>

    <div class="d-lg-flex justify-lg-center ga-lg-8">

        <div>
            <div>
                <h3 class="text-h5 text-center mt-4">修得済み科目選択</h3>
                <v-card class="mx-auto pa-2 mt-2 bg-white text-body-2" max-width="700" variant="outlined" rounded="lg">
                    <v-list :items="items" class="text-pre-wrap" :activatable="false">
                        <template v-for="item in items">
                            <v-list-item v-if="item.type === 'subheader'" :key="'category_' + item.id"
                                class="subheader bg-primary-lighter border-sm"
                                v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }" min-height="10">
                                {{ item.name }}
                            </v-list-item>
                            <v-list-item v-if="item.type === 'item'" :key="'list_' + item.id" :value="item.id"
                                @click="newValue => changeAquired(item)"
                                v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }" class="border-sm"
                                min-height="10">
                                <!-- これを付けないとCheckboxが消えた -->
                                <template v-slot:prepend="{ isSelected, select }">
                                    <v-list-item-action start>
                                        <v-checkbox-btn :model-value="item.is_aquired" color="primary"
                                            density="compact"></v-checkbox-btn>
                                    </v-list-item-action>
                                </template>

                                {{ item.name }}
                            </v-list-item>
                        </template>
                    </v-list>
                </v-card>
            </div>


        </div>
        <div>
            <div>
                <h3 class="text-h5 text-center mt-4">科目区分別単位数</h3>
                <v-card class="mx-auto pa-2 mt-2 bg-white text-body-2" max-width="500" variant="outlined" rounded="lg">
                    <v-list :items="items" class="text-pre-wrap" :activatable="false">
                        <template v-for="item in items">
                            <v-list-item v-if="item.type === 'subheader'" :key="'cat_' + item.id" class="border-sm"
                                min-height="10" v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }">
                                <div class="d-flex justify-space-between align-center">
                                    <p>{{ item.name }}</p>
                                    <p v-bind:style="{ marginLeft: 25 * (item.level - 1) + 'px' }" max-width="20">{{
                                        caculateAquiredCredits(item.id).toPrecision(3) + '単位' }}</p>
                                </div>
                            </v-list-item>
                        </template>
                    </v-list>
                </v-card>
            </div>

            <v-card class="mx-auto mt-8 pa-4" max-width="700" variant="outlined" rounded="lg">
                <h5 class="text-h5">卒業資格判定（卒業要件）</h5>
                <p>
                    履修方法に示す1および2の履修により、131単位以上修得すること</p>
                <p v-if="caculateAquiredCredits('A') + caculateAquiredCredits('B') + caculateAquiredCredits('C') >= 131"
                    class="font-weight-black text-green-darken-1">
                    合計履修単位数は{{
                        (caculateAquiredCredits('A') + caculateAquiredCredits('B') +
                            caculateAquiredCredits('C')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black text-red-darken-1">
                    合計履修単位数は{{
                        (caculateAquiredCredits('A') + caculateAquiredCredits('B') +
                            caculateAquiredCredits('C')).toPrecision(3)
                    }}単位で条件を満たしていません。
                </p>

                <p class="mt-2">1．基幹教育科目</p>
                <p class="ml-4">基幹教育科目より次の(1)～(6)のとおり履修し、29単位以上（必修25単位選択4単位以上）修得すること</p>
                <p class="ml-4">(1) 総合教養科目 10単位</p>
                <p v-if="caculateAquiredCredits('A1') >= 10" class="font-weight-black ml-10 text-green-darken-1">
                    総合教養科目の単位数は{{
                        (caculateAquiredCredits('A1')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-10 text-red-darken-1">
                    総合教養科目の単位数は{{
                        (caculateAquiredCredits('A1')).toPrecision(3)
                    }}単位で条件を満たしていません。
                </p>
                <p class="ml-4">(2) 初年次教育科目 2単位</p>
                <p v-if="caculateAquiredCredits('A2') >= 2" class="font-weight-black ml-10 text-green-darken-1">
                    初年次教育科目の単位数は{{
                        (caculateAquiredCredits('A2')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-10 text-red-darken-1">
                    初年次教育科目の単位数は{{
                        (caculateAquiredCredits('A2')).toPrecision(3)
                    }}単位で条件を満たしていません。
                </p>
                <p class="ml-4">(3) 情報リテラシー科目 2単位</p>
                <p v-if="caculateAquiredCredits('A3') >= 2" class="font-weight-black ml-10 text-green-darken-1">
                    情報リテラシー科目の単位数は{{
                        (caculateAquiredCredits('A3')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-10 text-red-darken-1">
                    情報リテラシー科目の単位数は{{
                        (caculateAquiredCredits('A3')).toPrecision(3)
                    }}単位で条件を満たしていません。
                </p>
                <p class="ml-4">(4) 外国語科目</p>
                <p class="ml-10">英語 6単位</p>
                <p v-if="caculateAquiredCredits('A41') >= 6" class="font-weight-black ml-14 text-green-darken-1">
                    英語科目の単位数は{{
                        (caculateAquiredCredits('A41')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-14 text-red-darken-1">
                    英語科目の単位数は{{
                        (caculateAquiredCredits('A41')).toPrecision(3)
                    }}単位で条件を満たしていません。
                </p>
                <p class="ml-10">初修外国語 2単位</p>
                <p v-if="caculateAquiredCredits('A42') >= 2" class="font-weight-black ml-14 text-green-darken-1">
                    初修外国語の単位数は{{
                        (caculateAquiredCredits('A42')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-14 text-red-darken-1">
                    初修外国語の単位数は{{
                        (caculateAquiredCredits('A42')).toPrecision(3)
                    }}単位で条件を満たしていません。
                </p>
                <p class="ml-4">(5) 健康・スポーツ科学科目 3単位</p>
                <p v-if="caculateAquiredCredits('A5') >= 3" class="font-weight-black ml-10 text-green-darken-1">
                    健康・スポーツ科学科目の単位数は{{
                        (caculateAquiredCredits('A5')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-10 text-red-darken-1">
                    健康・スポーツ科学科目の単位数は{{
                        (caculateAquiredCredits('A5')).toPrecision(3)
                    }}単位で条件を満たしていません。
                </p>
                <p class="ml-4">(6) (1)～(5)で履修した科目以外から4単位を修得すること</p>
                <p v-if="caculateAquiredCredits('A1') - 10 + caculateAquiredCredits('A41') - 6 + caculateAquiredCredits('A42') - 2 >= 4"
                    class="font-weight-black ml-10 text-green-darken-1">
                    (1)～(5)で履修した科目以外で{{
                        (caculateAquiredCredits('A1') - 10 + caculateAquiredCredits('A41') - 6 +
                            caculateAquiredCredits('A42') -
                            2).toPrecision(3)
                    }}単位を修得しているので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-10 text-red-darken-1">
                    (1)～(5)で履修した科目以外で{{
                        (caculateAquiredCredits('A1') - 10 + caculateAquiredCredits('A41') - 6 +
                            caculateAquiredCredits('A42') -
                            2).toPrecision(3)
                    }}単位しか修得できていないので条件を満たしていません。
                </p>
                <p class="ml-4">(7) 基礎教育科目 30単位（必修20単位 選択10単位）</p>
                <p v-if="caculateAquiredCredits('B', true, false) == 20 && caculateAquiredCredits('B', false, true) >= 10"
                    class="font-weight-black ml-10 text-green-darken-1">
                    基礎教育科目は必修を{{ (caculateAquiredCredits('B', true, false)).toPrecision(3) }}単位、選択を{{
                        (caculateAquiredCredits('B', false, true)).toPrecision(3)
                    }}単位を修得しているので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-10 text-red-darken-1">
                    基礎教育科目は必修を{{ (caculateAquiredCredits('B', true, false)).toPrecision(3) }}単位、選択を{{
                        (caculateAquiredCredits('B', false, true)).toPrecision(3)
                    }}単位しか修得できていないので条件を満たしていません。
                </p>
                <p class="mt-2">2．専門科目</p>
                <p>上記1(7)に加え、次の(1)～(3)のとおり履修し、合計102単位以上修得すること</p>
                <p>(1) 学部共通科目 2単位以上（必修2単位）</p>
                <p v-if="caculateAquiredCredits('C1', true, false) == 2"
                    class="font-weight-black ml-6 text-green-darken-1">
                    学部共通科目は必修を{{ (caculateAquiredCredits('C1', true, false)).toPrecision(3) }}単位、選択を{{
                        (caculateAquiredCredits('C1', false, true)).toPrecision(3)
                    }}単位を修得しているので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-6 text-red-darken-1">
                    学部共通科目は必修を{{ (caculateAquiredCredits('C1', true, false)).toPrecision(3) }}単位、選択を{{
                        (caculateAquiredCredits('C1', false, true)).toPrecision(3)
                    }}単位しか修得できていないので条件を満たしていません。
                </p>
                <p>(2) 専門導入科目 4単位（必修4単位）</p>
                <p v-if="caculateAquiredCredits('C2', true, false) == 4"
                    class="font-weight-black ml-6 text-green-darken-1">
                    専門導入科目は必修を{{ (caculateAquiredCredits('C2', true, false)).toPrecision(3) }}単位を修得しているので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-6 text-red-darken-1">
                    専門導入科目は必修を{{ (caculateAquiredCredits('C2', true, false)).toPrecision(3) }}単位しか修得できていないので条件を満たしていません。
                </p>
                <p>(3) 学科専門科目 18単位以上（必修18単位）</p>
                <p v-if="caculateAquiredCredits('C3', true, false) == 18"
                    class="font-weight-black ml-6 text-green-darken-1">
                    学科専門科目は必修を{{ (caculateAquiredCredits('C3', true, false)).toPrecision(3) }}単位、選択を{{
                        (caculateAquiredCredits('C3', false, true)).toPrecision(3)
                    }}単位を修得しているので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-6 text-red-darken-1">
                    学科専門科目は必修を{{ (caculateAquiredCredits('C3', true, false)).toPrecision(3) }}単位、選択を{{
                        (caculateAquiredCredits('C3', false, true)).toPrecision(3)
                    }}単位しか修得できていないので条件を満たしていません。
                </p>
                <p>(1)～(3)で72単位以上を修得すること</p>
                <p v-if="caculateAquiredCredits('C1') + caculateAquiredCredits('C2') + caculateAquiredCredits('C3') >= 72"
                    class="font-weight-black ml-6 text-green-darken-1">
                    (1)～(3)の合計が{{
                        (caculateAquiredCredits('C1') + caculateAquiredCredits('C2') +
                            caculateAquiredCredits('C3')).toPrecision(3)
                    }}単位なので条件を満たしています。
                </p>
                <p v-else class="font-weight-black ml-6 text-red-darken-1">
                    (1)～(3)の合計が{{
                        (caculateAquiredCredits('C1') + caculateAquiredCredits('C2') +
                            caculateAquiredCredits('C3')).toPrecision(3)
                    }}単位しか修得できていないので条件を満たしていません。
                </p>
            </v-card>
        </div>
    </div>
</template>


<script setup lang="ts">
import { onMounted, ref } from 'vue';

type SubjectCategory = {
    id: string;
    name: string;
    level: number;
    parent?: string;
};

type SubjectAllocation = {
    id: string;
    name: string;
    credits: number;
    allocated_grade: number;
    evaluation_category: string;
    is_required: boolean;
    level: number;
};

type ListItem = (SubjectCategory & {
    type: 'subheader';
}) | (SubjectAllocation & {
    type: 'item';
    value: string;
    is_aquired: boolean;
});

const items = ref<ListItem[]>([])
const subject_evaluation_category = ref<SubjectCategory[]>([])
const subject_allocation = ref<SubjectAllocation[]>([])
const aquired_subjects = ref<string[]>([])

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
function isAquired(id: string, aqs: string[] = []): boolean {
    if (aqs.length == 0) {
        aqs = getAquiredSubjects();
    }
    for (const aquired_subject of aqs) {
        if (aquired_subject === id) {
            return true
        }
    }
    return false
}

/**
 * 配列で与えられた科目IDのうち修得済みのものの単位数を計算して返却する
 * @param {string[]} ids - 科目IDの配列
 * @returns {number} - 修得済みの単位数
 */
function getAquiredCreditsFromSubjects(ids: string[]): number {
    let sum: number = 0;
    console.log("getAquiredCreditsFromSubjects called (ids:" + ids);
    console.log("(aquired:" + aquired_subjects.value);
    for (const item of items.value) {
        if (item.type == "item") {
            for (const id of ids) {
                if (item.id == id) {
                    sum += item.credits;
                }
            }
        }
    }
    return sum;
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
            items.value.push({ type: 'subheader', id: item.id, name: item.name, level: item.level })
            for (const sa_item of subject_allocation.value) {
                if (sa_item.evaluation_category === item.id) {
                    items.value.push({
                        type: "item",
                        id: sa_item.id,
                        name: (sa_item.is_required ? '[必修] ' : '') + sa_item.name + ' (' + sa_item.credits.toPrecision(3) + ' 単位, ' + sa_item.allocated_grade + '年次配当)',
                        credits: sa_item.credits,
                        allocated_grade: sa_item.allocated_grade,
                        evaluation_category: sa_item.evaluation_category,
                        is_required: sa_item.is_required,
                        is_aquired: isAquired(sa_item.id, aquired_subjects.value),
                        level: sa_item.level,
                        value: sa_item.id,
                    });
                }
            }
        }
    }
});

/**
 * 指定した科目カテゴリ以下に属する修得済みの科目の合計単位数を数える
 * @param {string} id - 科目カテゴリのID
 * @returns {number} - 修得済みの科目の合計単位数
 */
function caculateAquiredCredits(id: string, only_required: boolean = false, only_not_required: boolean = false) {
    let sum: number = 0;
    let target_subject_category_ids = getAllSubjectCategoryIds(id);
    for (const item of subject_allocation.value) {
        if (target_subject_category_ids.includes(item.evaluation_category) && isAquired(item.id, [])) {
            // 必修のみで計算する場合は必修以外を除外
            if (only_required && !item.is_required) {
                continue;
            }
            if (only_not_required && item.is_required) {
                continue;
            }
            sum += item.credits ?? 0;
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
    for (const item of subject_evaluation_category.value) {
        // idが自分(親)かどうか確かめることにより子を取得する
        if (item.parent === id) {
            target_subject_category_ids.push(item.id);
            target_subject_category_ids.push(...getAllSubjectCategoryIds(item.id));
        }
    }
    return target_subject_category_ids;
}

/**
 * 履修済みかどうかの状態を変更するメソッド
 * @param {Object} item - 変更対象のアイテムオブジェクト
 */
function changeAquired(item: ListItem & { type: 'item' }) {
    console.log('アイテム:', item);

    // 該当するアイテムの is_aquired プロパティを更新
    item.is_aquired = !item.is_aquired;

    // 必要であれば、ここでデータの永続化（APIコールなど）を行う
    // 例: saveItemStatus(item.value, newValue);
}
</script>