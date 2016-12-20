---
title: " Azure Portal을 통해 스트리밍 끝점 크기 조정 | Microsoft 문서"
description: "이 자습서에서는 Azure 포털을 사용하여 스트리밍 끝점의 크기를 조정하는 단계를 안내합니다."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cc9b75557ffafb831d3271d36921bac3da9264ee


---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Azure 포털을 통해 스트리밍 끝점 크기 조정
## <a name="overview"></a>개요
> [!NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
> 
> 

Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 클라이언트에 적응 비트 전송률 스트리밍을 통해 비디오를 제공하는 것입니다. 미디어 서비스에서 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다.

Media Services는 적응 비트 전송률 MP4 인코딩 콘텐츠를 Media Services에서 적시에 지원되는 각 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍, HDS)의 다시 패키징된 버전을 저장하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

동적 패키징을 이용하려면 다음을 수행해야 합니다.

* mezzanine(원본) 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다(인코딩 단계는 이 자습서의 뒷부분에서 설명).  
* 콘텐츠를 배달하는 출발점이 될 *스트리밍 끝점* 에 하나 이상의 스트리밍 단위를 만듭니다. 아래 단계는 스트리밍 단위의 수를 변경하는 방법을 보여 줍니다.

동적 패키징에서는 단일 저장소 형식으로 파일을 저장하고 비용을 지불하기만 하면 됩니다. 그러면 미디어 서비스가 클라이언트의 요청에 따라 적절한 응답을 빌드 및 제공합니다.

또한 스트리밍 단위를 조정하여 증가하는 대역폭 요구를 처리하기 위해 스트리밍 끝점 서비스의 용량을 제어할 수 있습니다. 프로덕션 환경에서 응용 프로그램에 대한 하나 이상의 확장 단위를 할당하는 것이 좋습니다. 스트리밍 단위는 200Mbps 단위로 구입할 수 있는 전용 송신 용량 및 [동적 패키징](media-services-dynamic-packaging-overview.md), CDN 통합, 고급 구성을 포함하는 추가 기능을 모두 제공합니다. 자세한 내용은 [Azure 포털을 통해 스트리밍 끝점 관리](media-services-portal-manage-streaming-endpoints.md)를 참조하세요.

## <a name="scale-streaming-endpoints"></a>스트리밍 끝점 크기 조정
스트리밍 예약 단위의 수를 만들고 변경하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Media Services 계정을 선택합니다.
2. **설정** 창에서 **스트리밍 끝점**을 선택합니다.
3. 크기를 조정할 스트리밍 끝점을 클릭합니다. 
4. 슬라이더를 이동하여 스트리밍 단위 수를 지정합니다.

![스트리밍 끝점](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

고려 사항은 다음과 같습니다.

* 새 스트리밍 단위를 할당하는 작업은 완료하는 데 20분 정도 걸릴 수 있습니다. 
* 현재, 스트리밍 단위의 양수 값을 0으로 변경하면 최대 1시간 동안 주문형 스트리밍을 사용하지 않을 수 있습니다.
* 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다. 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.

## <a name="next-steps"></a>다음 단계
미디어 서비스 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO3-->


