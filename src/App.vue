<script setup lang="ts">
import { copyFile, createDir, BaseDirectory, exists, readBinaryFile, readDir, FileEntry, readTextFile, removeDir, renameFile, removeFile, writeBinaryFile, writeTextFile } from '@tauri-apps/api/fs'; // 导入 copyFile
import { message } from '@tauri-apps/api/dialog';

import { ref } from 'vue';

// 复制文件
async function copy_file() {
  await copyFile('test/app.py', 'test/app-1.py', { dir: BaseDirectory.Home }); // 基路径为 Home，如: /home/username/
}

const test_dir = ref("");
// 创建文件夹
async function createDirTest() {
  await createDir(test_dir.value, { dir: BaseDirectory.Home, recursive: true });
}

// 判断文件 or 目录是否存在
async function isExist() {
  let is_exist = await exists(test_path.value, { dir: BaseDirectory.Home });
  let msg: string;

  if (is_exist) {
    msg = "文件&路径存在";
  } else {
    msg = "文件&路径不存在";
  }
  await message(msg);
}

// 读取 Binary 文件
async function testReadBinaryFile() {
  let binFile = await readBinaryFile("test/test.md", { dir: BaseDirectory.Home });

  console.log(binFile);
}

// 读取 Dir
async function testReadDir() {
  let filelist = await readDir("test/help", { dir: BaseDirectory.Home, recursive: true });
  processEntries(filelist);
}

// 处理 entries
function processEntries(entries: FileEntry[]) {
  for (const entry of entries) {
    console.log(`Entry: ${entry.path}`);

    if (entry.children) {
      processEntries(entry.children);
    }
  }
}

const test_txt = ref("");
// 读取文本文件
async function testReadTextFile() {
  let content = await readTextFile("test/a.txt", {dir: BaseDirectory.Home});

  test_txt.value = content;

}

const remove_dir = ref("");
// 删除目录
async function testRemoveDir() {
  await removeDir(remove_dir.value, {dir: BaseDirectory.Home});
}

// 重命名
async function testRenameFile() {
  await renameFile("test/JJJJ", "test/LLLL", {dir: BaseDirectory.Home});
}

const remove_file = ref("");
// 删除文件
async function testRemoveFile() {
  await removeFile(remove_file.value, {dir: BaseDirectory.Home});
}

// 写入二进制文件
async function testWriteBinaryFile() {
  await writeBinaryFile("test/test.png", new Uint8Array([]), {dir: BaseDirectory.Home});
}

// 写入文本文件
async function testWriteTextFile() {
  await writeTextFile("test/a.txt", a_txt.value, {dir: BaseDirectory.Home});
}

const a_txt = ref("");
const test_path = ref("");

</script>

<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <h1 class="text-center my-4">Welcome to Tauri!</h1>
        <div class="mb-3">
          <button type="button" class="btn btn-primary m-1" @click="copy_file">复制文件</button>
          复制 app.py 为 app-1.py
        </div>

        <div class="mb-3">
          <button type="button" class="btn btn-primary m-1" @click="createDirTest">创建目录</button>
          <input type="text" class="form-control" v-model="test_dir">
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="isExist">测试文件是否存在</button>
          <div>
            <label class="text-primary">请在输入框中输入基于 ~/test/ 路径下的所有路径</label>
            <input type="text" class="form-control" v-model="test_path" placeholder="请输入路径，相对于 ~/test 目录">
          </div>
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="testReadBinaryFile">读取二进制文件</button>
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="testReadDir">读取目录</button>
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="testReadTextFile">读取文件内容</button>
          ~/test/a.txt
          <p>{{ test_txt }}</p>
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="testRemoveDir">删除文件夹</button>
          <input type="text" class="form-control" v-model="remove_dir" placeholder="请输入要删除的路径">
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="testRenameFile">重命名</button>
          重命名 JJJJ 为 LLLL
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="testRemoveFile">删除文件</button>
          <input type="text" class="form-control" v-model="remove_file" placeholder="请输入要删除文件的路径">
        </div>

        <div class="mb-4">
          <button type="button" class="btn btn-primary m-1" @click="testWriteBinaryFile">写二进制文件</button>
        </div>

        <div class="mb-3">
          <button type="button" class="btn btn-primary m-1" @click="testWriteTextFile">写入文件</button>
          <label class="form-label">写入路径：~/test/a.txt</label>
          <textarea class="form-control" rows="3" v-model="a_txt"></textarea>
        </div>
      </div>
    </div>
  </div> <!-- container END -->
</template>

<style scoped>
.logo.vite:hover {
  filter: drop-shadow(0 0 2em #747bff);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #249b73);
}
</style>
