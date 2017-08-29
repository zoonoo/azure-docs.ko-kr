---
title: "인코딩 단위를 추가하여 미디어 처리 크기 조정 - Azure | Microsoft Docs"
description: ".NET을 사용하여 인코딩 단위를 추가하는 방법 알아보기"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;milangada;
ms.translationtype: Human Translation
ms.sourcegitcommit: bdf41edfa6260749a91bc52ec0a2b62fcae99fb0
ms.openlocfilehash: ee40b92c48d91a391fe72582c7ea5e244907747f
ms.contentlocale: ko-kr
ms.lasthandoff: 01/27/2017

---
# <a name="how-to-scale-encoding-with-net-sdk"></a>.NET SDK를 사용하여 인코딩 크기를 조정하는 방법
> [!div class="op_single_selector"]
> * [포털](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>개요
> [!IMPORTANT]
> 미디어 처리 크기 조정에 대해 자세히 알아보려면 이 [개요](media-services-scale-media-processing-overview.md) 항목을 참조하세요.
> 
> 

.NET SDK를 사용하여 예약 단위 유형 및 인코딩 예약 단위 수를 변경하려면 다음을 수행합니다.

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>지원 티켓 열기
기본적으로 모든 미디어 서비스 계정은 인코딩 예약 단위 최대 25개, 주문형 스트리밍 예약 단위 최대 5개까지 확장할 수 있습니다. 지원 티켓을 열어 더 높은 한도를 요청할 수 있습니다.

### <a name="open-a-support-ticket"></a>지원 티켓 열기
지원 티켓을 열려면 다음을 수행합니다.

1. [지원 받기](https://manage.windowsazure.com/?getsupport=true)를 클릭합니다. 로그인하지 않은 경우 자격 증명을 입력하라는 메시지가 표시됩니다.
2. 사용 중인 구독을 선택합니다.
3. 지원 유형으로 "기술적"을 선택합니다.
4. "티켓 만들기"를 클릭합니다.
5. 다음 페이지에 나타나는 제품 목록에서 "Azure 미디어 서비스"를 선택합니다.
6. 사용자의 문제에 적절한 "문제 유형"을 선택합니다.
7. 계속을 클릭합니다.
8. 다음 페이지의 지시에 따라 문제에 대한 세부 정보를 입력합니다.
9. 제출을 클릭하여 티켓을 엽니다.

## <a name="media-services-learning-paths"></a>미디어 서비스 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


