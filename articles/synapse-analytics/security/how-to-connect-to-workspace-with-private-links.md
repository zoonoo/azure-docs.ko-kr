---
title: 개인 링크를 사용하여 Azure Synapse 작업 영역에 연결
description: 이 문서에서는 개인 링크를 사용하여 Azure Synapse 작업 영역에 연결하는 방법을 설명합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432191"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>개인 링크를 사용하여 Azure Synapse 작업 영역에 연결(미리 보기)

이 문서에서는 Azure Synapse 작업 영역에 개인 끝점을 만드는 방법을 설명합니다. 자세한 내용은 [비공개 링크 및 비공개 끝점을](https://docs.microsoft.com/azure/private-link/) 참조하세요.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1단계: Azure 포털에서 Azure 시냅스 작업 영역 열기

**보안에서** **개인 끝점 연결을** 선택한 다음 **+ 개인 끝점을**선택합니다.
![Azure 포털에서 Azure 시냅스 작업 영역 열기](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>2단계: 구독 및 지역 세부 정보 선택

**개인 끝점 만들기** 창의 **기본** 탭에서 **구독** 및 **리소스 그룹을**선택합니다. 만들려는 전용 끝점에 **이름을** 지정합니다. 전용 끝점을 만들 려는 **지역을** 선택합니다.

전용 끝점은 서브넷에서 만들어집니다. 구독, 리소스 그룹 및 선택한 지역은 개인 끝점 서브넷을 필터링합니다. 다음: 완료되면 **리소스 >선택합니다.**
![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>3단계: Azure 시냅스 작업 영역 세부 정보 선택

**리소스** **탭에서 내 디렉터리에서 Azure 리소스에 연결을** 선택합니다. **Subscription** Azure Synapse 작업 영역에 개인 끝점을 만들기 위한 **리소스 유형은** *Microsoft.Synapse/작업 영역입니다.*

Azure 시냅스 작업 영역을 **리소스로**선택합니다. 모든 Azure Synapse 작업 영역에는 Sql, SqlOnDemand 및 Dev의 개인 끝점을 만들 수 있는 세 가지 **대상 하위 리소스가** 있습니다.

**다음: 구성>** 선택하여 설정의 다음 부분으로 진행합니다.
![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

**구성** 탭에서 개인 끝점을 만들어야 하는 **가상 네트워크** 및 **서브넷을** 선택합니다. 또한 개인 끝점에 매핑되는 DNS 레코드를 만들어야 합니다.

**개인 DNS 영역과 통합하려면** **예를** 선택하여 개인 끝점을 개인 DNS 영역과 통합합니다. VNet과 연결된 개인 DNS 영역이 없는 경우 새 개인 DNS 영역이 만들어집니다. **완료되면 검토 + 만들기를** 선택합니다.

![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

배포가 완료되면 Azure Portal에서 Azure Synapse 작업 영역을 열고 **개인 끝점 연결을**선택합니다. 개인 끝점에 연결된 새 개인 끝점 및 개인 끝점 연결 이름이 표시됩니다.

![구독 및 지역 세부 정보 선택](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>다음 단계

[관리되는 작업 영역 VNet에](./synapse-workspace-managed-vnet.md) 대해 자세히 알아보기

[관리되는 비공개 엔드포인트에](./synapse-workspace-managed-private-endpoints.md) 대해 자세히 알아보기

[데이터 원본에 관리되는 비공개 끝점 만들기](./how-to-create-managed-private-endpoints.md)
