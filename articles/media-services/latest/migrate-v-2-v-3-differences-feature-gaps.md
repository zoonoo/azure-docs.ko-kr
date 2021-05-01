---
title: Azure Media Services V2와 V3 사이의 기능 차이
description: 이 문서에서는 Azure Media Services v2와 v3의 기능 차이점을 설명합니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 564f3127fc6901695890daa520152a7aa1a2337f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962971"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Azure Media Services V2와 V3 사이의 기능 차이

![마이그레이션 가이드 로고](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![마이그레이션 2단계](./media/migration-guide/steps-2.svg)

마이그레이션 지침의 이 부분에서는 V2와 V3 API 간의 차이점에 대한 자세한 정보를 제공합니다.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>V2와 V3 API 간의 기능 차이

V3 API는 V2 API와 비교하여 다음과 같은 기능 차이가 있습니다. 다음과 같은 V2 API의 Media Encoder Standard 유용한 기능 중 몇 가지는 현재 V3에서 사용할 수 없습니다.

- 입력에 오디오가 없을 때 자동 오디오 트랙을 삽입하는 것은 Azure Media Player에서 더 이상 필요하지 않기 때문입니다.

- 입력에 비디오가 없을 때 비디오 트랙 삽입

- 코드 변환을 사용하는 라이브 이벤트는 현재 슬레이트 삽입 중간 스트림 및 API 호출을 통한 광고 표시기 삽입을 지원하지 않습니다.

- Azure Media Encoder Premium은 더 이상 V2에서 지원되지 않습니다. 8비트 HEVC 인코딩에 사용하는 경우 표준 인코더에서 새로운 HEVC 지원을 사용합니다. 
    - 표준 인코더에 오디오 채널 매핑 지원이 추가되었습니다.  [Media Services Encoding Swagger 설명서에서 오디오](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json)를 참조하세요.
    - MXF, ProRes와 같은 타사 라이선스 제품의 유용한 기능 또는 출력 형식을 사용하는 경우 Telestream의 Azure 파트너 솔루션을 사용합니다. 이 솔루션은 V2 사용 중지 시점에 트랜잭션됩니다. 또는 Imagine Communications나 [Bitmovin](http://bitmovin.com)을 사용할 수 있습니다.

- V2의 스트리밍 엔드포인트에 대한 "가용성 집합" 속성은 더 이상 지원되지 않습니다. V3 API에서 샘플 프로젝트 및 [고가용성 VOD](./architecture-high-availability-encoding-concept.md) 배달에 대한 지침을 참조하세요.

- Media Services V3에서는 FairPlay IV를 지정할 수 없습니다. 패키징 및 라이선스 배달 모두에 대해 Media Services를 사용하는 고객에게는 영향을 주지 않지만 타사 DRM 시스템을 사용하여 FairPlay 라이선스(하이브리드 모드)를 제공할 때에는 문제가 될 수 있습니다.

- 미사용 자산의 보호를 위한 클라이언트 쪽 스토리지 암호화가 V3 API에서 제거되어 미사용 데이터에 대한 스토리지 서비스 암호화로 대체되었습니다. V3 API는 기존의 암호화된 스토리지 자산을 계속 사용할 수 있지만 새로 만들 수는 없습니다.

## <a name="terminology-and-entity-changes"></a>용어 및 엔터티 변경

API에 대한 추가 변경 사항은 [용어 및 엔터티](migrate-v-2-v-3-differences-terminology.md) 변경을 참조하세요.
