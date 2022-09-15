<script setup lang="ts">
import { onMounted, onUnmounted, ref, reactive, computed } from "vue";
const input = ref("");
let dropdown = ref();
let chosen = ref();
let focus = ref(false);
let shortcutObj = reactive(
  Array.from({ length: 10 }, (_, index) => ({
    key: `${index}`,
    value: "",
  }))
);
let shortcutData = computed(() => shortcutObj.filter((item) => item.value));

const shortcutKeys = Object.keys(
  Array.from({ length: 10 }, (_, index) => index)
);
function resetChoose(isplus: boolean, resetNum: number) {
  let newChosen = undefined;
  newChosen = isplus ? Number(chosen.value) + 1 : Number(chosen.value) - 1;

  while (!shortcutObj[newChosen]?.value) {
    isplus ? (newChosen += 1) : (newChosen -= 1);
    if (!shortcutKeys.includes(String(newChosen))) newChosen = resetNum;
  }
  chosen.value = newChosen;
}

//长按计时器
let keydownTimer: null | number = null;

const keydownEventHandler = (e: KeyboardEvent) => {
  let downKey: string = e.key;
  if (
    (e.ctrlKey || e.metaKey) &&
    shortcutKeys.includes(downKey) &&
    focus.value
  ) {
    e.preventDefault();
    if (keydownTimer !== null) {
      clearTimeout(keydownTimer);
      keydownTimer = null;
    }
    const downKeyIndex = Number(downKey);
    if (shortcutObj[downKeyIndex].value) {
      input.value = input.value + shortcutObj[downKeyIndex].value;
    } else {
      shortcutObj[downKeyIndex].value = input.value;
    }
  }
  //长按唤起提示面板
  if (["Meta", "Ctrl"].includes(e.key) && focus.value) {
    keydownTimer = window.setTimeout(() => {
      dropdown.value.handleOpen();
      let nowChosen = shortcutData.value?.[0];
      if (!nowChosen) return;
      chosen.value = nowChosen.key || "";
    }, 1500);
  }
  //上下键切换选择
  if (["ArrowUp", "ArrowDown"].includes(e.key) && focus.value) {
    e.preventDefault();
    switch (e.key) {
      case "ArrowUp":
        resetChoose(false, 9);
        break;
      case "ArrowDown":
        resetChoose(true, 0);
        break;
    }
  }
  //回车键热语拼接
  if (e.key === "Enter" && focus.value) {
    input.value = input.value + shortcutObj[chosen.value].value;
  }
};
const keyupEventHandler = () => {
  if (keydownTimer !== null) {
    clearTimeout(keydownTimer);
    keydownTimer = null;
  }
};
onMounted(() => {
  document.addEventListener("keydown", keydownEventHandler);
  document.addEventListener("keyup", keyupEventHandler);
});
onUnmounted(() => {
  document.removeEventListener("keydown", keydownEventHandler);
  document.removeEventListener("keyup", keyupEventHandler);
  if (keydownTimer !== null) {
    clearTimeout(keydownTimer);
  }
});
</script>

<template>
  <el-space>
    <el-dropdown ref="dropdown">
      <el-button>快捷语</el-button>
      <template #dropdown>
        <el-dropdown-menu>
          <el-empty v-if="!shortcutData.length" description="当前无快捷热语" />
          <el-radio-group v-model="chosen">
            <el-radio-button
              v-for="item in shortcutData"
              :key="item.key + item.value"
              :label="item.key"
              >{{ `${item.key}：${item.value}` }}</el-radio-button
            >
          </el-radio-group>
        </el-dropdown-menu>
      </template>
    </el-dropdown>
    <el-input
      v-model="input"
      @focus="focus = true"
      @blur="
        focus = false;
        dropdown.handleClose();
      "
      placeholder="Please input"
    />
  </el-space>
</template>

<style>
.el-radio-group {
  width: 200px;
}
.el-radio-button__inner {
  border: 0;
  width: 200px;
}
.el-dropdown__popper .el-dropdown-menu {
  padding: 8px;
}
.el-radio-button:first-child .el-radio-button__inner {
  border: 0;
}
</style>
