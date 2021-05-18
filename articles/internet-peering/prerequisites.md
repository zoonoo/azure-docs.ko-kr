---
title: Microsoft와의 피어링을 설정하기 위한 필수 조건
titleSuffix: Azure
description: Microsoft와의 피어링을 설정하기 위한 필수 조건
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bc45bc8809eabe75902b602835ea595b56ff3cf8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586843"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Microsoft와의 피어링을 설정하기 위한 필수 조건

새 피어링을 요청하거나 레거시 피어링을 Azure 리소스로 변환하기 전에 아래의 사전 요구 사항이 충족되었는지 확인합니다.

## <a name="azure-related-prerequisites"></a>Azure 관련 사전 요구 사항
* **Microsoft Azure 계정:** Microsoft Azure 계정이 없으면 [Microsoft Azure 계정](https://azure.microsoft.com/free)을 만듭니다. 피어링은 Azure 구독 내의 리소스로 모델링되므로 피어링을 설정하려면 유효한 활성 Microsoft Azure 구독이 필요합니다. 다음 사항을 알아야 합니다.
    * 피어링을 설정하는 데 사용되는 Azure 리소스 유형은 항상 무료인 Azure 제품입니다. 즉, Azure 계정을 만들거나 구독을 만들거나 피어링을 설정하기 위해 Azure 리소스에 액세스하는 **PeerAsn** 및 **피어링** 에 대해서는 요금이 부과되지 않습니다. 이것은 사용자와 Microsoft 간 직접 피어링 계약과 혼동하면 안 됩니다. 해당 계약 조건은 피어링 팀과 명시적으로 논의됩니다. 이와 관련하여 문의사항이 있는 경우 [Microsoft 피어링](mailto:peering@microsoft.com)에 문의하시기 바랍니다.
    * 동일한 Azure 구독을 사용하여 무료 또는 유료인 다른 Azure 제품 또는 클라우드 서비스에 액세스 할 수 있습니다. 유료 제품에 액세스하면 요금이 부과됩니다.
    * 새 Azure 계정 및/또는 구독을 만드는 경우 평가판 기간 동안 Azure 클라우드 서비스를 사용해볼 수 있는 무료 Azure 크레딧을 받을 수 있습니다. 더 알고 싶으시다면 [Microsoft Azure 계정](https://azure.microsoft.com/free)에 방문하여 자세히 알아보세요.

* **피어 ASN 연결:** 피어링을 요청하기 전에 먼저 ASN 및 연락처 정보를 구독에 연결합니다. [피어 ASN을 Azure 구독에 연결](howto-subscription-association-powershell.md)의 지침을 따릅니다.

## <a name="other-prerequisites"></a>기타 사전 요구 사항
* **PeeringDB 프로필:** 피어는 [PeeringDB](https://www.peeringdb.com)에 완료되고 최신 상태의 프로필이 있어야 합니다. 등록 시스템에서 해당 정보를 사용하여 NOC 정보, 기술 담당자 연락처 정보 및 피어링 시설에서의 현재 상태와 같은 피어의 유효성을 검사합니다.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 직접 피어링 만들기 또는 수정](howto-direct-portal.md)
* [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)
* [포털을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)