---
layout: post
title: "cannot be resolved to absolute file path because it does not reside in the file system 원인 및 해결"
tags: Java
comments: true
---

```
cannot be resolved to absolute file path because it does not reside in the file system
```

경로를 어떤 환경에서든 읽어올 수 있도록 하는 과정에서 위와 같은 에러를 심심치 않게 볼 수 있다.  
특히 jar 환경으로 서비스를 하면 만나게 되는데 이런 경우 jar에서는 file:// 형태로는 동작하지 않으므로  
아래와 같이 `ClassPathResource`를 사용하여 경로를 읽고 경로에서 `getInputStream`으로 읽어 들인 후  
바로 `ServletOutputStream`으로 내보내면 간단하게 다운로드 기능을 사용할 수 있다.

<img src="/images/jarfilesystem1.png">

파일을 생성하고 다운로드를 해야하는 경우에도 저장 후 `FileOutputStream`으로 내보내는 것이 아니라  
`InputStream -> ServletOutputStream`으로 바로 넘겨주면 저장하지 않고 바로 다운로드 시킬 수 있다.