<properties 
	pageTitle="저장소 분석 및 청구" 
	description="저장소 분석을 활용하여 저장소 서비스에 대한 청구 방식을 보다 명확하게 이해하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>


# 저장소 분석 및 청구

이 항목에서는 저장소 분석 비용 및 메트릭과 로깅 데이터를 사용하여 저장소 서비스에 대한 청구 방식을 파악하는 방법을 설명합니다.


## 저장소 분석 비용

저장소 분석은 저장소 계정 소유자가 사용하도록 설정하며 기본적으로 사용하도록 설정되어 있지는 않습니다. 모든 메트릭 데이터는 저장소 계정 서비스를 통해 작성됩니다. 따라서 저장소 분석에서 수행하는 각 쓰기 작업에 대해 요금이 청구됩니다. 또한 메트릭 데이터에 사용되는 저장소에도 요금이 청구됩니다.

저장소 분석에서 수행하는 다음 작업에 대해 요금이 청구될 수 있습니다.

- 로깅용 Blob 작성 요청

- 메트릭용 테이블 엔터티 작성 요청

데이터 보존 정책을 구성한 경우에는 저장소 분석에서 이전 로깅 및 메트릭 데이터를 삭제할 때 삭제 트랜잭션에 대해 요금이 부과되지 않습니다. 그러나 클라이언트의 삭제 트랜잭션에는 요금이 청구됩니다. 보존 정책에 대한 자세한 내용은 [저장소 분석 데이터 보존 정책 설정](https://msdn.microsoft.com/library/azure/hh343263.aspx)을 참조하세요.

## 청구 가능한 요청 이해

계정의 저장소 서비스에 대한 모든 요청에는 요금이 청구될 수도 있고 청구되지 않을 수도 있습니다. 저장소 분석에서는 요청 처리 방법을 나타내는 상태 메시지를 포함하여 서비스에 대한 개별 요청을 기록합니다. 마찬가지로 서비스 및 해당 서비스의 API 작업에 대한 메트릭도 저장됩니다. 여기에는 특정 상태 메시지의 개수와 비율이 포함됩니다. 이러한 기능을 함께 사용하면 요금이 청구될 수 있는 요청을 분석하고 응용 프로그램을 개선할 수 있으며 서비스에 대한 요청의 문제를 진단할 수 있습니다. 청구에 대한 자세한 내용은 [Azure 저장소 청구 이해 - 대역폭, 트랜잭션 및 용량](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)을 참조하세요.

저장소 분석 데이터를 확인할 때는 [저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/azure/hh343260.aspx) 항목의 표를 통해 요금이 청구될 수 있는 요청을 확인할 수 있습니다. 그런 후에 실제 로그 및 메트릭 데이터를 상태 메시지와 비교하여 특정 요청에 대해 요금이 부과되었는지 파악할 수 있습니다. 또한 위 항목의 표를 통해 저장소 서비스 또는 개별 API 작업의 사용 가능 여부도 조사할 수 있습니다.

## 다음 단계
[저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/azure/hh343260.aspx)

[저장소 분석 로깅 정보](https://msdn.microsoft.com/library/azure/hh343262.aspx) 

[저장소 분석 메트릭 정보](../storage-about-analytics-metrics.md) 

<!--HONumber=47-->
