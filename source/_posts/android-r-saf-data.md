---
title: 在 Android R 中通过 SAF 访问 Android/data
date: 2021-02-18 00:17:51
id: '<%= page.date %>'
tags: 
- 随笔
- 原创
- Android
- 技术笔记
---
```Java
Intent intent = new Intent("android.intent.action.OPEN_DOCUMENT_TREE");
intent.putExtra("android.provider.extra.INITIAL_URI", Uri.parse("content://com.android.externalstorage.documents/tree/primary%3AAndroid%2Fdata/document/primary%3AAndroid%2Fdata"));
startActivityForResult(intent, SOME_RESULT_CODE);
```
访问`obb`同理
```Java
Intent intent = new Intent("android.intent.action.OPEN_DOCUMENT_TREE");
intent.putExtra("android.provider.extra.INITIAL_URI", Uri.parse("content://com.android.externalstorage.documents/tree/primary%3AAndroid%2Fobb/document/primary%3AAndroid%2Fobb"));
startActivityForResult(intent, SOME_RESULT_CODE);
```

完整代码：[GitHub@SAFTest](https://github.com/MlgmXyysd/SAFTest)