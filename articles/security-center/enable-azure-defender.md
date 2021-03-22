---
title: Azure Security Center의 통합 워크로드 보호 사용
description: Azure Defender를 통해 Azure Security Center의 보호를 하이브리드 및 다중 클라우드 리소스로 확장하는 방법을 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108144"
---
# <a name="quickstart-enable-azure-defender"></a>빠른 시작: Azure Defender 사용

Azure Defender의 이점에 대해 알아보려면 [Security Center 무료 및 Azure defender 사용](security-center-pricing.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Security Center 빠른 시작 및 자습서를 위해 Azure Defender를 사용하도록 설정해야 합니다. 

Azure Defender를 사용하여 전체 Azure 구독을 보호할 수 있으며, 보호는 구독 내의 모든 리소스에 상속됩니다.

30일 평가판을 사용할 수 있습니다. 선택한 통화와 지역에 따른 가격 정보는 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.

## <a name="enable-azure-defender-from-the-azure-portal"></a>Azure Portal에서 Azure Defender 사용

위협 방지 기능을 포함한 모든 Security Center 기능을 사용하도록 설정하려면 해당 워크로드가 포함된 구독에서 Azure Defender를 사용하도록 설정해야 합니다. 작업 영역 수준에서 사용하도록 설정해도 Just-In-Time VM 액세스, 적응형 애플리케이션 제어 및 Azure 리소스에 대한 네트워크 검색 기능은 사용하도록 설정되지 않습니다. 또한 작업 영역 수준에서 사용할 수 있는 유일한 Azure Defender 플랜은 서버용 Azure Defender와 머신의 SQL Server용 Azure Defender입니다.

- **Azure Defender for Storage 계정** 은 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.
- **Azure Defender for SQL** 은 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.
- **Azure Database for MariaDB/MySQL/PostgreSQL** 에 대한 위협 방지는 리소스 수준에서만 사용하도록 설정할 수 있습니다.

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>구독 및 작업 영역에서 Azure Defender를 사용하도록 설정하려면 다음을 수행합니다.

- 한 구독에서 Azure Defender를 사용하도록 설정하려면 다음을 수행합니다.

    1. Security Center의 주 메뉴에서 **가격 책정 및 설정** 을 선택합니다.
    1. 보호할 구독 또는 작업 영역을 선택합니다.
    1. **Azure Defender 켜기** 를 선택하여 업그레이드합니다.
    1. **저장** 을 선택합니다.

    > [!TIP]
    > Azure Defender의 각 요금제는 별도로 가격이 책정되며 개별적으로 설정 또는 해제할 수 있습니다. 예를 들어 연결된 Azure App Service 요금제가 없는 구독에서 App Service용 Azure Defender를 해제할 수 있습니다. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="포털의 Security Center 가격 책정 페이지":::

- 여러 구독 또는 작업 영역에서 Azure Defender를 사용하도록 설정하려면 다음을 수행합니다.

    1. Security Center의 사이드바에서 **시작** 을 선택합니다.

        **업그레이드** 탭에는 온보딩에 적합한 구독 및 작업 영역이 나열되어 있습니다.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="시작 페이지의 업그레이드 탭"::: 

    1. **구독 및 작업 영역을 선택하여 Azure Defender를 사용하도록 설정** 목록에서 업그레이드할 구독 및 작업 영역을 선택하고 **업그레이드** 를 선택하여 Azure Defender를 사용하도록 설정합니다.

       - 평가판에 적합하지 않은 구독 및 작업 영역을 선택하면 다음 단계에서 해당 구독 및 작업 영역을 업그레이드하고 요금이 부과되기 시작합니다.
       - 평가판에 적합한 작업 영역을 선택하면 다음 단계에서 평가판이 시작됩니다.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="시작 페이지에서 선택한 모든 작업 영역 및 구독 업그레이드":::


## <a name="next-steps"></a>다음 단계

이제 Azure Defender를 사용하도록 설정했으므로 [Azure Security Center의 자동 프로비저닝 에이전트 및 확장](security-center-enable-data-collection.md)에 설명된 필수 에이전트 및 확장에서 자동 데이터 수집을 사용하도록 설정합니다.