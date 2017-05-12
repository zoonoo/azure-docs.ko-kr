---
title: "Security Center 가격 책정 | Microsoft Docs"
description: "이 문서에서는 Azure Security Center에 대한 가격 책정 정보를 제공합니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 9c20ace9112c62e733b7b679e3f0be717144f3c2
ms.openlocfilehash: 933a485e36b3cf0f514f28fc069d49161605af3a
ms.contentlocale: ko-kr
ms.lasthandoff: 02/23/2017


---
# <a name="azure-security-center-pricing"></a>Azure Security Center 가격 책정
Azure 보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

## <a name="pricing-tiers"></a>가격 책정 계층
Security Center는 두 계층으로 제공됩니다.

* **무료 계층**은 모든 Azure 구독에서 자동으로 설정됩니다. 무료 계층은 Azure 리소스, 기본 보안 정책, 보안 권장 사항, 그리고 파트너의 보안 제품 및 서비스와의 통합 등의 보안 상태를 볼 수 있게 해줍니다.
* **표준 계층**은 인텔리전스, 동작 분석, 이상 탐지, 보안 사고 위협 및 위협 평가 보고서 등 고급 위협 감지 기능을 추가합니다. 표준 계층은 처음 60일간 무료로 제공됩니다.

자세한 내용은 Security Center [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

> [!NOTE]
> Security Center에서는 Azure Storage를 사용하여 보호된 노드에서 생성된 보안 데이터를 저장합니다. 이 저장소와 관련된 비용은 서비스 가격에 포함되지 않으며, 일반 [Azure Storage 요금](https://azure.microsoft.com/pricing/details/storage/blobs/)이 별도로 부과됩니다. 무료 평가판을 사용하는 60일 동안에도 Storage 요금이 부과됩니다.
>
>

## <a name="try-standard-free-for-60-days"></a>60일 표준 계층 무료 사용
표준 계층은 처음 60일간 무료로 제공됩니다. 60일 종료 시 서비스를 계속 사용하기로 선택하는 경우 사용량에 대한 요금이 자동으로 부과되기 시작합니다.

표준 계층 얻기:

1. **Security Center** 블레이드에서 **정책** 타일을 선택합니다.
2. 표준으로 업그레이드하려는 경우 구독을 선택합니다.
3. **보안 정책** 블레이드에서 **가격 책정 계층**을 선택합니다.
4. **가격 책정 계층 선택** 블레이드에서 **표준**을 선택합니다.
5. **선택**을 클릭합니다.

![표준 계층][1]

## <a name="why-upgrade-to-standard"></a>표준으로 업그레이드하는 이유?
Security Center의 표준 계층은 무료 계층의 모든 기능과 고급 위협 요소 탐지를 제공합니다. 고급 위협 요소 탐지를 통해 Azure 리소스를 대상으로 하는 활성 위협을 식별하고 이에 신속하게 대응하는 데 필요한 정보를 제공할 수 있습니다.

보안 센터는 서명 기반 방식을 뛰어 넘는 고급 보안 분석을 사용합니다. 수동 접근 방법을 사용하고 공격의 발전을 예측하여 식별할 수 없는 위협을 감지하도록 전체 클라우드 패브릭에 대한 이벤트를 평가하는 데 빅 데이터 및 Machine Learning 기술의 돌파구를 활용합니다.

표준 계층과 함께 제공되는 보안 분석은 다음과 같습니다.

* **위협 인텔리전스** - Microsoft 제품과 서비스, Microsoft Digital Crimes Unit, Microsoft 보안 대응 센터 및 외부 피드의 글로벌 위협 인텔리전스를 활용하여 알려진 위협 요소를 찾습니다.
* **동작 분석** - 알려진 패턴을 적용하여 악의적인 동작을 검색합니다.
* **이상 감지** - 통계적 프로파일링을 사용하여 기록 기준을 작성합니다. 잠재적 공격 벡터를 준수하는 설정된 기준에서 편차에 대해 경고합니다.

아래 **보안 경고** 블레이드에서 Security Center는 보안 **인시던트**를 감지했습니다. 보안 인시던트는 kill 체인 패턴과 일치하는 리소스에 대한 모든 경고의 집계입니다. 보안 인시던트를 선택하면 인시던트에 대한 자세한 정보를 표시하고 관련된 경고를 나열합니다. 경고를 선택하면 해당 발생에 대한 자세한 정보가 나타납니다.

![보안 인시던트][2]

아래의 **네트워크 통신** 경고는 경고에 대한 세부 정보를 제공합니다. 세부 정보에는 전체 설명, 심각도, 현재 상태(이 경우 해제되었으며, 이는 사용자가 조치를 취해 알림을 해제했음), 공격을 받는 리소스, 수정 단계가 있습니다. Microsoft 위협 인텔리전스 보고서에 연결되는 링크 목록도 있습니다. 이러한 보고서는 보안 수정 및 방어 목적을 위해 사용할 수 있습니다.

![보안 경고 세부 정보][3]

## <a name="enable-data-collection"></a>데이터 컬렉션 활성화
가상 컴퓨터 동작 분석을 사용하려면 데이터 수집을 켜야 합니다.

데이터 수집이 활성화되었는지 확인하려면:

1. **정책** 타일을 선택합니다. Azure 구독을 나열하는 **보안 정책** 블레이드가 열립니다.
2. 구독을 선택합니다.
3. **데이터 수집**이 꺼진 경우 켜기로 변경하고 변경 내용을 저장합니다.

자세한 내용은 [Azure Security Center에서 데이터 수집 활성화](security-center-enable-data-collection.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* 이 문서에서는 Security Center의 가격 책정을 소개했습니다. 가격 책정에 대한 추가 정보는 Security Center [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.
* Security Center의 고급 감지 기능에 대한 자세한 내용은 [Azure Security Center 감지 기능](security-center-detection-capabilities.md)을 참고하세요.
* Security Center 사용에 대한 질문이 있으면 [Azure Security Center FAQ](security-center-faq.md)를 참조하세요.
* 여전히 Security Center 또는 Azure 사용에 대한 질문이 있는 경우 [Azure 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc)을 방문하세요.

<!--Image references-->
[1]: ./media/security-center-pricing/standard.png
[2]: ./media/security-center-pricing/incident.png
[3]: ./media/security-center-pricing/network-alert.png

