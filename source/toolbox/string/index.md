---
title: 字符串处理
date: 2023-09-01 22:17:49
aside: true
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/Photo/friends.jpg
---

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <style>
      section {
        position: relative;
      }
      .public_title {
        font-size: 20px;
        font-weight: bold;
        margin-bottom: 12px;
      }
      .input_wrap {
        display: flex;
        justify-content: flex-start;
        align-items: center;
        gap: 12px;
      }
      .button_wrap {
        display: flex;
        justify-content: flex-start;
        flex-wrap: wrap;
        margin-top: 18px;
        gap: 18px;
      }
      .but {
        padding: 10px 20px;
        font-size: 16px;
        background-color: #007bff;
        color: #fff;
        border: none;
        display: flex;
      }
      #textInput {
        width: 400px;
        padding: 10px;
        font-size: 16px;
      }
      #copyCheckbox {
        margin-left: 10px;
      }
      #convertedText {
        font-size: 18px;
        margin-top: 10px;
      }
      #popoverContent {
        position: absolute;
        top: 10px;
        left: 50%;
        background-color: #fff;
        border: 1px solid #ccc;
        padding: 10px;
        box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.3);
      }
      .hidden {
        display: none;
      }
    </style>
  </head>
  <body>
    <section>
      <div class="public_title">英文转换</div>
      <div class="input_wrap">
        <input type="text" id="textInput" placeholder="在此输入英文" />
        <div class="checkbox_wrap">
          <input type="checkbox" id="copyCheckbox" />
          <label for="copyCheckbox">自动复制到剪贴板</label>
        </div>
      </div>
      <div id="popoverContent" class="hidden">This is the Popover content.</div>
      <div class="button_wrap">
        <button class="but upCase">转换为大写</button>
        <button class="but lowerCase">转换为小写</button>
        <button class="but capitalize">首字母大写</button>
        <button class="but underline">下划线拼接</button>
        <button class="but convertHump">空格驼峰互转</button>
        <button class="but empty">清空</button>
      </div>
      <div id="convertedText"></div>
    </section>
    <!-- <script src="/js/public/cache.js" type="module"></script> -->
    <!-- <script src="/js/view/toolbox/string/index.js" type="module"></script> -->
    <script type="module">
      const STORAGE_TYPE = {
        LOCAL_STORAGE: "localStorage",
        SESSION_STORAGE: "sessionStorage",
        COOKIES: "cookies",
      };
      class Cache {
        constructor(type) {
          this.type = type;
        }
        setItem(keyName, value, type) {
          switch (this.setType(type)) {
            case STORAGE_TYPE.LOCAL_STORAGE:
              localStorage.setItem(keyName, value);
              break;
            case STORAGE_TYPE.SESSION_STORAGE:
              sessionStorage.setItem(keyName, value);
              break;
            case STORAGE_TYPE.COOKIES:
              // Implement cookie storage here
              break;
            default:
              sessionStorage.setItem(keyName, value);
              break;
          }
        }
        getItem(keyName, type) {
          switch (this.setType(type)) {
            case STORAGE_TYPE.LOCAL_STORAGE:
              return localStorage.getItem(keyName);
            case STORAGE_TYPE.SESSION_STORAGE:
              return sessionStorage.getItem(keyName);
            case STORAGE_TYPE.COOKIES:
            default:
              return sessionStorage.getItem(keyName);
          }
        }
        removeItem(keyName, type) {
          switch (this.setType(type)) {
            case STORAGE_TYPE.LOCAL_STORAGE:
              localStorage.removeItem(keyName);
              break;
            case STORAGE_TYPE.SESSION_STORAGE:
              sessionStorage.removeItem(keyName);
              break;
            case STORAGE_TYPE.COOKIES:
              // Implement removal of cookie here
              break;
            default:
              sessionStorage.removeItem(keyName);
              break;
          }
        }
        clear(type) {
          switch (this.setType(type)) {
            case STORAGE_TYPE.LOCAL_STORAGE:
              localStorage.clear();
              break;
            case STORAGE_TYPE.SESSION_STORAGE:
              sessionStorage.clear();
              break;
            case STORAGE_TYPE.COOKIES:
              // Implement clearing cookies here
              break;
            default:
              sessionStorage.clear();
              break;
          }
        }
        setType(type) {
          if (!type) {
            return this.type;
          } else {
            return type;
          }
        }
        static instance = null;
        static getInstance(type) {
          if (!this.instance) {
            this.instance = new Cache(type);
          }
          return this.instance;
        }
      }
      const cache = Cache.getInstance(STORAGE_TYPE.LOCAL_STORAGE);
      // 转换字符串用到的工具函数
      // 大写
      const toUpperCase = (str) => {
        return str.replace(/([a-z]+)/g, function (match, p1) {
          return p1.toUpperCase();
        });
      };
      // 小写
      const toLowerCase = (str) => {
        return str.replace(/([A-Z]+)/g, function (match, p1) {
          return p1.toLowerCase();
        });
      };
      // 首字母大写
      const capitalizeWords = (text, delimiter = " ") => {
        return text
          .split(delimiter)
          .map((word) => word.charAt(0).toUpperCase() + word.slice(1))
          .join(delimiter);
      };
      // 加分割符，默认下划线
      const toUnderline = (text, separator = " ", delimiter = "_") => {
        return text
          .split(separator)
          .map((word) => word.toLowerCase())
          .join(delimiter);
      };
      // 是否由下划线拼接
      const isUnderScored = (str) => {
        return /^([a-zA-Z]+_)*[a-zA-Z]+$/.test(str);
      };
      // 转驼峰
      const camelize = (str) => {
        // (?:)非捕获组，^\w匹配开头的单个字母、[A-Z]单个大写字母、\b\w一个单词的开头、\s+个或多个连续的空格或制表符
        return str.replace(/(?:^\w|[A-Z]|\b\w|\s+)/g, function (match, index) {
          // (?:) 表示非捕获组，即在正则表达式的匹配过程中不用作为捕获组返回，也就是没有p1,p2
          // 当前匹配的字符串是 " "，则直接返回一个空字符串
          if (+match === 0) return "";
          // index是表示当前匹配子字符串在原字符串中的起始位置
          return index == 0 ? match.toLowerCase() : match.toUpperCase();
        });
      };
      // 转驼峰
      const toCamelCase = (str) => {
        const result = camelize(str);
        return result.charAt(0).toLowerCase() + result.slice(1);
      };
      // 不是驼峰转驼峰，是驼峰转成空格分隔
      const toConvertHump = (text) => {
        const isCamelCase = /^[a-z][A-Za-z0-9]*$/.test(text);
        if (isCamelCase) {
          // 是驼峰转换成空格拼接
          return text.replace(/[A-Z]/g, (match) => ` ${match.toLowerCase()}`);
        } else {
          // 不是驼峰转换成驼峰
          // return text.replace(/(?:^\w|[A-Z])/g, (match, index) =>
          //   index === 0 ? match.toLowerCase() : ` ${match.toLowerCase()}`
          // );
          return toCamelCase(text);
        }
      };
      // 配置提示信息
      const PROMPT_MESSAGE = [
        {
          className: "underline",
          message: "空格分隔的单词转换成【小写单词】并用'_'拼接起来，可互转",
        },
        {
          className: "convertHump",
          message: "将驼峰格式的单词与通过空格分隔的单词进行互转",
        },
        {
          className: "capitalize",
          message: "支持对空格,'-','_'进行分割的单词转换成首字母大写形式",
        },
      ];
      const listeners = [
        {
          className: "upCase",
          listener: convertText,
        },
        {
          className: "lowerCase",
          listener: lowerCase,
        },
        {
          className: "capitalize",
          listener: capitalized,
        },
        {
          className: "underline",
          listener: underline,
        },
        {
          className: "convertHump",
          listener: convertHump,
        },
        {
          className: "empty",
          listener: empty,
        },
        {
          className: "checkbox_wrap",
          listener: clickCheckbox,
        },
      ];
      const DELIMITERS = [" ", "_", "-"];
      // 获取元素
      const popoverContent = document.getElementById("popoverContent"); //悬浮提示
      const checkboxEl = document.getElementById("copyCheckbox"); // 复选框
      checkboxEl.checked = cache.getItem("isCopy") || false;
      const inputEl = document.getElementById("textInput"); //input输入框
      const convertedTextElement = document.getElementById("convertedText"); //底部提示
      // 绑定事件
      document.addEventListener("DOMContentLoaded", function () {
        // 鼠标移动世界
        PROMPT_MESSAGE.forEach((item) => {
          const popoverButton = document.getElementsByClassName(
            item.className
          )[0];
          popoverButton.addEventListener("mouseover", function () {
            popoverContent.innerText = item.message;
            popoverContent.classList.remove("hidden");
          });
          popoverButton.addEventListener("mouseout", function () {
            popoverContent.classList.add("hidden");
          });
        });
        // 点击事件
        listeners.forEach((item) => {
          const button = document.getElementsByClassName(item.className)[0];
          button.addEventListener("click", item.listener);
        });
      });
      // 转大写
      function convertText() {
        if (validityText()) {
          // 获取输入框中的文本
          const inputText = inputEl.value;
          // 将文本转换为大写
          const convertedText = inputText.toUpperCase();
          // 复选框是否被选中
          const copyToClipboard = checkboxEl.checked;
          // 更新输入框中的文本为大写形式
          inputEl.value = convertedText;
          // 如果复选框被选中，则复制到剪贴板
          if (copyToClipboard) {
            copyTextToClipboard(convertedText);
            convertedTextElement.textContent = `已转换为大写并已复制到剪贴板：${convertedText}`;
          } else {
            convertedTextElement.textContent = `已转换为大写：${convertedText}`;
          }
        }
      }
      // 将文字复制到剪贴板
      function copyTextToClipboard(text) {
        // 创建临时textarea元素
        const textarea = document.createElement("textarea");
        textarea.value = text;
        // 将textarea添加到DOM中
        document.body.appendChild(textarea);
        // 选择文本内容
        textarea.select();
        textarea.setSelectionRange(0, 99999); // 兼容性处理
        // 尝试复制文本到剪贴板
        document.execCommand("copy");
        // 移除临时元素
        document.body.removeChild(textarea);
      }
      // 效验输入的内容
      function validityText() {
        // 获取输入框中的文本
        const inputText = inputEl.value;
        switch (getTypeOfString(inputText)) {
          case -1:
            return true;
            break;
          case 1:
            convertedTextElement.textContent = `输入的纯数字,靓仔`;
            break;
          case 2:
            return true;
            break;
          case 3:
            convertedTextElement.textContent = `别输入汉字啊,靓仔`;
            break;
        }
      }
      //   判断字符串类型
      function getTypeOfString(inputString) {
        if (inputString.trim() === "") {
          return 0; // 空白字符串
        } else if (!isNaN(inputString)) {
          return 1; // 数字
        } else if (inputString.match(/^[a-zA-Z]+$/)) {
          return 2; // 英文字母
        } else if (inputString.match(/^[\u4e00-\u9fa5]+$/)) {
          return 3; // 汉字
        } else {
          return -1; // 未知类型
        }
      }
      // 转小写
      function lowerCase() {
        if (validityText()) {
          // 获取输入框中的文本
          const inputText = inputEl.value;
          const convertedText = toLowerCase(inputText);
          // 复选框是否被选中
          const copyToClipboard = checkboxEl.checked;
          inputEl.value = convertedText;
          // 如果复选框被选中，则复制到剪贴板
          if (copyToClipboard) {
            copyTextToClipboard(convertedText);
            convertedTextElement.textContent = `已转换为小写并已复制到剪贴板：${convertedText}`;
          } else {
            convertedTextElement.textContent = `已转换为小写：${convertedText}`;
          }
        }
      }
      // 首字母大写
      function capitalized() {
        console.log("点击了首字母大写");
        if (validityText()) {
          // 多个单词
          for (let i = 0; i < DELIMITERS.length; i++) {
            const delimiter = DELIMITERS[i];
            if (inputEl.value.indexOf(delimiter) !== -1) {
              // 分隔符采用第一个检测到的
              const convertedText = capitalizeWords(inputEl.value, delimiter);
              const copyToClipboard = checkboxEl.checked;
              inputEl.value = convertedText;
              if (copyToClipboard) {
                copyTextToClipboard(convertedText);
                convertedTextElement.textContent = `已转换为首字母大写并已复制到剪贴板：${convertedText}`;
              } else {
                convertedTextElement.textContent = `已转换为首字母大写：${convertedText}`;
              }
              return;
            }
          }
          // 只有一个单词的情况
          const convertedText = capitalizeWords(inputEl.value);
          const copyToClipboard = checkboxEl.checked;
          inputEl.value = convertedText;
          if (copyToClipboard) {
            copyTextToClipboard(convertedText);
            convertedTextElement.textContent = `已转换为首字母大写并已复制到剪贴板：${convertedText}`;
          } else {
            convertedTextElement.textContent = `已转换为首字母大写：${convertedText}`;
          }
        }
      }
      // 驼峰互转
      function convertHump() {
        console.log("点击了下划线拼接");
        if (validityText()) {
          const convertedText = toConvertHump(inputEl.value);
          const copyToClipboard = checkboxEl.checked;
          inputEl.value = convertedText;
          if (copyToClipboard) {
            copyTextToClipboard(convertedText);
            convertedTextElement.textContent = `已转换并已复制到剪贴板：${convertedText}`;
          } else {
            convertedTextElement.textContent = `已转换：${convertedText}`;
          }
        }
      }
      // 下划线拼接
      function underline() {
        console.log("点击了下划线拼接");
        if (validityText()) {
          let convertedText = null;
          if (isUnderScored(inputEl.value)) {
            // 是由下划线拼接的，改成由空字符串拼接
            convertedText = toUnderline(inputEl.value, "_", " ");
          } else {
            // 不是下划线拼接的，改成下划线拼接
            convertedText = toUnderline(inputEl.value);
          }
          const copyToClipboard = checkboxEl.checked;
          inputEl.value = convertedText;
          if (copyToClipboard) {
            copyTextToClipboard(convertedText);
            convertedTextElement.textContent = `已转换并已复制到剪贴板：${convertedText}`;
          } else {
            convertedTextElement.textContent = `已转换：${convertedText}`;
          }
        }
      }
      // 清空
      function empty() {
        inputEl.value = "";
        convertedTextElement.textContent = "";
      }
      //   点击复选框回调
      function clickCheckbox() {
        console.log("点击了复选框");
        cache.setItem("isCopy", checkboxEl.checked);
      }
    </script>

  </body>
</html>
