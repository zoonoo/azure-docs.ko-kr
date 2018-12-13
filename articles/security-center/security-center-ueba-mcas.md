---
title: Azure Security Center의 위협 검색 | Microsoft Docs
description: " Microsoft Cloud App Security를 Azure Security Center와 통합하여 위협 및 악의적인 공격을 검색합니다. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: 2a747bdd8de41283b9cba1e40e2652aa826e9c60
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044453"
---
# <a name="ueba-for-azure-resources-and-users"></a>Azure 리소스와 사용자에 대한 UEBA 

Azure Security Center는 Microsoft Cloud App Security와 함께 사용하여 Azure 리소스 및 사용자(Azure 활동)의 UEBA(사용자 및 엔터티 동작 분석)를 기반으로 경고를 표시합니다. 이러한 경고는 사용자 동작에서 이상 문제를 검색하고 사용자 및 엔터티 동작 분석과 ML(기계 학습)을 기반으로 하므로 전체 구독 활동에서 지능형 위협 탐지를 즉시 실행할 수 있습니다. 경고는 자동으로 사용하도록 설정되므로 새로운 이상 검색은 구독과 연결된 전체 사용자 및 리소스에서 많은 동작 이상 문제를 대상으로 하는 즉각적인 검색을 수행하여 즉각적인 결과를 제공합니다. 또한 이러한 경고는 Microsoft Cloud App Security 검색 엔진에 이미 있는 추가 데이터를 활용하여 조사 프로세스를 더 빨리 진행하고 지속적으로 발견되는 위협을 포함하도록 도와줍니다. 

> [!NOTE]
> 고객 리소스(예: 가상 머신 또는 Azure Active Directory 테넌트)에서 수집되거나 고객 리소스와 연결된 보안 관련 고객 데이터의 복사본을 저장할 수 있는 Azure Security Center: (a) Microsoft가 Azure Security Center를 배포해야 하는 지역을 제외하고 해당 리소스와 동일한 지역에 있는 경우 해당 데이터의 복사본이 미국에 저장되고, (b) Azure Security Center가 또 다른 Microsoft 온라인 서비스를 사용하여 해당 데이터를 처리하는 경우 다른 온라인 서비스의 지리적 위치 규칙에 따라 해당 데이터를 저장할 수 있습니다.
>

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>필수 조건

- 유효한 활성화된 [Microsoft Cloud App Security 라이선스](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)
- [Security Center 표준 계층](https://azure.microsoft.com/pricing/details/security-center/)
 
## <a name="threat-detection-alerts"></a>위협 검색 경고

Security Center는 다음과 같은 Microsoft Cloud App Security 이상 문제 검색 경고를 지원합니다.

**불가능한 이동**
-  이 검색은 사용자가 첫 번째 위치에서 두 번째 위치로 이동하는 데 걸리는 시간보다 짧은 기간 내에 지리적으로 먼 위치에서 시작되는 두 개의 사용자 활동(단일 또는 복수 세션)을 식별합니다. 이는 다른 사용자가 동일한 자격 증명을 사용하고 있음을 나타냅니다. 이 검색은 조직의 다른 사용자가 정기적으로 사용하는 VPN 및 위치와 같은 불가능한 이동 조건에 영향을 주는 확실한 “가양성”을 무시하는 기계 학습 알고리즘을 이용합니다. 검색에는 새 사용자 활동 패턴을 학습하는 7일의 초기 학습 기간이 있습니다.

**자주 사용되지 않는 국가에서 발생한 활동**
- 이 검색은 이전 활동 위치를 고려하여 새 위치 및 드문 위치를 확인합니다. 이상 문제 검색 엔진은 조직의 사용자가 사용하는 이전 위치에 대한 정보를 저장합니다. 경고는 조직의 사용자가 최근에 방문하거나 방문한 적이 없는 위치에서 활동이 발생할 때 트리거됩니다. 

**익명 IP 주소에서 발생한 활동**
- 이 검색은 익명 프록시 IP 주소로 식별된 IP 주소에서 사용자가 활성 상태인지 식별합니다. 이 프록시는 해당 장치의 IP 주소를 숨기려는 사용자가 사용하며 악의적인 의도로 사용될 수 있습니다. 이 검색은 조직의 다른 사용자가 널리 사용하는 태그가 잘못 지정된 IP 주소와 같은 “가양성”을 줄이는 기계 학습 알고리즘을 이용합니다.
 
  ![위협 검색 경고](./media/security-center-ueba-mcas/security-center-mcas-alert.png)

## <a name="disabling-threat-detection-alerts"></a>위협 검색 경고 사용 안 함

이러한 경고는 기본적으로 사용하도록 설정되지만 사용하지 않도록 설정할 수 있습니다.

1. Security Center 블레이드에서 **보안 정책**을 선택합니다. 변경하려는 구독의 경우 **설정 편집**을 클릭합니다.
2.  **위협 검색**을 클릭합니다.
3. **통합 사용**에서 **Microsoft Cloud App Security에서 내 데이터에 액세스하도록 허용**의 선택을 취소하고 **저장**을 클릭합니다.

   ![위협 검색 경고](./media/security-center-ueba-mcas/security-center-mcas-optout.png)

> [!NOTE]
> 일부 이상 문제 검색 경고가 표시되지 않는 7일의 초기 학습 기간이 있습니다. 이 기간 후에 각 세션은 지난달에 검색된 활동, 사용자가 활성 상태였던 시기, IP 주소, 디바이스 등과 이러한 활동의 위험 점수에 비교됩니다. 이러한 검색은 조직의 활동에서 학습한 기준선에 따라 사용자 환경을 프로파일링하고 경고를 트리거하는 기계 학습 이상 문제 검색 엔진의 일부입니다. 이러한 검색은 사용자 및 로그온 패턴을 프로파일링하여 가양성을 줄이도록 설계된 기계 학습 알고리즘을 이용합니다.
>
  
## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center의 위협적 검색 관련 작업을 설명했습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
