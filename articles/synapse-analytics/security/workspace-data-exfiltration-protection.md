---
title: Azure Synapse Analytics 작업 영역에 대 한 데이터 반출 보호
description: 이 문서에서는 Azure Synapse Analytics의 데이터 반출 보호에 대해 설명 합니다.
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 71210cdcc2b3758a59a1b41816e6468556e94808
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518263"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 작업 영역에 대 한 데이터 반출 보호
이 문서에서는 Azure Synapse Analytics의 데이터 반출 보호에 대해 설명 합니다.

## <a name="securing-data-egress-from-synapse-workspaces"></a>Synapse 작업 영역에서 데이터 송신 보호
Azure Synapse Analytics 작업 영역에서는 작업 영역에 대 한 데이터 반출 보호 사용을 지원 합니다. 반출 보호를 사용 하 여 Azure 리소스에 액세스 하는 악의적인 참가자와 조직의 범위 밖에 있는 위치에 중요 한 데이터를 빼내기 위해 공공 보호할 수 있습니다. 작업 영역을 만들 때 관리 되는 가상 네트워크를 사용 하 여 작업 영역을 구성 하 고 데이터 exfiltration에 대 한 추가 보호를 선택할 수 있습니다. 관리 되는 [가상 네트워크](./synapse-workspace-managed-vnet.md)를 사용 하 여 작업 영역을 만들면 데이터 통합 및 Spark 리소스가 관리 되는 가상 네트워크에 배포 됩니다. 작업 영역의 전용 SQL 풀 및 서버를 사용 하지 않는 SQL 풀에는 다중 테 넌 트 기능이 있으므로 관리 되는 가상 네트워크 외부에 있어야 합니다. 데이터 반출 보호를 사용 하는 작업 영역의 경우 관리 되는 가상 네트워크 내의 리소스는 항상 [관리 되는 개인 끝점](./synapse-workspace-managed-private-endpoints.md) 을 통해 통신 하며 Synapse SQL 리소스는 권한 있는 Azure 리소스 (작업 영역에서 승인 된 관리 개인 끝점 연결의 대상)에만 연결할 수 있습니다. 

>[!Note]
>작업 영역을 만든 후에는 관리 되는 가상 네트워크 및 데이터 반출 보호에 대 한 작업 영역 구성을 변경할 수 없습니다.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>승인 된 대상에 대 한 Synapse 작업 영역 데이터 송신 관리
데이터 반출을 사용 하 여 작업 영역을 만든 후 작업 영역 리소스의 소유자는 작업 영역에 대해 승인 된 Azure AD 테 넌 트 목록을 관리할 수 있습니다. 작업 영역에 대 한 [권한이](./synapse-workspace-access-control-overview.md) 있는 사용자는 Synapse Studio를 사용 하 여 작업 영역의 승인 된 Azure AD 테 넌 트에 있는 리소스에 대 한 관리 되는 개인 끝점 연결 요청을 만들 수 있습니다. 사용자가 승인 되지 않은 테 넌 트의 리소스에 대 한 개인 끝점 연결을 만들려고 하면 관리 되는 개인 끝점 생성이 차단 됩니다.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>데이터 반출 보호를 사용 하는 샘플 작업 영역
예를 사용 하 여 Synapse 작업 영역에 대 한 데이터 반출 보호를 설명 해 주세요. Contoso에는 테 넌 트 A 및 테 넌 트 B의 Azure 리소스가 있으며 이러한 리소스를 안전 하 게 연결 하는 데 필요 합니다. 테 넌 트 B가 승인 된 Azure AD 테 넌 트로 추가 된 테 넌 트 A에서 Synapse 작업 영역을 만들었습니다. 다이어그램은 저장소 계정 소유자가 승인한 테 넌 트 A 및 테 넌 트 B의 Azure Storage 계정에 대 한 개인 끝점 연결을 보여 줍니다. 다이어그램에는 차단 된 개인 끝점 생성도 표시 됩니다. Contoso의 작업 영역에 대해 승인 된 Azure AD 테 넌 트가 아닌 Fabrikam Azure AD 테 넌 트에서 Azure Storage 계정을 대상으로 하는이 개인 끝점의 생성이 차단 되었습니다. 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="이 다이어그램에서는 Synapse 작업 영역에 대 한 데이터 반출 보호가 구현 되는 방법을 보여 줍니다.":::

>[!IMPORTANT]
>작업 영역 테 넌 트 이외의 테 넌 트에 있는 리소스는 SQL 풀에 연결 하기 위해 방화벽 규칙을 차단 하지 않아야 합니다. 작업 영역에서 관리 되는 가상 네트워크 (예: Spark 클러스터) 내의 리소스는 방화벽으로 보호 되는 리소스에 대 한 관리 되는 개인 링크를 통해 연결할 수 있습니다.
## <a name="next-steps"></a>다음 단계

[데이터 반출 보호를 사용 하도록 설정한 상태에서 작업 영역을 만드는](./how-to-create-a-workspace-with-data-exfiltration-protection.md) 방법에 대해 알아봅니다.

[관리 작업 영역](./synapse-workspace-managed-vnet.md) 에 대 한 자세한 정보 Virtual Network

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)
