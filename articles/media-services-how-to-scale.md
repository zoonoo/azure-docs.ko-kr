<properties 
	pageTitle="미디어 서비스를 확장하는 방법" 
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
	ms.date="02/20/2015" 
	ms.author="juliako"/>


# 미디어 서비스를 확장하는 방법  

## 개요

계정에서 프로비전할 **스트리밍 예약 단위** 및 **인코딩 예약 단위**의 수를 지정하여 **미디어 서비스**를 확장할 수 있습니다. 

또한 저장소 계정을 추가하여 미디어 서비스 계정을 확장할 수 있습니다. 각 저장소 계정은 500TB로 제한됩니다. 여러 저장소 계정을 단일 미디어 서비스 계정에 연결하여 기본 제한 이상으로 저장소를 확장할 수 있습니다.

이 항목은 관련 항목으로 연결됩니다.

## <a id="streaming_endpoins"></a>스트리밍 예약 단위

자세한 내용은 [스트리밍 단위 크기 조정을 참조하세요.](media-services-manage-origins.md#scale_streaming_endpoints).

## <a id="encoding_reserved_units"></a>인코딩 예약 단위

인코딩 단위 크기를 조정하는 방법에 대한 내용은 **포털** 및 **.NET** 항목을 참조하세요.

[AZURE.INCLUDE [media-services-selector-scale-encoding-units](../includes/media-services-selector-scale-encoding-units.md)]

예약 단위는 인코딩 및 인덱싱 작업에서 동일합니다.

## <a id="storage"></a>저장소 크기 조정

자세한 내용은 [여러 저장소 계정에서 미디어 서비스 자산 관리](https://msdn.microsoft.com/library/azure/dn271889.aspx) 및 [Azure 저장소 작업](https://msdn.microsoft.com/library/azure/dn767951.aspx)을 참조하세요.




<!--HONumber=52-->