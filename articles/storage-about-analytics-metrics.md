<properties 
	pageTitle="저장소 분석 메트릭 정보" 
	description="트랜잭션 메트릭 및 용량 메트릭을 비롯한 저장소 분석 사용 방법, 메트릭 저장 방법 및 메트릭 데이터 액세스 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# 저장소 분석 메트릭 정보

## 개요

저장소 분석에서는 저장소 서비스에 대한 요청 관련 용량 데이터 및 집계된 트랜잭션 통계를 포함하는 메트릭을 저장할 수 있습니다. 트랜잭션은 API 작업 수준과 저장소 서비스 수준에서 모두 보고되며 용량은 저장소 서비스 수준에서 보고됩니다. 메트릭 데이터를 사용하면 저장소 서비스 사용량을 분석하고 저장소 서비스에 대한 요청의 문제를 진단하며 서비스를 사용하는 응용 프로그램의 성능을 개선할 수 있습니다.

저장소 분석을 사용하려면 모니터링할 각 서비스에 대해 저장소 분석을 개별적으로 사용하도록 설정해야 합니다. [Azure 관리 포털](https://manage.windowsazure.com/)에서 저장소 분석을 사용하도록 설정할 수 있습니다. 자세한 내용은 [저장소 계정을 모니터링하는 방법](../how-to-monitor-a-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. [Blob 서비스 속성 가져오기, 큐 서비스 속성 가져오기](https://msdn.microsoft.com/library/hh452239.aspx) 및 [테이블 서비스 속성 가져오기 작업을 사용하여 각 서비스에 대해 저장소 분석](https://msdn.microsoft.com/library/hh452238.aspx)을 사용하도록 설정합니다.

##트랜잭션 메트릭

각 저장소 서비스 및 요청한 API 작업에 대해 시간 또는 분 간격으로 유용한 데이터 집합이 기록됩니다. 이러한 데이터에는 수신/송신, 가용성, 오류, 분류된 요청 비율 등이 포함됩니다. 트랜잭션 세부 정보의 전체 목록은 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/hh343264.aspx) 항목에서 확인할 수 있습니다.

트랜잭션 데이터는 두 개의 수준, 즉 서비스 수준과 API 작업 수준에서 기록됩니다. 서비스 수준에서는 서비스에 대한 요청이 없더라도 요청한 모든 API 작업을 요약하는 통계가 1시간마다 테이블 엔터티에 기록됩니다. API 작업 수준에서는 해당 시간 동안 작업이 요청된 경우에만 통계가 엔터티에 기록됩니다.

예를 들어 Blob 서비스에 대해 **GetBlob** 작업을 수행하면 저장소 분석 메트릭이 요청을 기록한 다음 **GetBlob** 작업뿐만 아니라 Blob 서비스에 대해 집계된 데이터에도 포함합니다. 그러나 해당 시간 동안 요청된 **GetBlob** 작업이 없으면 해당 작업에 대한 '$MetricsTransactionsBlob'에 엔터티가 기록되지 않습니다.

저장소 분석 자체에서 수행한 요청과 사용자 요청 둘 모두에 대해 트랜잭션 메트릭이 기록됩니다. 예를 들어 로그 및 테이블 엔터티 작성을 위한 저장소 분석의 요청이 기록됩니다. 이러한 요청에 대해 요금이 청구되는 방식에 대한 자세한 내용은 [저장소 분석 및 청구](https://msdn.microsoft.com/library/hh360997.aspx)를 참조하세요.

##용량 메트릭

>[AZURE.NOTE] 현재는 Blob 서비스에 대해서만 용량 메트릭이 제공됩니다. 테이블 서비스와 큐 서비스에 대한 용량 메트릭은 이후 저장소 분석 버전에서 제공될 예정입니다.

용량 데이터는 저장소 계정의 Blob 서비스에 대해 매일 기록되며 2개 테이블 엔터티가 작성됩니다. 이러한 엔터티 중 하나는 사용자 데이터에 대한 통계를 제공하며 다른 하나는 저장소 분석에서 사용하는 '$logs' Blob 컨테이너에 대한 통계를 제공합니다. '$MetricsCapacityBlob' 테이블에는 다음 통계가 포함됩니다.

- **Capacity**: 저장소 계정의 Blob 서비스가 사용하는 저장소의 양(바이트)입니다.

- **ContainerCount**: 저장소 계정의 Blob 서비스에 포함된 Blob 컨테이너의 수입니다.

- **ObjectCount**: 저장소 계정의 Blob 서비스에서 커밋된/커밋되지 않은 블록이나 페이지 Blob의 수입니다.

용량 메트릭에 대한 자세한 내용은 저장소 분석 메트릭 테이블 스키마를 참조하세요.

##메트릭 저장 방법

각 저장소 서비스의 모든 메트릭 데이터는 해당 서비스용으로 예약된 테이블 3개에 저장됩니다. 이러한 테이블은 각각 트랜잭션 정보용, 세부 트랜잭션 정보용, 용량 정보용 테이블입니다. 트랜잭션 및 세부 트랜잭션 정보는 요청과 응답 데이터로 구성되며 용량 정보는 저장소 사용량 데이터로 구성됩니다. 다음 표에서 설명하는 대로 이름이 지정된 표에서 시간 메트릭, 분 메트릭 및 저장소 계정 Blob 서비스의 용량 정보에 액세스할 수 있습니다.

| 메트릭 수준                      	| 테이블 이름                                                                                                                 	| 지원되는 버전                                                                                                                       	|
|------------------------------------	|-----------------------------------------------------------------------------------------------------------------------------	|----------------------------------------------------------------------------------------------------------------------------------------------	|
| 시간 메트릭, 기본 위치   	|  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                              	| 2013-08-15 이전 버전만 해당됩니다. 이러한 이름은 계속 지원되기는 하지만 아래에 나와 있는 테이블을 사용하도록 전환하는 것이 좋습니다. 	|
| 시간 메트릭, 기본 위치   	| $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue             	| 2013-08-15를 포함한 모든 버전                                                                                                           	|
| 분 메트릭, 기본 위치   	| $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue       	| 2013-08-15를 포함한 모든 버전                                                                                                           	|
| 시간 메트릭, 보조 위치 	| $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue       	| 2013-08-15를 포함한 모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.                                                   	|
| 분 메트릭, 보조 위치 	| $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue 	| 2013-08-15를 포함한 모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.                                                   	|
| 용량(Blob 서비스만 해당)       	| $MetricsCapacityBlob                                                                                                        	| 2013-08-15를 포함한 모든 버전                                                                                                           	|



이러한 테이블은 저장소 계정에 대해 저장소 분석을 사용하도록 설정하면 자동으로 작성됩니다. 저장소 계정의 네임스페이스를 통해 이러한 테이블에 액세스할 수 있습니다(예: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`).


##메트릭 데이터 액세스

Azure 관리되는 라이브러리에서 제공하는 .NET API를 비롯한 테이블 서비스 API를 사용하면 메트릭 테이블의 모든 데이터에 액세스할 수 있습니다. 저장소 계정 관리자는 테이블 엔터티를 읽고 삭제할 수는 있지만 작성하거나 업데이트할 수는 없습니다.

##다음 단계
###개념
[저장소 분석 설정 및 구성](https://msdn.microsoft.com/library/hh360996.aspx)

[저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/hh343264.aspx)

[저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/hh343260.aspx)

[저장소 분석 로깅 정보](https://msdn.microsoft.com/library/hh343262.aspx)

###기타 리소스

[저장소 계정을 모니터링하는 방법](../how-to-monitor-a-storage-account) 
<!--HONumber=47-->
