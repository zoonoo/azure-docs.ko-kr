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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775408"
---
# <a name="prerequisites-to-set-up-peering-with-microsoft"></a>Microsoft와의 피어링을 설정하기 위한 필수 조건

새 피어 링을 요청 하거나 레거시 피어 링을 Azure 리소스로 변환 하기 전에 아래의 필수 구성 요소가 충족 되는지 확인 합니다.

## <a name="azure-related-prerequisites"></a>Azure 관련 필수 구성 요소
* **Microsoft Azure 계정:** Microsoft Azure 계정이 없으면 [Microsoft Azure 계정을](https://azure.microsoft.com/free)만듭니다. 피어 링을 설정 하려면 피어 링이 Azure 구독 내의 리소스로 모델링 되므로 올바른 활성 Microsoft Azure 구독이 필요 합니다. 다음 사항을 알아야 합니다.
    * 피어 링을 설정 하는 데 사용 되는 Azure 리소스 종류는 항상 무료 Azure 제품입니다. 즉, Azure 계정을 만들거나 구독을 만들거나 Azure 리소스에 액세스 **하 여 피어** **링을 설정** 하는 데에는 요금이 청구 되지 않습니다. 이는 사용자와 Microsoft 간의 직접 피어 링에 대 한 피어 링 계약과 혼동 하지 않는 것입니다 .이 용어는 피어 링 팀과 명시적으로 논의 됩니다. 이와 관련 하 여 궁금한 점이 있는 경우 [Microsoft 피어 링](mailto:peering@microsoft.com) 에 문의 하세요.
    * 동일한 Azure 구독을 사용 하 여 무료 또는 유료 일 수 있는 다른 Azure 제품 또는 클라우드 서비스에 액세스할 수 있습니다. 유료 제품에 액세스할 때 요금이 부과 됩니다.
    * 새 Azure 계정 및/또는 구독을 만드는 경우 Azure 클라우드 서비스를 사용 하는 데 활용할 수 있는 평가판 기간 동안 Azure 크레딧에 대 한 자격이 있을 수 있습니다. 관심이 있으면 [Microsoft Azure 계정](https://azure.microsoft.com/free) 에서 자세한 정보를 참조 하세요.

* **피어 ASN 연결:** 피어 링을 요청 하기 전에 먼저 ASN 및 연락처 정보를 구독에 연결 합니다. [피어 ASN을 Azure 구독에 연결](howto-subscription-association-powershell.md)의 지침을 따릅니다.

## <a name="other-prerequisites"></a>기타 필수 구성 요소
* **Peeringdb 프로필:** 피어는 [Peeringdb](https://www.peeringdb.com)에 완전 하 고 최신 프로필이 있어야 합니다. 등록 시스템에서이 정보를 사용 하 여 NOC 정보, 기술 연락처 정보 및 피어 링 기능에서의 현재 상태와 같은 피어의 세부 정보에 대 한 유효성을 검사 합니다.

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 직접 피어 링을 만들거나 수정](howto-direct-portal.md)합니다.
* [포털을 사용하여 레거시 직접 피어링을 Azure 리소스로 변환](howto-legacy-direct-portal.md)
* [포털을 사용하여 Exchange 피어링 만들기 또는 수정](howto-exchange-portal.md)
* [포털을 사용하여 레거시 Exchange 피어링을 Azure 리소스로 변환](howto-legacy-exchange-portal.md)