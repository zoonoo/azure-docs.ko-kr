<properties 
	pageTitle="미디어 서비스 계정 모니터링" 
	description="Azure에서 미디어 서비스 계정에 대해 모니터링을 구성하는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="juliako"/>

#<a id="monitormediaservicesaccount"></a>미디어 서비스 계정을 모니터링하는 방법

Azure 미디어 서비스 대시보드는 사용량 메트릭 및 미디어 서비스 계정을 관리하는 데 사용할 수 있는 계정 정보를 보여 줍니다.

인코더의 입력 및 출력 데이터가 표시하는 대기 중인 인코딩 작업, 실패한 인코딩 작업, 활성 인코딩 작업의 수 및 미디어 서비스 계정과 관련된 Blob 저장소 사용량을 모니터링할 수 있습니다. 또한 고객에게 콘텐츠를 스트리밍하는 경우 다양한 스트리밍 메트릭을 검색할 수도 있습니다. 최근 6시간, 24시간 또는 7일 동안의 데이터를 모니터링하도록 선택할 수 있습니다.
 
>[AZURE.NOTE] Azure 클래식 포털에서 저장소 데이터를 모니터링하면 추가 비용이 발생할 수 있습니다. 자세한 내용은 [저장소 분석 및 청구](http://go.microsoft.com/fwlink/?LinkId=256667)를 참조하십시오.

##<a id="configuremonitoring"></a>방법: 미디어 서비스 계정 모니터링

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/?LinkID=256666)에서 **미디어 서비스**를 클릭한 후 대시보드를 열 미디어 서비스 계정 이름을 클릭합니다. 

	![MediaServices\_Dashboard][dashboard]

2. 인코딩 작업 또는 데이터를 모니터링하려면 인코딩 작업을 미디어 서비스에 제출하기 시작하거나 Azure 미디어 주문형 스트리밍을 사용하여 고객에게 콘텐츠를 스트리밍하기 시작하면 됩니다. 약 1시간 후에 대시보드에 모니터링 데이터가 표시되기 시작합니다.

##<a id="configuringstorage"></a>방법: Blob 저장소 사용 모니터링(선택 사항)
1. **간략 상태** 섹션에서 **저장소 계정** 이름을 클릭합니다.
2. 저장소 계정 페이지에서 **configure page** 링크를 클릭하고 아래와 같이 Blob, 테이블 및 큐 서비스의 **모니터링** 설정까지 아래로 스크롤합니다.

	>[AZURE.NOTE] 미디어 서비스에서 지원되는 저장소 유형은 Blob뿐입니다.

	![StorageOptions][storage_options_scoped]

3. **모니터링**에서 다음과 같이 Blob의 모니터링 수준 및 데이터 보존 정책을 설정합니다.

-  모니터링 수준을 설정하려면 다음 중 하나를 선택합니다.

      **최소** - 수신/송신, 가용성, 대기 시간, 성공 비율 등 Blob, 테이블 및 큐 서비스에 대해 집계되는 메트릭을 수집합니다.

      **세부 정보 표시** - 최소 메트릭뿐 아니라 Azure 저장소 서비스 API의 각 저장소 작업에 대한 동일한 메트릭 집합을 수집합니다. 세부 정보 표시 메트릭을 사용하면 응용 프로그램 작업 중 발생하는 문제를 더 면밀하게 분석할 수 있습니다.

      **해제** - 모니터링을 해제합니다. 기존 모니터링 데이터는 보존 기간의 끝까지 저장됩니다.

- 데이터 보존 정책을 설정하려면 **보존(일)**에 1 - 365일 중 보존할 데이터 기간(일수)을 입력합니다. 보존 정책을 설정하지 않으려면 0을 입력합니다. 보존 정책이 없는 경우 언제든 모니터링 데이터를 삭제할 수 있습니다. 오래되고 사용하지 않는 분석 데이터를 비용 부담 없이 시스템에서 삭제할 수 있도록 계정의 저장소 분석 데이터를 보존하려는 기간에 따라 보존 정책을 설정하는 것이 좋습니다.

4. 모니터링 구성을 완료한 경우 **저장**을 클릭합니다. 미디어 서비스 메트릭과 비슷하게, 약 1시간 후에 대시보드에 모니터링 데이터가 표시되기 시작합니다. 메트릭은 저장소 계정에서 이름이 $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue 및 $MetricsCapacityBlob인 네 가지 테이블에 저장됩니다. 자세한 정보는 [저장소 분석 메트릭](http://go.microsoft.com/fwlink/?LinkId=256668)(영문)을 참조하세요.



##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]주문형 스트리밍 워크플로](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png

 

<!---HONumber=AcomDC_0420_2016-->