<template>
  <div class="stage">
    <div class="tables">
      <div class="title">映射表</div>
      <div v-if="isEditingMap">
        <textarea v-model="editingMapString"></textarea>
      </div>
      <table v-else class="mappings">
        <tr v-for="item in mapTable" :key="item[0]">
          <td class="from">{{item[0]}}</td>
          <td class="sep"> &lt;=&gt; </td>
          <td class="to">{{item[1]}}</td>
        </tr>
      </table>
      <div class="buttons">
        <template v-if="isEditingMap">
          <button class="primary" @click="submitMappings">确认修改</button>
          <button class="danger" @click="isEditingMap = false">取消修改</button>
        </template>
        <button v-else class="primary" @click="changeMappings">修改映射表</button>
      </div>
    </div>
    <div class="input">
      <div class="title">输入</div>
      <div class="input-content">
        <div class="guide">请在下方粘贴需要翻译的多肽序列</div>
        <div class="form">
          <textarea v-model="inputString" class="text"></textarea>
        </div>
      </div>
    </div>
    <div class="output">
      <div class="title">输出</div>
      <div class="output-content">
        <div class="guide">翻译结果</div>
        <textarea readonly :value="outputString" class="result" tabindex="1"></textarea>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { defaultMapString } from './config'

let mapString = $ref(defaultMapString)
let mapTable = $computed(() => {
  return mapString.split('\n').map(item => item.split(/\s+/))
})
let editingMapString = $ref('')
let isEditingMap = $ref(false)
let mappings = $computed(() => {
  return mapTable.reduce((obj, cur) => {
    const [value, key] = cur
    obj[key] = value
    return obj
  }, {} as Record<string, string>)
})
console.log(mappings)
let inputString = $ref('')
let outputString = $computed(() => {
  return inputString.split('\n').map(line => {
    if (/^[A-Z]+$/.test(line)) {
      return line.split('').map(char => mappings[char]).join(',')
    }
    return line
  }).join('\n')
})

const changeMappings = () => {
  editingMapString = mapString
  isEditingMap = true
}

const submitMappings = () => {
  mapString = editingMapString
  isEditingMap = false
}



</script>


<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>


<style scoped lang="less">
.stage {
  width: 100vw;
  height: 100vh;
  display: flex;
}

.tables, .input, .output {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.title {
  height: 40px;
  border-bottom: 1px solid #aaa;
  line-height: 40px;
  font-weight: bold;
  font-size: 18px;
}

.tables {
  width: 300px;
  .buttons {
    width: 100%;
    text-align: center;
  }
  button {
    margin: 4px;
  }
  textarea {
    height: 80vh;
  }
}

.input, .output {
  flex: 1;
  border-left: 1px solid #eee;
  overflow: hidden;
}

.mappings {
  margin: 20px auto;
  border-collapse: collapse;
  tr {
    border-bottom: 1px solid #aaa;
  }
  td {
    padding: 4px;
    font-weight: bold;
    &.sep {
      color: #ccc;
    }
  }
}

.input {
  /* background-color: red; */
}

.input-content {
  height: 100%;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  padding: 20px;
  .guide {
    height: 30px;
    text-align: left;
  }
  .form {
    flex: 1;
  }
  .text {
    width: 100%;
    height: 100%;
    font-size: 16px;
    border: 1px solid #ccc;
    outline: none;
    resize: none;
    padding: 12px;
    &:focus {
      border-color: #8385f2;
    }
  }
}

.output-content {
  height: 100%;
  padding: 20px;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  .guide {
    height: 30px;
    text-align: left;
  }
  .result {
    flex: 1;
    border: 1px solid #eee;
    background: #fefefe;
    overflow: auto;
    text-align: left;
    padding: 10px;
    resize: none;
    width: 100%;
  }
}

</style>