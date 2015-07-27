<properties 
	pageTitle="인코딩 예약 단위 크기를 조정하는 방법" 
	description="계정에서 프로비전할 주문형 스트리밍 예약 단위 및 인코딩 예약 단위의 수를 지정하여 미디어 서비스를 확장하는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>


#인코딩 크기를 조정하는 방법

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다.

##개요

미디어 서비스 계정은 인코딩 작업을 처리하는 속도를 결정하는 예약 단위 유형에 연관됩니다. **Basic**, **Standard**, 또는 **Premium** 예약 단위 유형 중에서 선택할 수 있습니다. 예를 들어 **Standard** 예약 단위 유형을 사용하는 경우 **Basic** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다. 자세한 내용은 [인코딩 예약 단위 유형(영문)](http://azure.microsoft.com/blog/author/milanga)을 참조하세요.

예약 단위 유형을 지정하는 것 외에도 인코딩 예약 단위를 사용하여 계정을 프로비전하도록 지정할 수 있습니다. 프로비전된 인코딩 예약 단위의 수는 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수를 결정합니다. 예를 들어 계정에 5개의 예약 단위가 있는 경우 처리할 미디어 작업이 있다면 5개의 미디어 작업이 동시에 실행됩니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되자마자 순차적으로 처리를 위해 선택됩니다. 계정에 프로비전된 예약 단위가 없는 경우에는 작업이 순차적으로 선택됩니다. 이 경우 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

예약 단위 유형 및 인코딩 예약 단위 수를 변경하려면 다음을 수행합니다.

1. [관리 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.

2. **ENCODING** 페이지를 선택합니다.

	**예약 단위 유형**을 변경하려면, BASIC, STANDARD 또는 PREMIUM을 누릅니다.

	선택한 예약 단위 유형에 대한 예약 단위 수를 변경하려면 **인코딩** 슬라이더를 사용합니다.
	
	
	![프로세서 페이지](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)

	  
	>[Azure.Note]싱가포르, 홍콩, 오사카, 베이징, 상하이 데이터 센터에서는 프리미엄 예약 단위 유형을 제공 하지 않습니다.

3. 저장 단추를 눌러 변경 내용을 저장합니다.

	저장을 누르는 즉시 새 인코딩 예약 단위가 할당됩니다.

	>[Azure.Note]24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다.

##할당량 및 제한 사항

할당량 및 제한 사항과 지원 티켓을 여는 방법에 대한 자세한 내용은 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조하세요.




 

<!---HONumber=July15_HO3-->