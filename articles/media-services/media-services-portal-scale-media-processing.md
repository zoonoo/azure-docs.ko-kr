---
title: "Azure Portal을 사용하여 미디어 처리 크기 조정 | Microsoft Docs"
description: "이 자습서에서는 Azure Portal을 사용하여 미디어 처리의 크기를 조정하는 단계를 안내합니다."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: e6ef52f7aee8b2a0d0dd6ebc99ad7a8c5c11f525
ms.contentlocale: ko-kr
ms.lasthandoff: 07/06/2017

---
# <a name="change-the-reserved-unit-type"></a>예약 단위 유형 변경
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [포털](media-services-portal-scale-media-processing.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>개요

미디어 서비스 계정은 미디어 처리 작업을 처리하는 속도를 결정하는 예약 단위 형식과 연결됩니다. **S1**, **S2**, **S3** 예약 단위 유형 중에서 선택할 수 있습니다. 예를 들어 **S2** 예약 단위 유형을 사용하는 경우 **S1** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다.

예약 단위 유형을 지정하는 것 외에도 계정에 **RU(예약 단위)**를 프로비전하도록 지정할 수 있습니다. 프로비전되는 RU의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다.

>[!NOTE]
>RU는 Azure Media Indexer를 사용하는 인덱싱 작업을 비롯하여 모든 미디어 처리 병렬화에 대해 작동합니다. 그러나 인코딩과 달리 인덱싱 작업은 예약 단위가 더 빠르게 실행되어도 더 빨리 처리되지 않습니다.

> [!IMPORTANT]
> 미디어 처리 크기 조정에 대해 자세히 알아보려면 이 [개요](media-services-scale-media-processing-overview.md) 항목을 참조하세요.
> 
> 

## <a name="scale-media-processing"></a>미디어 처리 크기 조정
예약 단위 유형 및 예약 단위 수를 변경하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 창에서 **미디어 예약 단위**를 선택합니다.
   
    선택한 예약 단위 유형의 예약 단위 수를 변경하려면 **미디어 예약 단위** 슬라이더를 사용합니다.
   
    **예약 단위 형식**을 변경하려면 S1, S2 또는 S3을 누릅니다.
   
    ![프로세서 페이지](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. 저장 단추를 눌러 변경 내용을 저장합니다.
   
    저장을 누르면 새 예약 단위가 할당됩니다.

## <a name="next-steps"></a>다음 단계
미디어 서비스 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


