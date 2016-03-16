<properties
	pageTitle="CDN - 파일을 압축하여 성능 향상"
	description="파일을 압축하여 파일 전송 속도를 개선하고 페이지 로드 성능을 향상시킬 수 있습니다."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# 파일을 압축하여 성능 향상

이 항목에서는 파일을 압축하여 파일 전송 속도를 개선하고 페이지 로드 성능을 향상시키는 방법을 설명합니다.

CDN은 다음 두 가지 방법으로 압축을 지원할 수 있습니다.

- 원본 서버에서 압축을 사용하도록 설정할 수 있습니다. 이 경우 CDN이 기본적으로 압축을 지원하며 클라이언트에 압축 파일을 전달합니다.
- CDN 에지 서버에서 직접 압축을 사용하도록 설정할 수 있습니다. 이 경우 CDN이 파일을 압축하여 최종 사용자에게 제공합니다.

## 압축을 사용하도록 설정

> [AZURE.NOTE] 표준 및 프리미엄 CDN 계층은 동일한 압축 기능을 제공하지만 사용자 인터페이스는 다릅니다. 표준과 프리미엄 CDN 계층 간의 차이점에 대한 자세한 내용은 [Azure CDN 개요](cdn-overview.md)를 참조하세요.

### 표준 계층

1. CDN 프로필 블레이드에서 관리하려는 CDN 끝점을 클릭합니다.

	![CDN 프로필 블레이드 끝점](./media/cdn-file-compression/cdn-endpoints.png)

	CDN 끝점 블레이드가 열립니다.

2. **구성** 단추를 클릭합니다.

	![CDN 프로필 블레이드 관리 단추](./media/cdn-file-compression/cdn-config-btn.png)

	CDN 구성 블레이드가 열립니다.

3. **압축**을 켭니다.

	![CDN 압축 옵션](./media/cdn-file-compression/cdn-compress-standard.png)

4. 기본 형식을 사용하거나 파일 형식을 추가 또는 제거하여 목록을 수정합니다.

5. 변경한 후 **저장** 단추를 클릭합니다.

### 프리미엄 계층

1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.

	![CDN 프로필 블레이드 관리 단추](./media/cdn-file-compression/cdn-manage-btn.png)

	CDN 관리 포털이 열립니다.

2. **HTTP Large**(HTTP 크게) 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다. **압축**을 클릭합니다.

	압축 옵션이 표시됩니다.

	![파일 압축](./media/cdn-file-compression/cdn-compress-files.png)

3. 파일 형식 목록을 수정한 후 **업데이트** 단추를 클릭합니다.


## 압축 프로세스 및 규칙

1. 요청자가 콘텐츠에 대한 요청을 보냅니다.
2. 에지 서버에서 **Accept-Encoding** 헤더가 있는지 여부를 확인합니다.
	1. 포함된 경우 이 헤더는 요청된 압축 방법을 식별합니다.
	1. 누락된 경우 이 유형의 요청이 압축되지 않은 형식으로 처리됩니다.
3.	가장 가까운 에지 POP는 캐시 상태, 압축 방법 및 유효한 TTL(Time to Live)이 여전히 있는지 여부를 확인합니다.
	1.	캐시 누락 수: 요청한 버전이 캐시되지 않은 경우 요청이 원본에 전달됩니다.
	2.	동일한 압축 방법을 사용한 캐시 적중 횟수: 에지 서버가 클라이언트에 압축된 콘텐츠를 즉시 전달합니다.
	3.	다른 압축 방법을 사용한 캐시 적중 횟수: 에지 서버가 자산을 요청된 압축 방법으로 코드를 변환합니다.
	4.	캐시 적중 횟수 및 압축되지 않음: 초기 요청에서 자산이 압축되지 않은 형식으로 캐시된 경우 위의 정의/요구 사항 섹션의 기준에 따라 요청이 에지 서버 압축에 적합한지 확인하는 검사가 수행됩니다.
		1.	적합한 경우 에지 서버가 파일을 압축하고 클라이언트에 제공합니다.
		2.	적합하지 않은 경우 에지 서버가 압축되지 않은 콘텐츠를 클라이언트에 즉시 전달합니다.



## 고려 사항

1. 미디어 서비스 CDN 사용 스트리밍 끝점의 경우 다음 콘텐츠 형식에 대해 기본적으로 압축이 사용됩니다. application/vnd.ms-sstr+xml, application/dash+xml,application/vnd.apple.mpegurl, application/f4m+xml. Azure 포털에서 언급된 형식에 대해 압축을 사용하거나 사용하지 않도록 설정할 수 없습니다.  
2. 하나의 파일 버전(압축 또는 압축되지 않음)만 에지 서버에 캐시됩니다. 다른 버전을 요청하면 에지 서버에서 콘텐츠를 트랜스코딩합니다.  

<!---HONumber=AcomDC_0302_2016-->