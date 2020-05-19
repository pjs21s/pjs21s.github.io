---
layout: post
title: "실제로 적용해본 웹 사이트 최적화 기본 정리"
tags: Frontend
comments: true
---

회사 업무로 웹 페이지 최적화 작업을 맡게되었습니다.
이번에 해보면서 사용한 것들과 깨달은 점들을 정리해보려고 합니다.

우선 웹 페이지 최적화를 하면서 시행한 방안들은 이와 같습니다.
그리고 기본적으로는 Yahoo 개발자 페이지에 있는 [Best Practices for Speeding Up Your Web Site](https://developer.yahoo.com/performance/rules.html) 참고했습니다.
성능 측정은 <https://www.webpagetest.org/>를 통해 시행했습니다.

1. CSS 상단, Script 하단 배치
2. 불필요한 주석, 공백 제거, CSS 외부 파일로 분리
3. Lazy Loading, YUI Compressor 사용하여 CSS, Script 압축
4. pngquant 사용하여 화면에서 사용하는 이미지 압축

크게 어려운건 아니고 이미 선조 개발자분들께서 만들어 놓으신 규칙만 잘 지키면 됩니다.
오히려 도구 찾아서 적용하고 테스트하는게 더 시간이 걸렸던것 같습니다.

# CSS 상단, Script 하단 배치

브라우저가 페이지를 그릴때 스타일시트가 반드시 필요합니다.
그렇기에 브라우저는 스타일시트를 전부 다운로드 할 때까지 렌더링하지 않고 대기합니다.
그렇기에 먼저 다운로드 하기 위해 상단에 넣어줍니다.

브라우저는 스크립트를 실행할 때 렌더링을 중지합니다.
그래서 만약 중간 중간에 스크립트가 있다면 사용자 입장에서 화면이 멈춘것처럼 느껴질 수 있습니다.
그렇기에 스크립트는 하단 `</body>`위에 적습니다.

```html
<html>
<head>
<link href="/css/modal.css" type="text/css" rel="stylesheet">
<link href="/css/main.css" type="text/css" rel="stylesheet">
<link href="/css/index.css" type="text/css" rel="stylesheet">
</head>
<body>
...
<script type="text/javascript" src="/js/graph.js"></script>
<script type="text/javascript" src="/js/Chart.min.js"></script>
</body>
</html>
```

# 불필요한 주석, 공백 제거, CSS 외부 파일로 분리

필요한 주석을 애써 지울 필요는 없지만 소스가 정말 비대해지고 전임자들에 의한 필요없는 주석이 남아있다면
귀찮더라도 찾아서 전부 지우는것이 좋습니다. 쓸데없는 공백도 마찬가지 입니다.
필자는 주석과 공백을 지워서 0.1MB를 절약했습니다.
물론 제가 만진 코드는 조금 많이 비대합니다.

CSS를 외부 파일로 분리하면 유지 보수도 쉽고 브라우저 캐시에 의해 추후 로딩이 빨라집니다.
하지만 가져오는데 정말로 시간이 많이 걸린다면 인라인 형식으로 넣어주는것도 괜찮은것 같습니다.

# Lazy Loading, YUI Compressor 사용하여 CSS, Script 압축

스프링의 경우 YUI Compressor를 사용하면 배포를 위해 war를 뽑을 때 Script, CSS를 압축해줍니다.
고칠 필요가 없는 외부 라이브러리의 경우 미리 압축을 해놓을 수 있지만 추후 유지 보수가 필요한
자체 JS, CSS라면 미리 압축을 해놓을 시 유지보수가 힘들것이므로 배포할때만 압축을 합니다.

그러나 이에 경우 이미 다수의 코드를 압축하여 사용하고 있다면 효과가 미미합니다.
그렇기에 가능하면 JS, CSS를 통합하여 요청 수를 줄이는 것도 한 방법입니다.

아래 코드의 경우 필자가 사용한 코드인데 `<exclude>`는 minify가 필요없어서 제외할 항목들을 적어주는 곳이고 `<webappDirectory>`와 `maven-war-plugin`의 `<directory>`의 경로를 같게 해야 압축한 항목들이 알아서 교체가 되어
war 파일을 뽑아줍니다. 그렇지 않으면 압축한 소스코드들이 별도의 폴더에만 생성됩니다.


```xml
<plugin>
    <groupId>net.alchim31.maven</groupId>
    <artifactId>yuicompressor-maven-plugin</artifactId>
    <version>1.5.1</version>
        <executions>
            <execution>
                <goals>
                    <goal>compress</goal>
                </goals>
            </execution>
        </executions>
    <configuration>
        <nosuffix>true</nosuffix>
        <webappDirectory>${project.build.directory}/min</webappDirectory>
        <excludes>
            <exclude>/*.min.js</exclude>
            <exclude>/ol.js</exclude>
        </excludes>
    </configuration>
</plugin>
<plugin>
    <artifactId>maven-war-plugin</artifactId>
    <configuration>
        <webResources>
            <resource>
            <directory>${project.build.directory}/min</directory>
            </resource>
        </webResources>
    </configuration>
</plugin>
```


`loading='lazy'`를 사용하는 방법이 최근 구글에서 발표되었으나 아직은 제대로 사용할 수 없기에 이미지 lazy loading은
오픈 소스를 사용합니다.
<https://github.com/aFarkas/lazysizes>
너무 매뉴얼이 잘 되어있어서 제가 따로 작성할 부분은 없습니다.
단지 lazy loading 특성상 사용자가 그 기능을 사용해야 이미지를 로드하기 때문에 무작정 적용하기보다 테스트 해보고 알맞게 적용하는것이 좋습니다.

이번엔 Script를 lazy loading 해보겠습니다.
Script를 lazy loading하는 이유는 특정 기능에만 사용하는 Script를 로딩하느라 시간을 낭비하지 않기 위해서입니다.
아래와 같은 코드를 통해 문서가 전부 로딩되고 나서 Script를 가져올 수 있습니다.


```javascript
function lazyload(){
    var scriptArray = ["plotly.min.js", "kriging.js", "Chart.js", "d3.min.js", "html2canvas.min.js"];
    var headTag = document.getElementsByTagName("head")[0];
    if(!headTag){
        headTag = document.getElementsByTagName("script")[0].parentNode;
    }
		for(var i = 0; i<scriptArray.length; i++){
			var scriptTag = document.createElement("script");
		    
		    scriptTag.setAttribute("src", "/js/"+scriptArray[i]);
		    scriptTag.setAttribute("type","text/javascript");
		    scriptTag.setAttribute("async","true");
			
			headTag.appendChild(scriptTag);   
		}
	}
	window.onload=function(){
		lazyload();
	}
```


# pngquant 사용하여 화면에서 사용하는 이미지 압축

PNG 압축 사이트로 유명한 tinyPNG에서 사용하는 pngquant를 사용해 CentOS에서 이미지를 압축했습니다.
tinyPNG는 압축을 하는 이미지가 일정 수 이상이 되면 유료가 되지만 기반 기술인 pngquant는 오픈 소스이기 때문에 무료입니다.

물론 여유가 되면 JPEG로 변환을 하거나 사이즈도 줄이고 PJPEG라는 형식도 있으니 참고하면 좋습니다.
하지만 필자는 압축을 해야할 이미지도 수천장이 될것이므로 압축만 해보기로 했고 실제로 꽤
성능 향상이 이루어졌습니다. 물론 이미지를 많이 사용하는 서비스이기에 체감이 크다고 생각합니다.

우선 pngquant를 설치합니다.
최신 버전을 사용해야한다면 조금 더 복잡하지만 필자는 그냥 구버전을 사용했습니다.
`yum install pngquant`
`pngquant -V`
버전 확인이 된다면 설치가 완료된것입니다.

기본 사용법은 `pngquant image.png`
진행상황을 보고 싶다면 `pngquant --verbose image.png`
기본적으로 pngquant는 압축을 할 때 최대한 높은 퀄리티로 작업하기 때문에
용량을 조금 더 줄여야 한다면 지정을 해주어야 합니다.
`pngquant --quality=70-80 --verbose image.png`
정말로 중요한(의료용 이미지) 이미지가 아닌 이상 퀄리티를 70까지 낮춰도 무방합니다.
기본적으로 pngquant는 이미지를 덮어씌우지 않고 다른 이름으로 새로 만듭니다.
그렇기에 만약 덮어씌우고 싶다면 이와 같이 사용해야 합니다.
`pngquant test/*.png --ext .png --force`

그리고 여러 서브디렉토리가 있는 상황에서 그 안에 있는 모든 png를 압축하고 싶다면
.sh 파일을 만들어서 실행해야 합니다.
아래와 같은 코드를 .sh 파일에 적고 실행하면 서브 디렉토리가 여러개 있어도 png만 찾아서
압축합니다. 당연히 저 .sh 파일은 여러개의 서브디렉토리를 포함하는 상위 디렉토리에 있어야 합니다.


```shell
for file in `find . -name '*.png'`; do
    pngquant --quality=75-85 --verbose --force --ext '.png' $file
done
```


이미지 압축을 통해 약 30 - 70% 사이의 압축이 이루어졌고 압축 전에 비해 웹 페이지 용량이 40%정도 감소했습니다.

Windows에서 PNG 압축을 여러장 해야한다면 PNGGauntlet을 사용하면 좋은 것 같습니다.
Mac OS, linux의 경우 툴이 다양해서 찾아보면 금새 나옵니다.