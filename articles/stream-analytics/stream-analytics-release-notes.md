<properties 
	pageTitle="스트림 분석 릴리스 정보 | Microsoft Azure" 
	description="스트림 분석 GA 릴리스 정보" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="08/20/2015" 
	ms.author="jeffstok"/>

#Microsoft 스트림 분석 릴리스 정보

## 스트림 분석의 2015/08/20 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

제목|설명
---|---
추가된 LAST 함수 |이제 [LAST](http://msdn.microsoft.com/library/mt421186.aspx) 함수를 스트림 분석에서 사용할 수 있으며 특정 기간 내의 이벤트 스트림에서 최신 이벤트를 검색할 수 있습니다.
새 배열 함수|이제 배열 함수 [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) 및 [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx)를 사용할 수 있습니다.
새 레코드 함수|이제 레코드 함수 [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) 및 [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx)를 사용할 수 있습니다.

## 스트림 분석의 2015/07/30 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.

제목|설명
---|---
Azure ID에서 분리된 Power BI 조직 ID|이 기능을 통해 모든 Azure 계정 유형의 ASA 작업에 대한 [Power BI 출력](stream-analytics-power-bi-dashboard.md)을 사용합니다(Live ID 또는 조직 ID). 또한 Azure 계정에 대한 하나의 조직 ID를 포함하고 Power BI 출력 권한 부여를 위한 다른 하나를 사용할 수 있습니다.
서비스 버스 큐 출력에 대한 지원|[서비스 버스 큐](stream-analytics-connect-data-event-outputs.md#service-bus-queues) 출력은 지금 스트림 분석 작업에서 사용할 수 있습니다.
서비스 버스 토픽 출력에 대한 지원|[서비스 버스 토픽](stream-analytics-connect-data-event-outputs.md#service-bus-topics) 출력은 지금 스트림 분석 작업에서 사용할 수 있습니다.

## 스트림 분석의 2015/07/09 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.


제목|설명
---|---
사용자 지정 Blob 출력 분할|이제 Blob 저장소 출력은 출력 Blob이 작성되는 빈도 및 구조와 출력 데이터 경로 폴더 구조의 형식을 지정하는 옵션을 허용합니다. 

## 스트림 분석의 2015/05/03 릴리스에 대한 정보 ##

이 릴리스에는 다음 업데이트가 포함되어 있습니다.


제목|설명
---|---
순서 비지정 허용 오차 창의 최대 증가 값|순서 비지정 허용 오차 창의 최대 크기는 이제 59:59(MM:SS)입니다.
JSON 출력 형식: 구분 줄 또는 배열|이제 Blob 저장소 또는 이벤트 허브로 출력 시 JSON 개체의 배열이나 새 줄로 JSON 개체를 구분하여 출력하는 옵션이 있습니다. 

## 스트림 분석의 2015/04/16 릴리스에 대한 정보 ##


제목|설명
---|---
Azure 저장소 계정 구성 지연|처음에 한 지역에서 스트림 분석 작업을 만들면 해당 지역에서 스트림 분석 작업을 모니터링하기 위해 새 저장소 계정을 만들거나 기존 계정을 지정하라는 메시지가 표시됩니다. 모니터링을 구성하는 데 다소 시간이 소요되므로 동일한 지역에서 30분 이내에 다른 스트림 분석 작업을 만들면 모니터링 저장소 계정 드롭다운에 최근에 구성한 계정이 표시되지 않고 또 다른 저장소 계정을 지정하도록 요구됩니다. 불필요한 저장소 계정을 만들지 않으려면 한 지역에서 추가 작업을 프로비전하기 전에 해당 지역에서 처음 작업을 만든 후에 30분 정도 기다립니다.
작업 업그레이드|현재 스트림 분석에서는 실행 중인 작업의 정의 또는 구성을 실시간으로 편집할 수 없습니다. 실행 중인 작업의 입력, 출력, 쿼리, 규모 또는 구성을 변경하려면 먼저 작업을 중지해야 합니다.
입력 원본에서 유추되는 데이터 형식|CREATE TABLE 문을 사용하지 않은 경우 입력 유형은 입력 형식에서 파생됩니다. 예를 들어 CSV의 모든 필드는 문자열입니다. 형식 불일치 오류를 방지하려면 CAST 함수를 사용하여 필드를 올바른 형식으로 명시적으로 변환해야 합니다.
누락된 필드는 null 값으로 출력됨|입력 원본에 없는 필드를 참조하면 출력 이벤트에 null 값이 생성됩니다.
WITH 문은 SELECT 문 앞에 와야 함|쿼리에서 SELECT 문은 WITH 문에 정의된 하위 쿼리를 따라야 합니다.
메모리 부족 문제|순서가 지정되지 않은 이벤트 및/또는 많은 양의 상태를 유지하는 복합 쿼리에 대해 허용 오차가 큰 스트리밍 분석 작업을 수행하면 메모리가 부족하여 작업이 다시 시작됩니다. 시작 및 중지 작업은 작업의 작업 로그에 표시됩니다. 이러한 동작을 방지하려면 여러 파티션에 걸쳐 쿼리 출력 크기를 조정합니다. 향후 릴리스에서는 영향 받는 작업을 다시 시작하지 않고 성능을 저하시켜 이러한 제한 문제를 해결할 예정입니다.
페이로드 타임스탬프가 없는 대용량 Blob 입력은 메모리 부족 문제를 일으킬 수 있습니다.|TIMESTAMP BY를 통해 타임스탬프 필드를 지정하지 않은 경우 Blob 저장소에서 대용량 파일을 사용하면 스트림 분석 작업이 중단될 수 있습니다. 이 문제를 방지하려면 각 Blob을 10MB 미만으로 유지하세요.
SQL 데이터베이스 이벤트 볼륨 제한|SQL 데이터베이스를 출력 대상으로 사용할 경우 대용량 데이터가 있으면 스트림 분석 작업 시간이 초과될 수 있습니다. 이 문제를 해결하려면 집계 또는 필터 연산자를 사용하여 출력 볼륨을 줄이거나 Azure Blob 저장소 또는 이벤트 허브를 출력 대상으로 대신 선택하세요.
PowerBI 데이터 집합은 하나의 테이블만 포함할 수 있습니다.|PowerBI는 지정된 데이터 집합에서 둘 이상의 테이블을 지원하지 않습니다.

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/ko-kr/home?forum=AzureStreamAnalytics)을 참조하세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](../stream.analytics.get.started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=August15_HO8-->