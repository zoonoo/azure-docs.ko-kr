---
title: 개인 링크를 사용 하 여 Azure Synapse 작업 영역에 연결
description: 이 문서에서는 개인 링크를 사용 하 여 Azure Synapse 작업 영역에 연결 하는 방법을 설명 합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432191"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>개인 링크를 사용 하 여 Azure Synapse 작업 영역에 연결 (미리 보기)

이 문서에서는 Azure Synapse 작업 영역에 대 한 개인 끝점을 만드는 방법을 설명 합니다. 자세히 알아보려면 [개인 링크 및 개인 끝점](https://docs.microsoft.com/azure/private-link/) 을 참조 하세요.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1 단계: Azure Portal에서 Azure Synapse 작업 영역을 엽니다.

**보안** 아래에서 **개인 끝점 연결** 을 선택 하 고 **+ 개인 끝점**을 선택 합니다.
![Azure Portal에서 Azure Synapse 작업 영역을 엽니다.](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>2 단계: 구독 및 지역 세부 정보 선택

**개인 끝점 만들기** 창의 **기본 사항** 탭에서 **구독** 및 **리소스 그룹**을 선택 합니다. 만들려는 개인 끝점에 **이름을** 지정 합니다. 개인 끝점을 만들 **지역을** 선택 합니다.

개인 끝점은 서브넷에 생성 됩니다. 선택한 구독, 리소스 그룹 및 지역이 개인 끝점 서브넷을 필터링 합니다. 완료 되 면 **다음: 리소스 >** 를 선택 합니다.
![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>3 단계: Azure Synapse 작업 영역 세부 정보 선택

**리소스** 탭에서 **내 디렉터리의 Azure 리소스에 연결을** 선택 합니다. azure Synapse 작업 영역을 포함 하는 **구독** 을 선택 합니다. Azure Synapse 작업 영역에 대 한 개인 끝점을 만들기 위한 **리소스 형식은** *Synapse/작업 영역*입니다.

**리소스로**Azure Synapse 작업 영역을 선택 합니다. 모든 Azure Synapse 작업 영역에는 Sql, SqlOnDemand 및 Dev에 대 한 개인 끝점을 만들 수 있는 세 개의 **대상 하위 리소스가** 있습니다.

**다음: 구성>** 를 선택 하 여 설치의 다음 부분으로 이동 합니다.
![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

**구성** 탭에서 개인 끝점을 만들 **가상 네트워크** 및 **서브넷** 을 선택 합니다. 또한 개인 끝점에 매핑되는 DNS 레코드를 만들어야 합니다.

개인 dns 영역과 **통합** 하려면 **예** 를 선택 하 여 개인 끝점을 개인 dns 영역과 통합 합니다. VNet에 연결 된 개인 DNS 영역이 없는 경우 새 개인 DNS 영역을 만듭니다. 완료 되 면 **검토 + 만들기** 를 선택 합니다.

![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

배포가 완료 되 면 Azure Portal에서 Azure Synapse 작업 영역을 열고 **개인 끝점 연결**을 선택 합니다. 개인 끝점과 연결 된 새 개인 끝점 및 개인 끝점 연결 이름이 표시 됩니다.

![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>다음 단계

[관리 작업 영역 VNet](./synapse-workspace-managed-vnet.md) 에 대 한 자세한 정보

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)
