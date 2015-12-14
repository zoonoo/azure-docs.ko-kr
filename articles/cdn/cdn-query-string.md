<properties 
	pageTitle="CDN - 쿼리 문자열이 포함된 요청의 캐싱 동작 제어" 
	description="CDN 쿼리 문자열 캐싱은 파일에 쿼리 문자열이 들어 있을 때 파일이 캐시되는 방법을 제어합니다." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

#쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Premium](cdn-query-string-premium.md)

##개요

쿼리 문자열 캐싱은 파일에 쿼리 문자열이 들어 있을 때 파일이 캐시되는 방법을 제어합니다.

> [AZURE.NOTE]표준 및 프리미엄 CDN 계층은 동일한 쿼리 문자열 캐싱 기능을 제공하지만 사용자 인터페이스는 다릅니다. 이 문서에서는 **표준** 계층 사용자 인터페이스를 설명합니다. 프리미엄 계층의 경우 [쿼리 문자열이 포함된 CDN 요청의 캐싱 동작 제어 - 프리미엄](cdn-query-string-premium.md)을 참조하세요.

다음 세 가지 모드를 사용할 수 있습니다.

- **쿼리 문자열 무시**: 기본 모드입니다. CDN 에지 노드가 첫 번째 요청에서 요청자의 쿼리 문자열을 원본으로 전달하고 자산을 캐시합니다. 캐시된 자산이 만료될 때까지 에지 노드에서 제공되는 해당 자산의 모든 후속 요청은 쿼리 문자열을 무시합니다.
- **쿼리 문자열이 포함된 URL에 대한 캐싱 바이패스**:이 모드에서 쿼리 문자열이 포함된 요청은 CDN 에지 노드에서 캐시되지 않습니다. 에지 노드가 원본에서 직접 자산을 검색하고 각 요청과 함께 요청자에게 전달합니다.
- **모든 고유한 URL 캐시**: 이 모드는 쿼리 문자열이 포함된 각 요청을 자체 캐시를 가진 고유한 자산으로 처리합니다. 예를 들어 *foo.ashx?q=bar*에 대한 요청의 경우 원본에서의 응답이 에지 노드에서 캐시되고 그와 동일한 쿼리 문자열을 가진 후속 캐시에 대해 반환됩니다. *foo.ashx?q=somethingelse*에 대한 요청은 자체 TTL(Time To Live)과 함께 별도의 자산으로 캐시됩니다.
	
	>[AZURE.WARNING]이 모드는 쿼리 문자열에 세션 ID 또는 사용자 이름과 같이 모든 요청에서 변경되는 매개 변수가 들어 있는 경우 캐시 적중률이 매우 낮아지기 때문에 사용하지 않아야 합니다.

##쿼리 문자열 캐싱 설정 변경

1. CDN 프로필 블레이드에서 관리하려는 CDN 끝점을 클릭합니다.
	
	![CDN 프로필 블레이드 끝점](./media/cdn-query-string/cdn-endpoints.png)

	CDN 끝점 블레이드가 열립니다.

2. **구성** 단추를 클릭합니다.

	![CDN 프로필 블레이드 관리 단추](./media/cdn-query-string/cdn-config-btn.png)
	
	CDN 구성 블레이드가 열립니다.
	
3. **쿼리 문자열 캐싱 동작** 드롭다운에서 설정을 선택합니다.
	
	![CDN 쿼리 문자열 캐싱 옵션](./media/cdn-query-string/cdn-query-string.png)
	
4. 선택한 후 **저장** 단추를 클릭합니다.




	

<!---HONumber=AcomDC_1203_2015-->