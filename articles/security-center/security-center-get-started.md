---
title: Azure Defender로 업그레이드 - Azure Security Center
description: 이 빠른 시작에서는 보안 강화를 위해 Security Center의 Azure Defender로 업그레이드하는 방법을 보여 줍니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099355"
---
# <a name="quickstart-set-up-azure-security-center"></a>빠른 시작: Azure Security Center 설정

Azure Security Center는 하이브리드 클라우드 및 다중 워크로드 전체에서 통합 보안 관리 및 위협 방지 기능을 제공합니다. 체험 기능은 Azure 리소스에 대한 제한된 보안만 제공하지만 Azure Defender를 사용하도록 설정하면 이러한 기능이 온-프레미스 및 기타 클라우드로 확장됩니다. Azure Defender를 사용하면 보안 취약성을 찾아서 수정하고, 액세스 및 애플리케이션 제어를 적용하여 악성 활동을 차단하고, 분석 및 인텔리전스를 사용하여 위협을 검색하고, 공격을 받을 때 신속하게 대응할 수 있습니다. 무료로 Azure Defender를 사용해 볼 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

이 빠른 시작 섹션에서는 Azure Security Center 및 Azure Defender를 사용하도록 설정하는 데 권장되는 모든 단계를 안내합니다. 모든 빠른 시작 단계를 완료한 후에는 다음을 수행할 수 있습니다.

> [!div class="checklist"]
> * Azure 구독에서 활성화된 Security Center
> * Azure 구독에서 활성화된 Azure Defender
> * 자동 데이터 수집 설정
> * 보안 경고를 위한 이메일 알림 설정
> * Azure에 연결된 하이브리드 및 다중 클라우드 머신

## <a name="prerequisites"></a>사전 요구 사항
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

구독에 Azure Defender를 사용하도록 설정하려면 구독 소유자, 구독 기여자 또는 보안 관리자 역할을 할당 받아야 합니다.

## <a name="enable-security-center-on-your-azure-subscription"></a>Azure 구독에서 Security Center 활성화

> [!TIP]
> 관리 그룹 내의 모든 구독에서 Security Center를 사용하도록 설정하려면 [여러 Azure 구독에서 Security Center 사용](onboard-management-group.md)을 참조하세요.

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.

1. 포털의 메뉴에서 **Security Center** 를 선택합니다. 

    Security Center 개요 페이지가 열립니다.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Security Center 개요 대시보드" lightbox="./media/security-center-get-started/overview.png":::

    **Security Center - 개요** 에서는 하이브리드 클라우드 워크로드의 보안 태세를 총체적으로 확인하여 워크로드의 보안을 검색 및 평가하고 위험을 파악 및 완화할 수 있습니다. Security Center는 이전에 사용자 또는 다른 구독 사용자가 온보딩하지 않은 Azure 구독을 자동으로 사용하도록 설정합니다(비용 없음).

**구독** 메뉴 항목을 선택하여 구독 목록을 보고 필터링할 수 있습니다. Security Center는 선택한 구독의 보안 상태를 반영하도록 디스플레이를 조정합니다. 

Security Center를 처음 시작하면 수분 내에 다음이 표시될 수 있습니다.

- 연결된 리소스의 보안을 개선하는 방법에 대한 **권장 사항**
- 현재 Security Center에서 평가 중인 리소스 인벤토리 및 각 리소스의 보안 상태입니다.

Security Center를 최대한 활용하려면 빠른 시작 섹션의 다음 단계를 계속 진행합니다.



## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Azure Security Center를 사용하도록 설정했습니다. 다음 단계는 하이브리드 클라우드 워크로드 전반에서 통합 보안 관리 및 위협 방지를 위해 Azure Defender를 사용하도록 설정하는 것입니다.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Defender 사용](enable-azure-defender.md)