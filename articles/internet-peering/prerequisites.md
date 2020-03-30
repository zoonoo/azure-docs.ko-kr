---
title: Microsoft와의 피어링을 설정하기 위한 필수 조건
titleSuffix: Azure
description: Microsoft와의 피어링을 설정하기 위한 필수 조건
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 3c820a7be561aeef9b7e50fd0ac0cf4dee721af8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775408"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Microsoft와의 피어링을 설정하기 위한 필수 조건

새 피어링을 요청하거나 레거시 피어링을 Azure 리소스로 변환하기 전에 아래 필수 구성 조건이 충족되었는지 확인합니다.

## <a name="azure-related-prerequisites"></a>Azure 관련 전제 조건
* **마이크로소프트 Azure 계정:** Microsoft Azure 계정이 없는 경우 [Microsoft Azure 계정을](https://azure.microsoft.com/free)만듭니다. 피어링이 Azure 구독 내의 리소스로 모델링되기 때문에 피어링을 설정하려면 유효하고 활성Microsoft Azure 구독이 필요합니다. 다음 사항을 알아야 합니다.
    * 피어링을 설정하는 데 사용되는 Azure 리소스 유형은 항상 무료 Azure 제품입니다( 즉, Azure 계정을 만들거나 구독을 만들거나 피어링을 설정하기 위해 Azure 리소스 **PeerAsn** 및 **피어링에** 액세스하는 것에 대한 요금이 부과되지 않음). 이는 귀하와 Microsoft 간의 직접 피어링에 대한 피어링 계약과 혼동되지 않으며, 이 약관은 피어링 팀과 명시적으로 논의됩니다. 이와 관련하여 질문이 있는 경우 [Microsoft 피어링에](mailto:peering@microsoft.com) 문의하십시오.
    * 동일한 Azure 구독을 사용하여 무료 또는 유료일 수 있는 다른 Azure 제품 또는 클라우드 서비스에 액세스할 수 있습니다. 유료 제품에 액세스하면 요금이 부과됩니다.
    * 새 Azure 계정 및/또는 구독을 만드는 경우 Azure Cloud 서비스를 시도하는 데 사용할 수 있는 평가판 기간 동안 무료 Azure 크레딧을 받을 수 있습니다. 관심이 있는 경우 자세한 내용은 [Microsoft Azure 계정을](https://azure.microsoft.com/free) 방문하십시오.

* **동료 ASN 을 연결:** 피어링을 요청하기 전에 먼저 ASN 및 연락처 정보를 구독에 연결합니다. [피어 ASN 에서 Azure 구독에](howto-subscription-association-powershell.md)대한 설명의 지침을 따릅니다.

## <a name="other-prerequisites"></a>기타 전제 조건
* **피어링DB 프로필:** [피어링DB.](https://www.peeringdb.com) 당사는 등록 시스템에서 이 정보를 사용하여 NOC 정보, 기술 연락처 정보 및 피어링 시설 등에서피어의 존재와 같은 피어의 세부 정보를 검증합니다.

## <a name="next-steps"></a>다음 단계

* [포털을 사용하여 직접 피어링을 만들거나 수정합니다.](howto-direct-portal.md)
* [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)
* [포털을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)