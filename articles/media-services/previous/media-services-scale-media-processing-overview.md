---
title: 미디어 예약 단위 개요 | Microsoft Docs
description: 이 문서에서는 Azure Media Services를 사용하여 미디어 처리를 스케일링하는 방법을 대략적으로 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012720"
---
# <a name="media-reserved-units"></a>미디어 예약 단위

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services를 사용하면 MRU(미디어 예약 단위)를 관리하여 미디어 처리의 크기를 조정할 수 있습니다. MRU는 미디어 인코딩에 필요한 추가 컴퓨팅 용량을 제공합니다. MRU 개수에 따라 미디어 작업이 처리되는 속도와 계정에서 동시에 처리할 수 있는 미디어 작업의 개수가 달라집니다. 예를 들어 계정에 5개의 MRU가 있는 경우 처리할 작업이 있다면 5개의 미디어 작업이 동시에 실행될 수 있습니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되면 순차적으로 처리됩니다. MRU를 프로비저닝할 때마다 용량이 예약되지만 전용 리소스는 제공되지 않습니다. 수요가 매우 높은 경우에는 모든 MRU가 즉시 처리되지 않을 수 있습니다.

## <a name="choosing-between-different-reserved-unit-types"></a>여러 예약 단위 유형 중에서 선택

다음 표를 참조하여 여러 인코딩 속도 중에서 선택할 때 적절한 속도를 결정할 수 있습니다.  사용된 MRU에 따라 7분간 1080p 비디오에 대한 인코딩 시간을 보여 줍니다.

|RU 유형|시나리오|7분 1080p 비디오에 대한 결과 예 |
|---|---|---|
| **S1**|단일 비트 전송률 인코딩 <br/>SD 이하 해상도의 파일, 시간이 중요하지 않은 인코딩, 저가형 비디오.|“H264 단일 비트 전송률 SD 16x9”를 사용하여 단일 비트 전송률 SD 해상도 MP4 파일을 인코딩하면 약 7분이 걸립니다.|
| **S2**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>SD 및 HD 인코딩에서 모두 일반적으로 사용됨|“H264 단일 비트 전송률 720p” 미리 설정을 사용하여 인코딩할 때 약 6분이 걸립니다.<br/><br/>“H264 다중 비트 전송률 720p” 미리 설정을 사용하여 인코딩할 때 약 12분이 걸립니다.|
| **S3**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>Full HD 및 4K 해상도 비디오. 시간이 중요하며 소요 시간이 짧은 인코딩|“H264 단일 비트 전송률 1080p” 미리 설정을 사용하여 인코딩할 때 약 3분이 걸립니다.<br/><br/>"H264 다중 비트 전송률 1080p" 미리 설정을 사용하여 인코딩할 때 약 8분이 걸립니다.|

> [!NOTE]
> 계정에 대한 MRU를 프로비저닝하지 않으면 미디어 작업은 S1 MRU의 성능으로 처리되고 작업이 순차적으로 처리됩니다. 처리 용량이 예약되지 않으므로 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

## <a name="considerations"></a>고려 사항

* Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 단위를 사용하여 계정을 프로비저닝하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.
* MRU가 없는 작업 인코딩을 위해, 작업이 대기 상태로 유지될 수 있는 시간의 상한은 없으며 작업은 한 번에 최대 하나만 실행됩니다.

## <a name="billing"></a>결제

계정에 미디어 예약 단위를 프로비저닝한 시간(분)을 기준으로 요금이 부과됩니다. 계정에서 실행 중인 작업이 있는지 여부와 관계없이 요금이 부과됩니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.

## <a name="quotas-and-limitations"></a>할당량 및 제한 사항

할당량 및 제한 사항과 지원 티켓을 여는 방법에 대한 자세한 내용은 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 기술 중 하나를 사용하여 미디어 처리 스케일링을 시도합니다.

[.NET](media-services-dotnet-encoding-units.md)
[Portal](media-services-portal-scale-media-processing.md)
[REST](/rest/api/media/operations/encodingreservedunittype)
[Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
[PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)