---
title: Azure Synapse Analytics 작업 영역에 대한 데이터 반출 보호
description: 이 문서에서는 Azure Synapse Analytics의 데이터 반출 보호에 대해 설명합니다
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 792197b3558a16706fee52204fa90a8ef8d0735a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232055"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 작업 영역에 대한 데이터 반출 보호
이 문서에서는 Azure Synapse Analytics의 데이터 반출 보호에 대해 설명합니다

## <a name="securing-data-egress-from-synapse-workspaces"></a>Synapse 작업 영역에서 데이터 송신 보호
Azure Synapse Analytics 작업 영역에서는 작업 영역에 대한 데이터 반출 보호 사용을 지원합니다. 반출 보호를 사용하여 Azure 리소스에 액세스해 조직의 범위 밖에 있는 위치로 중요한 데이터를 반출하는 악의적인 내부자를 막을 수 있습니다. 작업 영역을 만들 때 관리형 가상 네트워크와 데이터 반출에 대한 추가 보호를 사용하여 작업 영역을 구성할 수 있습니다. 작업 영역을 [관리형 가상 네트워크](./synapse-workspace-managed-vnet.md)를 이용해 만들면 데이터 통합과 Spark 리소스가 관리형 가상 네트워크에 배포됩니다. 작업 영역의 전용 SQL 풀과 서버리스 SQL 풀에는 다중 테넌트 기능이 있으므로 관리형 가상 네트워크 외부에 있어야 합니다. 데이터 반출 보호를 사용하는 작업 영역의 경우 관리형 가상 네트워크 내의 리소스는 항상 [관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)를 통해 통신하며 Synapse SQL 리소스는 권한 있는 Azure 리소스(작업 영역에서 승인된 관리형 프라이빗 엔드포인트 연결의 대상)에만 연결할 수 있습니다. 

> [!Note]
> 작업 영역을 만든 후에는 관리형 가상 네트워크 및 데이터 반출 방지에 대한 작업 영역 구성을 변경할 수 없습니다.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>승인된 대상에 대한 Synapse 작업 영역 데이터 송신 관리
데이터 반출 보호를 사용하도록 설정한 상태에서 작업 영역을 만든 후 작업 영역 리소스 소유자는 작업 영역에 대한 승인된 Azure AD 테넌트 목록을 관리할 수 있습니다. 작업 영역에 대한 [올바른 권한](./synapse-workspace-access-control-overview.md)이 있는 사용자는 Synapse Studio를 사용하여 작업 영역의 승인된 Azure AD 테넌트에 있는 리소스에 대한 관리형 프라이빗 엔드포인트 연결 요청을 만들 수 있습니다. 사용자가 승인되지 않은 테넌트의 리소스에 대한 프라이빗 엔드포인트 연결을 만들려고 하면 관리형 프라이빗 엔드포인트 만들기가 차단됩니다.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>데이터 반출 보호를 사용하도록 설정된 샘플 작업 영역
예제를 사용하여 Synapse 작업 영역에 대한 데이터 반출 보호를 설명합니다. Contoso에는 테넌트 A 및 테넌트 B의 Azure 리소스가 있으며 이러한 리소스를 안전하게 연결할 필요가 있습니다. 테넌트 B가 승인된 Azure AD 테넌트로 추가된 테넌트 A에서 Synapse 작업 영역을 만들었습니다. 다이어그램은 스토리지 계정 소유자가 승인한 테넌트 A 및 테넌트 B의 Azure Storage 계정에 대한 프라이빗 엔드포인트 연결을 표시합니다. 다이어그램에는 차단된 프라이빗 엔드포인트 만들기도 표시됩니다. 이 프라이빗 엔드포인트 생성은 Contoso 작업 영역에 대한 승인되지 않은 Azure AD 테넌트인 Fabrikam Azure AD 테넌트에 있는 Azure Storage 계정을 대상으로 지정했기 때문에 차단되었습니다.

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="이 다이어그램에서는 Synapse 작업 영역에 데이터 반출 보호가 구현되는 방법을 설명합니다":::

>[!IMPORTANT]
>
> 작업 영역 테넌트 이외의 테넌트에 있는 리소스에 SQL 풀을 연결하려면 리소스를 차단하는 방화벽 규칙이 없어야 합니다. 작업 영역의 관리형 가상 네트워크(예: Spark 클러스터) 내의 리소스는 관리형 프라이빗 링크를 통해 방화벽으로 보호되는 리소스에 연결할 수 있습니다.
> >

## <a name="next-steps"></a>다음 단계

[데이터 반출 보호를 사용하도록 설정한 상태에서 작업 영역을 만드는](./how-to-create-a-workspace-with-data-exfiltration-protection.md) 방법에 대해 자세히 알아봅니다

[관리형 작업 영역 가상 네트워크](./synapse-workspace-managed-vnet.md)에 대한 자세한 정보를 알아봅니다

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)
