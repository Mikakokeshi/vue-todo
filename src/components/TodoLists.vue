<script setup>
import { ref } from 'vue'
import { statuses } from '../const/statuses'
import { categories } from '../const/categories'
import { priorities } from '../const/priorities'

const items = ref(JSON.parse(localStorage.getItem('items')) || [])

let inputContent = ref()
let inputLimit = ref()
let inputState = ref()
let inputCategory = ref()
let inputPriority = ref()
let inputMemo = ref()
let deleteItemId = ref()
let isEditting = ref(false)
let isShowModal = ref(false)
let isToday = ref()
let isSortAscendingStatus = ref(false)
let isSortAscendingLimit = ref(false)
let isSortAscendingCategory = ref(false)
let isSortAscendingPriority = ref(false)
let isSortLimit = ref(false)
let isSortState = ref(false)
let isSortCategory = ref(false)
let isSortPriority = ref(false)

function onEdit(id) {
  console.log(id)

  console.log(isEditting.value)
  if (isEditting.value) {
    return alert('編集中のタスクを完了してください')
  } else {
    isEditting.value = true
    console.log('編集中なし')
    items.value[id].onEdit = true
    inputContent.value = items.value[id].content
    inputLimit.value = items.value[id].limit
    inputState.value = items.value[id].state
    inputCategory.value = items.value[id].category
    inputPriority.value = items.value[id].priority
    inputMemo.value = items.value[id].memo

    // console.log(inputContent.value, inputLimit.value, inputState.value)
    return
  }
}

function onUpdate(id) {
  console.log(id)
  const newItem = {
    id: id,
    content: inputContent.value,
    limit: inputLimit.value,
    state: inputState.value,
    category: inputCategory.value,
    priority: inputPriority.value,
    memo: inputMemo.value,
    onEdit: false
  }
  console.log(newItem)
  items.value.splice(id, 1, newItem)
  isEditting.value = false

  localStorage.setItem('items', JSON.stringify(items.value))
}

function showDeleteModal(id, content) {
  isShowModal.value = true
  deleteItemId.value = id
  inputContent.value = content
}

function closeDeleteModal() {
  isShowModal.value = false
}
function onDelete(id) {
  const deleteItemId = id
  items.value.splice(deleteItemId, 1)
  items.value = items.value.map((item, index) => ({
    id: index,
    content: item.content,
    limit: item.limit,
    state: item.state,
    category: item.category,
    pritority: item.pritority,
    memo: item.memo,
    onEdit: item.onEdit
  }))
  localStorage.setItem('items', JSON.stringify(items.value))

  isShowModal.value = false
}

function onMemoChange(event) {
  inputMemo.value = event.target.value
}

// 今日かどうか判定
const today = new Date()
  .toLocaleDateString('ja-JP', {
    year: 'numeric',
    month: '2-digit',
    day: '2-digit'
  })
  .replaceAll('/', '-')

function ifToday() {
  isToday.value = items.value.find((item) => item.limit === today)

  if ((isToday.value = today)) {
    return isToday.value == true
  }
}
ifToday()

// ソート
function sortState() {
  items.value.sort((a, b) => {
    console.log(items.value)

    if (isSortState.value) {
      if (isSortAscendingStatus.value) {
        console.log('ステータス　昇順')
        return a.state.value > b.state.value ? 1 : -1
      } else {
        console.log('ステータス　降順')

        return a.state.value < b.state.value ? 1 : -1
      }
    } else if (isSortLimit.value) {
      if (isSortAscendingLimit.value) {
        console.log('期限　昇順')
        return a.limit > b.limit ? 1 : -1
      } else {
        console.log('期限 降順')

        return a.limit < b.limit ? 1 : -1
      }
    } else if (isSortCategory.value) {
      if (isSortAscendingCategory.value) {
        console.log('カテゴリ　昇順')
        return a.category > b.category ? 1 : -1
      } else {
        console.log('カテゴリ 降順')
        return a.category < b.category ? 1 : -1
      }
    } else if (isSortPriority.value) {
      if (isSortAscendingPriority.value) {
        console.log('プライオリティ　昇順')
        return a.priority > b.priority ? 1 : -1
      } else {
        console.log('プライオリティ 降順')
        return a.priority < b.priority ? 1 : -1
      }
    }
  })
  const updatedItem = items.value.map((item, index) => {
    item.id = index // Adjust based on your desired ID starting value
    return item
  })
  localStorage.setItem('items', JSON.stringify(updatedItem))
}

function toggleSortStatus() {
  isSortLimit.value = false
  isSortCategory.value = false
  isSortState.value = true
  isSortAscendingStatus.value = !isSortAscendingStatus.value // Toggle sorting direction
  sortState()
}

function toggleSortLimit() {
  isSortState.value = false
  isSortCategory.value = false
  isSortPriority.value = false
  isSortLimit.value = true
  isSortAscendingLimit.value = !isSortAscendingLimit.value // Toggle sorting direction
  sortState()
}
function toggleSortCategory() {
  isSortState.value = false
  isSortLimit.value = false
  isSortPriority.value = false
  isSortCategory.value = true
  isSortAscendingCategory.value = !isSortAscendingCategory.value // Toggle sorting direction
  sortState()
}
function toggleSortPriority() {
  isSortState.value = false
  isSortLimit.value = false
  isSortCategory.value = false
  isSortPriority.value = true
  isSortAscendingPriority.value = !isSortAscendingPriority.value // Toggle sorting direction
  sortState()
}

console.log(items.value)
console.log(items.value.slice(-5).reverse())
</script>

<template>
  <div v-if="items.length" class="table_wrap">
    <table>
      <thead>
        <tr class="table_head">
          <th></th>
          <th>タスク</th>
          <th class="limit">
            <div class="sort_wrap">
              期限
              <div class="sort">
                <button v-if="!isSortAscendingLimit" @click="toggleSortLimit()">
                  <v-icon>mdi-sort-ascending</v-icon>
                </button>
                <button v-else @click="toggleSortLimit()">
                  <v-icon>mdi-sort-descending</v-icon>
                </button>
              </div>
            </div>
          </th>
          <th class="status">
            <div class="sort_wrap">
              ステータス
              <div class="sort">
                <button v-if="!isSortAscendingStatus" @click="toggleSortStatus()">
                  <v-icon>mdi-sort-ascending</v-icon>
                </button>
                <button v-else @click="toggleSortStatus()">
                  <v-icon>mdi-sort-descending</v-icon>
                </button>
              </div>
            </div>
          </th>
          <th class="category">
            <div class="sort_wrap">
              カテゴリ
              <div class="sort">
                <button v-if="!isSortAscendingCategory" @click="toggleSortCategory()">
                  <v-icon>mdi-sort-ascending</v-icon>
                </button>
                <button v-else @click="toggleSortCategory()">
                  <v-icon>mdi-sort-descending</v-icon>
                </button>
              </div>
            </div>
          </th>
          <th class="priority">
            <div class="sort_wrap">
              重要度
              <div class="sort">
                <button v-if="!isSortAscendingPriority" @click="toggleSortPriority()">
                  <v-icon>mdi-sort-ascending</v-icon>
                </button>
                <button v-else @click="toggleSortPriority()">
                  <v-icon>mdi-sort-descending</v-icon>
                </button>
              </div>
            </div>
          </th>
          <th class="memo">メモ</th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in items" :key="item.id">
          <th>{{ item.id + 1 }}</th>

          <td>
            <span v-if="!item.onEdit" @click="onEdit(item.id)">{{ item.content }}</span>
            <input v-model="inputContent" v-else type="text" />
          </td>
          <td class="limit-value">
            <span
              :class="{ red: item.limit === today }"
              v-if="!item.onEdit"
              @click="onEdit(item.id)"
              >{{ item.limit }}</span
            >
            <!-- :class='{"クラス名", 条件} -->
            <div v-else>
              <VueDatePicker
                showIcon
                v-model="inputLimit"
                format="yyyy/MM/dd"
                locale="ja"
                model-type="yyyy-MM-dd"
                week-start="0"
                :enable-time-picker="false"
                :day-class="getDayClass"
                :minDate="new Date()"
              />
            </div>
          </td>
          <td>
            <span v-if="!item.onEdit" @click="onEdit(item.id)">{{ item.state.value }}</span>
            <select v-else v-model="inputState">
              <option
                v-for="state in statuses"
                :key="state.id"
                :value="state"
                :selected="state.id == item.state.id"
              >
                {{ state.value }}
              </option>
            </select>
          </td>
          <td>
            <span v-if="!item.onEdit" @click="onEdit(item.id)">{{ item.category }}</span>
            <select v-else v-model="inputCategory">
              <option
                v-for="category in categories"
                :key="category.id"
                :value="category.value"
                :selected="category.id == item.category.id"
              >
                {{ category.value }}
              </option>
            </select>
          </td>
          <td>
            <span v-if="!item.onEdit" @click="onEdit(item.id)">{{ item.priority }}</span>
            <select v-else v-model="inputPriority">
              <option
                v-for="priority in priorities"
                :key="priority.id"
                :value="priority.value"
                :selected="priority.id == item.priority.id"
              >
                {{ priority.value }}
              </option>
            </select>
          </td>
          <td>
            <span v-if="!item.onEdit" @click="onEdit(item.id)">{{ item.memo }}</span>
            <input v-else type="text" v-model="inputMemo" @change="onMemoChange($event)" />
          </td>
          <td class="button">
            <button v-if="!item.onEdit" @click="onEdit(item.id)">
              <v-icon color="blue">mdi-pencil</v-icon>
            </button>
            <button v-else @click="onUpdate(item.id)">
              <v-icon color="green">mdi-check</v-icon>
            </button>
          </td>
          <td class="button">
            <button @click="showDeleteModal(item.id, item.content)">
              <v-icon color="red">mdi-delete</v-icon>
            </button>
          </td>
          <!-- 削除モーダル -->
          <td v-if="isShowModal" class="modal">
            <div class="modal-content">
              <p>{{ ` タスク: 「${inputContent}」 ` }}本当に削除しますか？</p>
              <v-btn class="modal-button" @click="onDelete(deleteItemId)"> はい </v-btn>
              <v-btn class="modal-button" @click="closeDeleteModal">いいえ</v-btn>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  right: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.2);
  justify-content: center;
  display: flex;
  align-items: center;
}
.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
}
.modal-content p {
  padding-bottom: 10px;
}
.modal-button {
  margin: 5px;
}

.table_wrap table {
  border-collapse: collapse;
  height: 100%;
  font-weight: bold;
  text-align: center;
}
table span {
  width: 100%;
  padding: 5px;
  display: block;
  font-size: 12px;
}
table input,
table select {
  font-size: 12px;
  text-align: center;
}

table .table_head .sort_wrap {
  display: flex;
  justify-content: center;
  font-weight: bold;
}
.status {
  width: 150px;
}
.category {
  width: 160px;
}
.priority {
  width: 130px;
}
.sort {
  padding-left: 5px;
}

table .button {
  width: 30px;
}
table .memo {
  width: 180px;
}

table .button button {
  font-size: 11px;
  padding: 4px;
}

input,
select {
  width: 100%;
  padding: 3px;
  height: 30px;
  border: 1px solid rgb(118, 118, 118);
  border-radius: 5px;
}

.limit .sort_wrap {
  width: 135px;
}

.red {
  color: red;
  font-weight: bold;
  border: 1px solid red;
  border-radius: 70px;
  justify-items: center;
  line-height: 1;
  margin: auto;
}

.limit-value .red {
  width: 120px;
  text-align: center;
}
</style>

<style>
table th {
  display: table-cell;
  font-size: 12px;
  font-weight: bold;
  padding: 0 4px;
  height: 52px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}
table td {
  display: table-cell;
  height: 52px;
  width: 120px;
  padding: 0 4px;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.table_wrap .dp__pointer {
  font-size: 12px !important;
  border: 1px solid rgb(118, 118, 118) !important;
  height: 30px !important;
}
.dp__menu {
  position: relative;
  z-index: 2;
}
@media screen and (max-width: 678px) {
  .table_wrap,
  .v-table {
    overflow-x: scroll;
    overflow-y: visible;
  }
  .table_wrap table {
    border-collapse: collapse;
    min-width: 940px;
  }
  .limit {
    width: 135px;
  }
}
</style>
