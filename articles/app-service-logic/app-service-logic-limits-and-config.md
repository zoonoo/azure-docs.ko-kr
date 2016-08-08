<properties
	pageTitle="논리 앱 한도 및 구성 | Microsoft Azure"
	description="논리 앱에 사용 가능한 서비스 한도 및 구성 값에 대한 개요입니다."
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="jeffhollan"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2016"
	ms.author="jehollan"/>

# 논리 앱 한도 및 구성

Azure Logic Apps에 대한 현재 한도 및 구성 정보는 다음과 같습니다.

## 제한

### HTTP 요청 한도

단일 HTTP 요청 및/또는 커넥터 호출에 대한 한도는 다음과 같습니다.

#### 시간 제한

|이름|제한|참고 사항|
|----|----|----|
|요청 시간 초과|1분|필요에 따라 [비동기 패턴](app-service-logic-create-api-app.md) 또는 [until 루프](app-service-logic-loops-and-scopes.md)를 보완할 수 있음|

#### 메시지 크기

|이름|제한|참고 사항|
|----|----|----|
|메시지 크기|50MB|일부 커넥터 및 API에서는 50MB를 지원하지 않을 수 있습니다. 요청 트리거는 최대 25MB를 지원함|
|식 평가 제한|131,072자|`@concat()`, `@base64()`, `string`은 이 제한보다 길 수 없음|

#### 다시 시도 정책

|이름|제한|참고 사항|
|----|----|----|
|다시 시도 횟수|4|[재시도 정책 매개 변수](https://msdn.microsoft.com/ko-KR/library/azure/mt643939.aspx)로 구성할 수 있음|
|재시도 최대 지연 시간|1시간|[재시도 정책 매개 변수](https://msdn.microsoft.com/ko-KR/library/azure/mt643939.aspx)로 구성할 수 있음|
|재시도 최소 지연 시간|20분|[재시도 정책 매개 변수](https://msdn.microsoft.com/ko-KR/library/azure/mt643939.aspx)로 구성할 수 있음|

### 실행 기간 및 보존

단일 논리 앱 실행에 대한 한도는 다음과 같습니다.

|이름|제한|참고 사항|
|----|----|----|
|실행 기간|90일||
|저장소 보존|90일|실행 시작 시간을 기준으로 합니다.|
|최소 되풀이 간격|15초||
|최대 되풀이 간격|500일||


### 반복 및 분리 한도

단일 논리 앱 실행에 대한 한도는 다음과 같습니다.

|이름|제한|참고 사항|
|----|----|----|
|ForEach 항목|10000|[쿼리 작업](../connectors/connectors-native-query.md)을 사용하여 필요에 따라 큰 배열을 필터링할 수 있습니다.|
|Until 반복|10000||
|SplitOn 항목|10000||
|ForEach 병렬 처리|20|`foreach` 작업에 `"operationOptions": "Sequential"`을 추가하여 순차적인 foreach를 설정할 수 있습니다.|


### 처리량 한도

단일 논리 앱 인스턴스에 대한 한도는 다음과 같습니다.

|이름|제한|참고 사항|
|----|----|----|
|초당 트리거 수|100|필요에 따라 여러 앱 간에 워크플로를 배포할 수 있음|

### 정의 한도

단일 논리 앱 정의에 대한 한도는 다음과 같습니다.

|이름|제한|참고 사항|
|----|----|----|
|ForEach에서 작업|1|중첩된 워크플로를 추가하여 필요에 따라 확장할 수 있음|
|워크플로당 작업|60|중첩된 워크플로를 추가하여 필요에 따라 확장할 수 있음|
|허용된 작업 중첩 깊이|5|중첩된 워크플로를 추가하여 필요에 따라 확장할 수 있음|
|구독당 지역별 흐름|1000||
|워크플로당 트리거|10||
|식당 최대 문자 수|8,192||
|최대 `trackedProperties` 크기(자)|16,000|
|`action`/`trigger` 이름 제한|80||
|`description` 길이 제한|256||
|`parameters` 제한|50||
|`outputs` 제한|10||

## 구성

### IP 주소

[커넥터](../connectors/apis-list.md)에서의 호출은 아래 지정된 IP 주소에서 발생합니다.

논리 앱에서의 직접 호출(즉, [HTTP](../connectors/connectors-native-http.md) 또는 [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)를 통해)은 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/ko-KR/download/details.aspx?id=41653) 중에서 발생할 수 있습니다.

|논리 앱 지역|아웃바운드 IP|
|-----|----|
|오스트레일리아 동부|40\.126.251.213|
|오스트레일리아 남동부|40\.127.80.34|
|브라질 남부|191\.232.38.129|
|인도 중부|104\.211.98.164|
|미국 중부|40\.122.49.51|
|동아시아|23\.99.116.181|
|미국 동부|191\.237.41.52|
|미국 동부 2|104\.208.233.100|
|일본 동부|40\.115.186.96|
|일본 서부|40\.74.130.77|
|미국 중북부|65\.52.218.230|
|북유럽|104\.45.93.9|
|미국 중남부|104\.214.70.191|
|동남아시아|13\.76.231.68|
|인도 남부|104\.211.227.225|
|서유럽|40\.115.50.13|
|인도 서부|104\.211.161.203|
|미국 서부|104\.40.51.248|


## 다음 단계  

- 논리 앱을 시작하려면 [논리 앱 만들기](app-service-logic-create-a-logic-app.md) 자습서를 따르세요.
- [일반적인 예제 및 시나리오 보기](app-service-logic-examples-and-scenarios.md)
- [논리 앱으로 비즈니스 프로세스를 자동화할 수 있습니다](http://channel9.msdn.com/Events/Build/2016/T694)
- [논리 앱과 시스템을 통합하는 방법을 알아봅니다](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0727_2016-->