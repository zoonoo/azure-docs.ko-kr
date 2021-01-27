---
title: Azure Media Services V2와 V3 사이의 기능 차이
description: 이 문서에서는 Azure Media Services v 2에서 v 2로의 기능 차이에 대해 설명 합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 2fa827bc2841a0bae4c9646c8a70e42dc2b500e3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898412"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Azure Media Services V2와 V3 사이의 기능 차이

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2 단계](./media/migration-guide/steps-2.svg)

마이그레이션 지침의이 부분에서는 V2와 V3 Api 간의 차이점에 대 한 자세한 정보를 제공 합니다.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>V2와 V3 Api 간의 기능 차이

V3 API는 V2 API를 사용 하 여 다음과 같은 기능을 사용 합니다. V2 Api의 Media Encoder Standard에 대 한 몇 가지 고급 기능은 현재 V3에서 사용할 수 없습니다.

- 입력에 오디오가 없을 때 자동 오디오 트랙 삽입 (Azure Media Player에 더 이상 필요 하지 않음)

- 입력에 비디오가 없을 때 비디오 트랙을 삽입 합니다.

- 트랜스 코딩을 사용 하는 라이브 이벤트는 현재 슬레이트 삽입 중간 스트림 및 API 호출을 통한 광고 표식 삽입을 지원 하지 않습니다.

- Azure Media Premium 인코더는 더 이상 V2에서 지원 되지 않습니다. 8 비트 HEVC 인코딩에 사용 하는 경우 표준 인코더에서 새로운 HEVC 지원을 사용 합니다. 
    - 표준 인코더에 대 한 오디오 채널 매핑에 대 한 지원이 추가 되었습니다.  [Media Services Encoding Swagger 설명서에서 오디오를](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)참조 하세요.
    - MXF 또는 ProRes와 같은 타사 라이선스 제품의 고급 기능 또는 출력 형식을 사용 하는 경우 Telestream의 Azure 파트너 솔루션을 사용 합니다 .이 솔루션은 V2 사용 중지 시점에 트랜잭션 됩니다. 또는에 대 한 통신을 사용 하거나 [Bitmovin](http://bitmovin.com)사용할 수 있습니다.

- V 2의 스트리밍 끝점에 대 한 "가용성 집합" 속성은 더 이상 지원 되지 않습니다. V3 API에서 샘플 프로젝트 및 [고가용성 VOD](https://docs.microsoft.com/azure/media-services/latest/media-services-high-availability-encoding) 배달에 대 한 지침을 참조 하세요.

- Media Services V3에서는 FairPlay IV를 지정할 수 없습니다. 패키징 및 라이선스 배달 모두에 대해 Media Services를 사용 하는 고객에 게는 영향을 주지 않지만 타사 DRM 시스템을 사용 하 여 FairPlay 라이선스 (하이브리드 모드)를 제공할 때 문제가 될 수 있습니다.

- 미사용 자산의 보호를 위한 클라이언트 쪽 저장소 암호화가 V3 API에서 제거 되 고 미사용 데이터에 대 한 storage 서비스 암호화로 대체 되었습니다. V3 Api는 기존의 저장소 암호화 된 자산을 계속 사용할 수 있지만 새로 만들 수는 없습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
