---
layout: post
title: "postgis 설치 중 Requires: libgeotiff.so.2()(64bit) 문제 해결"
tags: Postgis
comments: true
---

gdal에 필요한 라이브러리들을 설치하고 postgis를 설치하려니 아래와 같은 에러가 발생했다.

Error: Package: gdal-libs-1.9.2-8.rhel6.x86_64 (pgdg-common)
Requires: libgeotiff.so.2()(64bit)

여기 저기 찾아도 해결책이 안나와서 해외 개인 블로그를 찾아서 아래 명령어를 사용하니 잘 해결이 되었다.
아마 라이브러리 충돌이나 제대로 설치가 안된 문제인듯 하다.

`sudo rpm -Uvh http://elgis.argeo.org/repos/6/elgis-release-6-6_0.noarch.rpm`

참조 : <https://eemyop.blogspot.com/2013/05/postgis-on-rhel-6-toybox-make-sure-to.html>