---
title: IoT Edge 모듈 마켓플레이스 FAQ | Microsoft Docs
description: IoT Edge 모듈 마켓플레이스 FAQ
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2c2e7729eb70a5dd37dc4df10605eec9429e1043
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807465"
---
# <a name="iot-edge-module-marketplace-frequently-asked-questions"></a>IoT Edge 모듈 마켓플레이스 질문과 대답


## <a name="what-is-the-edge-module-marketplace"></a>IoT Edge 모듈 마켓플레이스란 무엇인가요?


IoT Edge 모듈 마켓플레이스는 Azure Marketplace를 통해 *검색할 수 있는*, 미리 작성된 *인증된* Edge 모듈 목록입니다.

![IoT Edge 모듈](./media/cloud-partner-portal-iot-edge-module-faq/iot-edge-modules.png)

## <a name="where-will-edge-modules-be-visible"></a>Edge 모듈은 어디서 볼 수 있나요? 


[Azure Portal 마켓플레이스](https://ms.portal.azure.com/)(인증된 환경)의 사물 인터넷 범주 아래에 “IoT Edge 모듈”로 태그가 지정되어 있습니다.

그리고 [Azure 마켓플레이스 웹 사이트](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/internet-of-things?page=1)(익명 환경)의 사물 인터넷 범주 아래에 “IoT Edge 모듈”로 태그가 지정되어 있습니다.

## <a name="is-it-open-to-partners"></a>파트너에게 열려 있나요?


아직 없습니다. 지금은 Microsoft에서 작성한 모듈만 마켓플레이스의 IoT Edge 섹션에 게시됩니다. 

## <a name="why-should-partners-publish-their-iot-edge-modules"></a>파트너가 해당 IoT Edge 모듈을 게시해야 하는 이유는 무엇인가요?


-   이 시장 진입 채널을 추가하고 솔루션을 전시하면 제품 가시성을 높일 수 있습니다.

-   더 많은 잠재 고객을 확보할 수 있습니다. Azure Marketplace에 참여하면 해당 솔루션에 관심이 있는 사용자에 대한 세부 정보를 얻을 수 있습니다.

-   누구보다 먼저, 더 많은 수익 창출 기능을 활용할 수 있습니다.

## <a name="what-is-the-onboarding-process"></a>온보딩 프로세스란 무엇인가요?


아직 시작되지는 않았지만, 온보딩 프로세스는 Azure Marketplace를 통해 수행됩니다. 자세한 지침은 [Azure Marketplace 및 AppSource 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)에 있습니다. 컨테이너의 트랜잭션 목록 유형을 참조하세요. 

파트너는 먼저 Azure Marketplace의 게시자가 되어야 합니다. 그런 다음, [Cloud 파트너 포털](./cloud-partner-portal-getting-started-with-the-cloud-partner-portal.md)을 통해 해당 Edge 모듈을 등록할 수 있습니다.

## <a name="are-there-any-monetization-capabilities"></a>수익 창출 기능이 있나요?


지금은 기본 제공되는 기능이 없습니다. 첫 번째 목표는 ‘무료’ 또는 ‘사용자 라이선스 필요’ Edge 모듈로 마켓플레이스를 시작하는 것입니다. 사용량 청구 모델과 같은 수익 창출 기능도 추가할 예정입니다.

## <a name="what-is-bring-your-own-license-byol"></a>BYOL(사용자 라이선스 필요)이란 무엇인가요?


[Microsoft Azure Marketplace 참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)의 공식 정의는 다음과 같습니다.

- ‘고객은 Azure Marketplace에서 제공하는 서비스에 액세스하거나 사용할 수 있는 권한을 Azure Marketplace 외부에서 얻었으며, 이러한 서비스 사용 시 Azure Marketplace 요금이 부과되지 않습니다.’

해당 소프트웨어의 사용을 허가하고 그에 대한 수익을 수금하는 것은 파트너의 책임입니다.

## <a name="can-partners-publish-a-freemium-module"></a>파트너가 “Freemium” 모듈을 게시할 수 있나요?


예, 무료지만 몇 가지 제한 사항이 있는 모듈인 Freemium 모듈은 다른 게시와 똑같이 취급됩니다.

## <a name="is-intellectual-property-protected"></a>지적 재산권이 보호되나요?


Edge 모듈은 Docker 호환 컨테이너입니다. 분산 컨테이너에 패키지된 IP(지적 재산권)를 보호하는 것은 파트너의 책임입니다.

## <a name="where-will-the-modules-be-hosted"></a>모듈은 어디에 호스트되나요?


IoT Edge 모듈은 Docker Hub와 같은, 익명으로 쿼리할 수 있는 Microsoft 소유 컨테이너 레지스트리에 호스트됩니다.

## <a name="what-are-the-integration-plans-between-the-azure-marketplace-and-the-azure-iot-tools"></a>Azure Marketplace와 Azure IoT 도구 간의 통합 플랜은 무엇인가요?

Azure Marketplace와 Azure IoT 도구 간에 보다 긴밀한 통합을 구축할 예정입니다. 고려하고 있는 통합의 예로는 IoT Hub 포털이나 Visual Studio Code에서 바로 IoT Edge 모듈 마켓플레이스를 검색할 수 있도록 하는 것 등이 있습니다.

## <a name="which-operating-system-or-architecture-should-my-container-support"></a>내 컨테이너가 지원해야 하는 운영 체제 또는 아키텍처는 무엇인가요?

IoT Edge 모듈은 프로덕션의 IoT Edge와 동일한 OS/아키텍처 행렬을 지원해야 합니다. 이 목록은 [Azure IoT Edge 지원](https://docs.microsoft.com/azure/iot-edge/support)에서 유지 관리됩니다. 예를 들어, 모듈은 현재 Linux x64 및 Linux ARM32를 지원해야 합니다.

## <a name="are-there-any-other-certification-constraints-to-be-aware-of"></a>주의해야 하는 다른 인증 제약 조건이 있나요?

정확한 인증 제약 조건 집합은 여전히 작업 중입니다. 기본 원칙은 다음과 같습니다.

-   수량보다 품질 우선 홍보

-   IoT Edge 특정 컨테이너(임의 컨테이너 아님)

-   프로덕션 등급

-   원클릭 배포(기본 구성 값 집합이 하나 이상 제공됨)

## <a name="any-other-questions"></a>기타 궁금한 점이 있는 경우


[Azure IoT Edge 온보딩](mailto:azureiotedgeonboarding@service.microsoft.com)에 문의하세요.
