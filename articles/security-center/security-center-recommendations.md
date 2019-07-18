---
title: Azure Security Center에서 보안 권장 사항 | Microsoft Docs
description: 이 문서에서는 Azure Security Center의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 어떤 도움이 되는지 알아봅니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2019
ms.author: v-mohabe
ms.openlocfilehash: fe1d4bf27f3c4bb1f70c1c1fa9767c27f8767998
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064209"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure Security Center의 보안 권장 사항 
이 항목에서는 보고 Azure 리소스를 보호 하는 데 Azure Security Center의 권장 사항을 이해 하는 방법을 설명 합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  이 문서는 단계별 가이드가 아닙니다.
>

## <a name="what-are-security-recommendations"></a>보안 권장 사항이란?
Security Center에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 권장 사항을 만듭니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다.

## <a name="implementing-security-recommendations"></a>보안 권장 사항 구현
### <a name="set-recommendations"></a>권장 사항 설정
[Azure Security Center의 보안 정책 설정](tutorial-security-policy.md)에서 다음을 배울 수 있습니다.

* 보안 정책 구성.
* 데이터 수집 사용.
* 보안 정책의 일부로 표시될 권장 사항 선택.

현재 정책 권장 사항은 시스템 업데이트, 기준 규칙, 맬웨어 방지 프로그램, 서브넷 및 네트워크 인터페이스의 [네트워크 보안 그룹](../virtual-network/security-overview.md), SQL 데이터베이스 감사, SQL 데이터베이스 투명한 데이터 암호화 및 웹 애플리케이션 방화벽에 중점을 두고 있습니다.  [보안 정책 설정](tutorial-security-policy.md) 은 각 권장 사항 옵션에 대한 설명을 제공합니다.

### <a name="monitor-recommendations"></a>권장 사항 모니터링
보안 정책이 설정되면 보안 센터는 리소스의 보안 상태를 분석하여 잠재적인 취약성을 식별합니다. 합니다 **권장 사항** 타일 **개요** Security Center에서 식별 된 권장 사항의 총 수를 표시 합니다.

![Security center 개요](./media/security-center-recommendations/asc-overview.png)

1. 선택 된 **권장 사항 타일** 아래에서 **개요**합니다. 합니다 **권장 사항** 목록이 열립니다.
    
      ![권장 사항 보기](./media/security-center-recommendations/view-recommendations.png)

    권장 사항을 필터링할 수 있습니다. 권장 사항을 필터링하려면 **권장 사항** 블레이드에서 **필터**를 선택합니다. **필터** 블레이드가 열리면 확인하려는 심각도 및 상태 값을 선택합니다.

   * **권장 사항**: 권장 사항입니다.
   * **보안 점수 영향**: 어떻게 중요 한 각 권장 사항을 확인 하기 위해 Security Center 보안 권장 사항을 사용 하 고 고급 알고리즘을 적용 하 여 생성 된 점수입니다. 자세한 내용은 [점수를 계산할 보안](security-center-secure-score.md#secure-score-calculation)합니다.
   * **리소스**: 이 권장 사항이 적용되는 리소스를 나열합니다.
   * **상태 표시줄**:  특정 권장 사항의 심각도를 설명합니다.
       * **높음 (빨강)** : 중요한 리소스(애플리케이션, VM 또는 네트워크 보안 그룹 등)에 취약점이 있으며 주의가 필요합니다.
       * **중간 (주황색)** : 취약점이 있으며 중요하지 않은 또는 추가 단계가 프로세스를 완료하거나 취약점을 제거하는 데 필요합니다.
       * **낮음 (파란색)** : 해결해야 하지만 즉시 조치하지 않아도 되는 취약점이 있습니다. (기본적으로 낮은 권장 사항은 표시되지 않지만 표시하려는 경우 낮은 권장 사항으로 필터링할 수 있습니다.) 
       * **정상 (녹색)** :
       * **사용할 수 없습니다 (회색)** :

1. 각 권장 사항 세부 정보를 보려면 권장 사항을 클릭 합니다.

    ![권장 사항 세부 정보](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> 참조 [클래식 및 Resource Manager 배포 모델](../azure-classic-rm.md) Azure 리소스에 대 한 합니다.
  
 ### <a name="apply-recommendations"></a>권장 사항 적용
> 모든 권장 사항을 검토 한 후 먼저 적용 하는 하나를 결정 합니다. 보안을 사용 하는 것이 좋습니다 처음에 권장 사항을 적용할 평가에 영향을 주지 점수입니다.

1. 목록에서 권장 사항을 클릭 합니다.
1. 지침에 따라 합니다 *재구성 단계* 섹션입니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터의 보안 권장 사항을 소개했습니다. Security Center에 대 한 자세한 내용은 다음 항목을 참조 합니다.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
