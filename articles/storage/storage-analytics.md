<properties 
	pageTitle="저장소 분석" 
	description="Blob, 큐, 테이블 및 파일 서비스의 동시성을 관리하는 방법에 대해 알아봅니다." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/08/2014" 
	ms.author="tamram"/>

# 저장소 분석

Azure 저장소 분석은 로깅을 수행하며 저장소 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 서비스에 대한 문제를 진단할 수 있습니다.

저장소 분석을 사용하려면 모니터링할 각 서비스에 대해 저장소 분석을 개별적으로 사용하도록 설정해야 합니다. [Azure 관리 포털](https://manage.windowsazure.com/)에서 저장소 분석을 사용하도록 설정할 수 있습니다. 자세한 내용은 [저장소 계정을 모니터링하는 방법](http://www.azure.com/manage/services/storage/how-to-monitor-a-storage-account/)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. [Blob 서비스 속성 가져오기](https://msdn.microsoft.com/ko-kr/library/hh452239.aspx), [큐 서비스 속성 가져오기](https://msdn.microsoft.com/ko-kr/library/hh452243.aspx) 및 [테이블 서비스 속성 가져오기](https://msdn.microsoft.com/ko-kr/library/hh452238.aspx) 작업을 사용하여 각 서비스에 대해 저장소 분석을 사용하도록 설정합니다.

집계된 데이터는 알려진 로깅용 Blob 및 알려진 메트릭용 테이블에 저장됩니다. Blob 서비스 및 테이블 서비스 API를 사용하면 이러한 Blob와 테이블에 액세스할 수 있습니다.

저장소 분석에 저장되는 데이터의 양은 20TB로 제한됩니다. 이 제한은 총 저장소 계정 제한과 관계없이 적용됩니다. 청구 및 데이터 보존 정책에 대한 자세한 내용은 [저장소 분석 및 청구](https://msdn.microsoft.com/library/hh360997.aspx)를 참조하세요. 저장소 계정 제한에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](https://msdn.microsoft.com/library/dn249410.aspx)를 참조하세요.

저장소 분석 및 기타 도구를 사용하여 Azure 저장소 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 가이드는 [Microsoft Azure 저장소 모니터링, 진단 및 문제 해결](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/)을 참조하세요.



<!--HONumber=47-->
 