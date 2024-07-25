---
title: 批量上传方案
date: 2024-07-22 13:17:00
tags: vue
categories: vue
description: 本篇文章让我们一起来看看如何利用element-ui的上传组件实现批量上传。阅读时长：6min。
cover: https://cdn.jsdelivr.net/gh/pengpen1/blog-images/books-2596809_1280.jpg
top_img: https://jsd.012700.xyz/gh/jerryc127/CDN@latest/cover/default_bg.png
---
**概要：**在这篇记录中，我们将学习如何利用element-ui的上传组件实现批量上传。

### 思路1：多少文件调用多少次接口

这个使用`element-ui`自动的功能就能实现，但是缺点是批量上传多少个文件就会同时发起多少个请求，而且浏览器对并发请求是有一定的限制，一般是不能超过6个，就算浏览器不限制，我们也不能确定后端能否处理好这么多并发请求，所以这个方案不是最优解。

```vue
<el-upload
  class="upload-demo"
  drag
  action="https://jsonplaceholder.typicode.com/posts/"
  multiple>
  <i class="el-icon-upload"></i>
  <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
  <div class="el-upload__tip" slot="tip">只能上传jpg/png文件，且不超过500kb</div>
</el-upload>
```



### 思路2：维护数组，一次调用

首先我们得知道一些关于el-upload的基本的知识：

1. 如果批量选中文件，默认情况下每个文件会依次经历`on-change`以及`before-upload`再次`on-change`
2. element内部维护的fileList，是不会重置的
3. 我们需要手动在



我们用一个数组来保存需要上传的文件，然后用户在点击上传按钮后，再将文件通过一个接口传递给后端。这个方案有个不好的地方就是需要加个按钮。

```js
  const uploadRef = ref(null);
  const uploadFileList = ref([]);
  const uploadProps = reactive({
    drag: true,
    uploadDisabled: true, //上传按钮
    "auto-upload": false,
    accept: ".docx,.pdf,.ppt,.pptx,.xlsx,.xls,.jpg,.png,.bmp=,.jpeg",
    multiple: true,
    // action: "/fontGateway/" + REQUEST_URL.WATER + "v1/WatermarkAddTask", // 单个自动上传可用这个
    action: "",
    "before-upload": (file) => {
      console.log(file);
      // 上传前的操作
      return true;
    },
    "on-change": (file, fileList) => {
      console.log(file, fileList);
      uploadFileList.value = fileList;
    },
    "before-remove": (file, fileList) => {
      console.log(file, fileList);
      uploadFileList.value = fileList;
    },
    "on-success": (response, file, fileList) => {
      if (response.code === 200) {
        searchHandle();
      } else {
        FMessage.error(response.msg);
      }
      uploadRef.value.clearFiles();
      uploadFileList.value = [];
    },
    "on-error": (erron, file, fileList) => {
      FMessage.error(erron);
      uploadRef.value.clearFiles();
      uploadFileList.value = [];
    },
  });
  const uploadDisabled = computed(() => uploadFileList.value.length === 0);
  const clickUploadHandler = async () => {
    console.log("clickUploadHandler", uploadFileList.value);
    const result = await uploadData(uploadFileList.value);
    if (result) {
      searchHandle();
    }
    uploadRef.value.clearFiles();
  };
```

这里维护的数组其实就是`element-ui`内部的fileList，用户点击上传按钮就会调用`clickUploadHandler`的函数，并将带有所有需要上传文件的数组传递给接口。

通过创建formData，并将所以文件叠加在`file`字段上，传递给后端，实现一个接口上传多个文件的功能。

```js
  const uploadData = async (fileList) => {
    const formData = new FormData();
    fileList.forEach((file) => {
      formData.append("file", file.raw);
    });

    const options = {
      method: "post",
      url: REQUEST_URL.WATER + "v1/WatermarkAddTask",
      headers: {
        "Content-Type": "multipart/form-data",
      },
      data: formData,
    };
    return axios.request(options).then((res) => {
      if (res.data.code === 200 || res.data.success === true) {
        FMessage.success(res.data.msg);
        return true;
      }
      FMessage.error(res.data.msg);
      return false;
    });
  };
```

![](https://cdn.jsdelivr.net/gh/pengpen1/blog-images/81855918194d8a26502c1ecca4650de.png)

上面是后端代码的截图，他们获取的就算formdata中的Files，文件集合。



### 思路3：维护数组，延迟调用

这里通过维护一个上传数组，根据顺序依次延迟2秒进行上传，成功上传后清空该数组。

```js
  // 上传文件相关
  const uploadRef = ref(null);
  const uploadFileIndex = ref(0); // 在采用方案1时需要
  const uploadFileList = ref([]); // 方案2，1都需要
  const uploadProps = reactive({
    drag: true,
    uploadDisabled: true, //上传按钮
    "auto-upload": true,
    accept: ".docx,.pdf,.ppt,.pptx,.xlsx,.xls,.jpg,.png,.bmp=,.jpeg",
    multiple: true,
    "show-file-list": false,
    action: "/fontGateway/" + REQUEST_URL.WATER + "v1/WatermarkAddTask", // 方案1：采用自动上传，可用这个
    // action: "", // 方案2：手动维护上传数组，通过按钮进行上传操作
    "before-upload": async (file) => {
      console.log("2222222", uploadFileList.value);
      //  方案2：手动维护上传数组
      // return true;

      // 方案1
      const index =
        uploadFileList.value.findIndex((item) => {
          return item.uid === file.uid;
        }) + 1;
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(file);
          uploadFileIndex.value = index;
          return resolve();
        }, index * 2000);
      });
    },
    "on-change": (file, fileList) => {
      console.log(file, fileList);
      // 方案2：手动维护上传数组，通过按钮进行上传操作
      // uploadFileList.value = fileList;

      // 方案1
      uploadFileList.value.splice(0);
      uploadFileList.value.push(...fileList);
    },
    "before-remove": (file, fileList) => {
      console.log(file, fileList);
      // 方案2：手动维护上传数组，通过按钮进行上传操作
      // uploadFileList.value = fileList;
    },
    "on-success": (response, file, fileList) => {
      // 方案2：手动维护上传数组，通过按钮进行上传操作
      //   if (response.code === 200) {
      //     searchHandle();
      //   } else {
      //     FMessage.error(response.msg);
      //   }
      //   uploadRef.value.clearFiles();
      //   uploadFileList.value = [];

      // 方案1：自动上传
      console.log(
        file,
        fileList,
        uploadFileIndex.value >= uploadFileList.value.length
      );
      if (uploadFileIndex.value >= uploadFileList.value.length) {
        setTimeout(() => {
          uploadFileList.value.splice(0);
        });
      }
      console.log(uploadFileIndex.value);
      if (response.code === 200) {
        searchHandle();
      } else {
        FMessage.error(response.msg);
      }
    },
    "on-error": (erron, file, fileList) => {
      FMessage.error(erron);
      // 方案2：手动维护上传数组，通过按钮进行上传操作
      // uploadRef.value.clearFiles();
      // uploadFileList.value = [];

      // 方案1
      // uploadRef.value.clearFiles();
      // uploadFileList.value = [];
    },
    // httpRequest: (param) => {
    // 方案2.2：改写httpRequest,这里为了方便维护，采用同步fileList的方案
    //   debugger;
    //   fileList.value.push(param.file); // 一般情况下是在这里创建FormData对象，但我们需要上传多个文件，为避免发送多次请求，因此在这里只进行文件的获取，param可以拿到文件上传的所有信息
    // },
  });
```



### 需求补充，完整代码

上面方案的代码是有问题的，因为没有清空`fileList`中的数据，导致上传列表一直在叠加，并未清空。

在下面这个方案中，我们直接将`fileList`作为上传列表，并在上传成功之前展示在列表中展示正在上传的状态。

另外，这个上传组件中的接口，需要我们手动加上token。

```js
  // 上传文件相关
  const uploadRef = ref(null);
  const uploadFileIndex = ref(0); // 在采用方案1时需要
  const uploadFileList = ref([]); // 方案2，1都需要,方案2时就是文件数组，方案1时可以理解为上传队列，成功和失败都会从队列中移除，根据队列中的索引决定演示器的设置
  const uploadProps = reactive({
    drag: true,
    uploadDisabled: true, //上传按钮
    headers: {
      Authorization: store.getters.getToken,
    },
    "auto-upload": true,
    accept: ".docx,.pdf,.ppt,.pptx,.xlsx,.xls,.jpg,.png,.bmp=,.jpeg",
    multiple: true,
    "show-file-list": false,
    action: "/fontGateway/" + REQUEST_URL.WATER + "v1/WatermarkAddTask", // 方案1：采用自动上传，可用这个
    "before-upload": async (file) => {

      // 方案1
      updateTableData();
      const index =
        uploadFileList.value.findIndex((item) => {
          return item.uid === file.uid;
        }) + 1;
      console.error("index", index * 2000);
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(file);
          uploadFileIndex.value = index;
          return resolve();
        }, index * 2000);
      });
    },
    "on-change": (file, fileList) => {
      uploadFileList.value = fileList;
    },
    "before-remove": (file, fileList) => {
      console.log(file, fileList);
    },
    "on-success": async (response, file, fileList) => {

      const index = uploadFileList.value.findIndex((item) => {
        return item.uid === file.uid;
      });

      if (index || index === 0) {
        uploadFileList.value.splice(index, 1);
        fileList.splice(index, 1);
      }
      if (response.code === 200) {
        await searchHandle();
        updateTableData();
      } else {
        FMessage.error(response.msg);
        updateTableData();
      }
    },
    "on-error": (erron, file, fileList) => {
      FMessage.error(erron);
      const index = uploadFileList.value.findIndex((item) => {
        return item.uid === file.uid;
      });
      if (index || index === 0) {
        uploadFileList.value.splice(index, 1);
        fileList.splice(index, 1);
        updateTableData();
      }
    },
  });
  const uploadDisabled = computed(() => uploadFileList.value.length === 0);
  const clickUploadHandler = async () => {
    console.log("clickUploadHandler", uploadFileList.value);
    const result = await uploadData(uploadFileList.value);
    if (result) {
      searchHandle();
    }
    uploadRef.value.clearFiles();
  };
  // 根据 uploadFileList 更新列表数据
  const updateTableData = () => {
    console.log(
      "updateTableData",
      uploadFileList.value,
      tableData.value,
      paginationProps.pageSize
    );

    // 情况1，待上传数组的长度 + 当前列表的长度 <= 每页显示的条数
    if (
      uploadFileList.value.length + tableData.value.length <=
      paginationProps.pageSize
    ) {
      tableData.value = [
        ...formatUploadList(uploadFileList.value),
        ...tableData.value,
      ];
    } else {
      // 情况2，待上传数组的长度 + 当前列表的长度 > 每页显示的条数
      // 情况2.1，待上传数组的长度 >= 每页显示的条数
      if (uploadFileList.value.length >= paginationProps.pageSize) {
        tableData.value = [
          ...formatUploadList(
            uploadFileList.value.slice(0, paginationProps.pageSize)
          ),
        ];
      } else {
        // 情况2.2，待上传数组的长度 < 每页显示的条数
        tableData.value = [
          ...formatUploadList(uploadFileList.value),
          ...tableData.value.slice(
            0,
            paginationProps.pageSize - uploadFileList.value.length
          ),
        ];
      }
    }
  };
  // 格式化上传列表的数据：状态->待确认
  const formatUploadList = (fileList) => {
    if (!fileList) return [];
    return fileList.map((item) => {
      const type = item.name.split(".")[1];
      return {
        fileName: item.name,
        fileFormat: FILE_TYPE_MAP[type].value,
        fileFormatName: type,
        taskStatus: 1,
        watermarkStrategyId: "",
        watermarkContent: "",
        isEditState: false,
        isEditStrategy: false,
        // 按钮权限
        isDeleteHide: true,
        isRetryHide: true,
        isDownHide: true,
        isStartHide: true,
      };
    });
  };
```



### 总结

实现方式多种多样，需求也是多种多样，我们要扩展自己的思路，以应对将来的各种需求。