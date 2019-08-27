---
title: Azure Security Center에서 보안 경고 관리| Microsoft Docs
description: 이 문서는 Azure Security Center 기능을 사용하여 보안 경고를 관리하고 대응하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/27/2019
ms.author: v-mohabe
ms.openlocfilehash: a31b6006ff3345947fc0c64b9427d79e3870a2b6
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70052099"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Azure Security Center에서 보안 경고 관리 및 대응

이 항목에서는 리소스를 보호 하기 위해 받은 경고를 확인 하 고 처리 하는 방법을 보여 줍니다. 

* 여러 유형의 경고에 대 한 자세한 내용은 [보안 경고 유형](security-center-alerts-overview.md#security-alert-types)을 참조 하세요.
* Security Center에서 경고를 생성 하는 방법에 대 한 개요는 [Azure Security Center 검색 하 고 위협에 대응 하는 방법](security-center-alerts-overview.md#detect-threats)을 참조 하세요.

> [!NOTE]
> 고급 감지를 사용하도록 설정하려면 Azure Security Center 표준으로 업그레이드합니다. 평가판을 사용할 수 있습니다. 업그레이드하려면 [보안 정책](tutorial-security-policy.md)에서 가격 책정 계층을 선택합니다. 자세한 내용은 [Azure Security Center 가격 책정](security-center-pricing.md)을 참조하세요.

## <a name="what-are-security-alerts"></a>보안 경고란?
보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다.

> [!NOTE]
> Security Center 검색 기능이 작동 하는 방식에 대 한 자세한 내용은 [Azure Security Center 검색 하 고 위협에 대응 하는 방법](security-center-alerts-overview.md#detect-threats)을 참조 하세요.

## <a name="manage-your-security-alerts"></a>보안 경고 관리

1. Security Center 대시보드에서 **위협 방지** 타일을 참조 하 여 경고를 확인 하 고 개요를 확인 합니다.

    ![보안 센터의 보안 경고 타일](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 경고에 대 한 자세한 내용을 보려면 타일을 클릭 합니다.

   ![Security Center의 보안 경고](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 표시 되는 경고를 필터링 하려면 **필터**를 클릭 하 고, 열리는 **필터** 블레이드에서 적용 하려는 필터 옵션을 선택 합니다. 목록은 선택한 필터에 따라 업데이트 됩니다. 필터링은 매우 유용할 수 있습니다. 예를 들어 시스템에서 잠재적 위반을 조사하고 있기 때문에 최근 24시간 동안 발생한 보안 경고를 해결하려고 할 수 있습니다.

    ![보안 센터에서 경고 필터링](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>보안 경고에 대응

1. **보안 경고** 목록에서 보안 경고를 클릭 합니다. 공격을 해결 하기 위해 수행 해야 하는 단계와 관련 리소스가 나와 있습니다.

    ![보안 경고에 대응](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 정보를 검토 한 후 공격을 받은 리소스를 클릭 합니다.

    ![보안 경고에 대해 수행할 작업에 대 한 제안](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    **일반 정보** 섹션은 보안 경고를 트리거한 항목에 대 한 통찰력을 제공할 수 있습니다. 대상 리소스, 원본 IP 주소 (해당 하는 경우), 경고가 아직 활성 상태인 경우, 수정 하는 방법에 대 한 권장 사항 등의 정보가 표시 됩니다.  

    > [!NOTE]
    >일부 경우에는 원본 IP 주소를 사용할 수 없습니다. 일부 Windows 보안 이벤트 로그에는 IP 주소가 포함 되지 않습니다.

1. Security Center에서 제안 하는 수정 단계는 보안 경고에 따라 다릅니다. 각 경고에 대해 다음을 수행 합니다. 경우에 따라 위협 검색 경고를 완화 하기 위해 다른 Azure 컨트롤 또는 서비스를 사용 하 여 권장 되는 수정 사항을 구현 해야 할 수 있습니다. 

    다음 항목에서는 리소스 유형에 따라 다양 한 경고를 안내 합니다.
    
    * [IaaS Vm 및 서버 경고](security-center-alerts-iaas.md)
    * [기본 계산 경고](security-center-alerts-compute.md)
    * [데이터 서비스 경고](security-center-alerts-data-services.md)
    
    다음 항목에서는 azure에 배포 된 리소스에 대 한 추가 보호 계층을 적용 하기 위해 Security Center Azure 인프라와 통합 하 여 수집 하는 다양 한 원격 분석을 사용 하는 방법에 대해 설명 합니다.
    
    * [서비스 계층 경고](security-center-alerts-service-layer.md)
    * [Azure 보안 제품과 통합](security-center-alerts-integration.md)
    
## <a name="see-also"></a>참고자료

이 문서에서는 보안 센터에서 보안 정책을 구성하는 방법을 배웠습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center의 보안 경고](security-center-alerts-overview.md)
* [보안 인시던트 처리](security-center-incident.md)
* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md)
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
