---
layout: post
title: "geoserver shp 설정하기"
tags: Geoserver
comments: true
---

[대한민국 Shapefile(SHP) 다운로드](http://www.gisdeveloper.co.kr/?p=2332)

위의 링크에서 shp을 다운받아서 geoserver 폴더에 넣습니다.

<img src="/images/geoserversetting1.png">

CTPRVN_202005가 제가 압축을 푼 폴더입니다.

<img src="/images/geoserversetting2.png">

폴더에 들어가시면 이렇게 4 종류의 파일이 있어야 합니다.
prj는 지도에 shp을 올바르게 뿌려주기 위한 좌표계변환 참조 파일입니다.

그리고 geoserver 웹 페이지로 돌아가서 좌측의 작업공간(Workspace)을 눌러 새로운 작업공간을 추가합니다.

<img src="/images/geoserversetting3.png">

저는 test라는 이름으로 생성했습니다.

이제 좌측의 저장소(Stores)를 눌러 새로운 저장소를 생성합니다.

<img src="/images/geoserversetting4.png">

여기서 처음에 있는 `Directory of spatial files(shapefiles)`가 아니라 6번째에 있는 `Shapefile`을 눌러 생성해야합니다.

<img src="/images/geoserversetting5.png">

데이터 저장소 이름은 자유롭게 적어주시고 Shapefile을 위치를 잘 선택하면 됩니다. 저는 방금 넣은 폴더안에 있는 shp을 선택했습니다. 그리고 이 폴더안에는 prj가 있어야 올바르게 좌표계 변환을 할 수 있습니다.

<img src="/images/geoserversetting6.png">

저장을 누르면 새로운 레이어라는 타이틀과 함께 발행하기를 클릭할 수 있게 됩니다. 클릭해줍니다.

다른 항목들은 그대로 두시고 아래로 내려오시면 공간 좌표 체계가 있습니다. 여기만 잘 수정하면 됩니다.

<img src="/images/geoserversetting7.png">

이미지처럼 하면 되는데요. 일단 정의한 좌표체계에 있는 검색 버튼을 눌러서 4326을 검색해서 WGS84를 선택합니다. 그리고 좌표체계 처리 방식은 원본을 정의한 좌표체계로 투영을 선택합니다.

밑에 있는 레이어 최소경계 영역은 데이터로부터 계산하기를 눌러서 자동으로 계산하도록 합니다. 위/경도 영역도 원본 영역으로부터 계산하기를 눌러 자동 계산합니다. 수치는 파일에 따라서 조금씩 다를 수 있습니다.

이렇게 하고 다시 위로 올라와서 발행 탭을 눌러줍니다. 밑으로 내려오시면 WMS 설정이 있는데 저는 여기서 투명을 선택하고 기본 스타일을 line(선)으로 변경하였습니다. 왜냐하면 지도 위에 반투명하게 뿌릴 예정이기 때문입니다. 이제 저장을 합니다.

<img src="/images/geoserversetting8.png">

좌측에 레이어 미리보기를 클릭하셔서 이미지처럼 OpenLayers를 누르시거나 옆에서 PNG와 같은걸 선택하셔서 지도가 정상적으로 나오면 성공입니다.