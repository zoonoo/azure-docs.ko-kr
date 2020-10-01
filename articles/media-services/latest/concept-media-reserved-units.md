---
title: 미디어 예약 단위-Azure | Microsoft Docs
description: 미디어 예약 단위를 사용 하면 미디어 프로세스를 확장 하 고 미디어 처리 작업의 속도를 결정할 수 있습니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: df691f5b9e74432f683e52fbb896dd4d44efe2aa
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91622088"
---
# <a name="media-reserved-units"></a>미디어 예약 단위

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용 하면 Mru (미디어 예약 단위)를 관리 하 여 미디어 처리를 확장할 수 있습니다. MRU는 미디어 인코딩에 필요한 추가 컴퓨팅 용량을 제공 합니다. Mru 수는 미디어 작업이 처리 되는 속도와 계정에서 동시에 처리할 수 있는 미디어 작업의 수를 결정 합니다. 예를 들어 계정에 5 개의 Mru 있고 처리할 태스크가 있는 경우 5 개의 미디어 작업이 동시에 실행 될 수 있습니다. 나머지 작업은 큐에 대기 되며 실행 중인 작업이 완료 되 면 순차적으로 처리 하도록 선택할 수 있습니다. 프로 비전 하는 각 MRU는 용량 예약을 생성 하지만 전용 리소스를 제공 하지 않습니다. 요구가 매우 높은 시간 동안 모든 Mru는 즉시 처리를 시작 하지 않을 수 있습니다.

작업은 자산에 대 한 개별 작업 (예: 적응 스트리밍 인코딩)입니다. 작업을 제출할 때 Media Services는 개별 작업 (예: 작업)으로 작업을 분리 하 여 별도의 Mru 연결 합니다.

## <a name="choosing-between-different-reserved-unit-types"></a>여러 예약 단위 유형 중에서 선택

다음 표를 참조하여 여러 인코딩 속도 중에서 선택할 때 적절한 속도를 결정할 수 있습니다.  사용 된 MRU에 따라 7 분 1080p video에 대 한 인코딩 기간을 보여 줍니다.

|RU 유형|시나리오|7분 1080p 비디오에 대한 결과 예 |
|---|---|---|
| **S1**|단일 비트 전송률 인코딩 <br/>SD 이하 해상도의 파일, 시간이 중요하지 않은 인코딩, 저가형 비디오.|"H264 단일 비트 전송률 SD 16x9"를 사용 하 여 단일 비트 전송률 SD 확인 MP4 파일로 인코딩하는 데 약 7 분이 걸립니다.|
| **S2**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>SD 및 HD 인코딩에서 모두 일반적으로 사용됨|"H264 단일 비트 전송률 720p" 사전 설정을 사용 하는 인코딩은 약 6 분 정도 걸립니다.<br/><br/>"H264 다중 비트 전송률 720p" 사전 설정을 사용 하는 인코딩은 약 12 분이 소요 됩니다.|
| **S3**|단일 비트 전송률 및 다중 비트 전송률 인코딩.<br/>Full HD 및 4K 해상도 비디오. 시간이 중요하며 소요 시간이 짧은 인코딩|"H264 단일 비트 전송률 1080p" 사전 설정을 사용 하는 인코딩은 3 분 정도 걸립니다.<br/><br/>"H264 다중 비트 전송률 1080p" 미리 설정을 사용하여 인코딩할 때 약 8분이 걸립니다.|

> [!NOTE]
> 계정에 대 한 MRU를 프로 비전 하지 않으면 미디어 작업은 S1 MRU의 성능으로 처리 되 고 작업은 순차적으로 선택 됩니다. 처리 용량이 예약 되어 있지 않으므로 한 작업을 완료 하는 대기 시간과 그 다음으로 시작 하는 대기 시간은 시스템의 리소스 가용성에 따라 달라 집니다.

## <a name="considerations"></a>고려 사항

* Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 S3 단위 10 개를 사용 하 여 계정을 프로 비전 하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.
* Mru 없는 인코딩 작업의 경우 작업이 대기 상태에서 소비할 수 있는 시간에 대 한 상한이 없으며 한 번에 하나의 작업만 실행 됩니다.

## <a name="billing"></a>결제

실행 중인 작업이 있는지 여부에 관계 없이 계정에서 미디어 예약 단위가 프로 비전 되는 시간 (분)을 기준으로 요금이 청구 됩니다. 자세한 내용은 [Media Services 가격 책정](https://azure.microsoft.com/pricing/details/media-services/) 페이지에서 FAQ 섹션을 참조하세요.

## <a name="next-step"></a>다음 단계
[CLI를 사용 하 여 미디어 예약 단위 크기 조정](media-reserved-units-cli-how-to.md) 
 [비디오 분석](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>참조

* [할당량 및 제한](limits-quotas-constraints.md)
