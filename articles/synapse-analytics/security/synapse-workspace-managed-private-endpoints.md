---
title: 관리형 프라이빗 엔드포인트
description: Azure Synapse Analytics의 관리형 프라이빗 엔드포인트에 대해 설명하는 문서
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569949"
---
# <a name="synapse-managed-private-endpoints"></a>Synapse 관리형 프라이빗 엔드포인트

이 문서에서는 Azure Synapse Analytics의 관리형 프라이빗 엔드포인트에 대해 설명합니다.

## <a name="managed-private-endpoints"></a>관리형 프라이빗 엔드포인트

관리형 프라이빗 엔드포인트는 Azure Synapse 작업 영역과 연결된 Managed Virtual Network에서 만들어진 프라이빗 엔드포인트입니다. 관리형 프라이빗 엔드포인트는 Azure 리소스에 대한 프라이빗 링크를 설정합니다. Azure Synapse는 사용자 대신 이러한 프라이빗 엔드포인트를 관리합니다. Azure Synapse 작업 영역에서 관리형 프라이빗 엔드포인트를 만들어 Azure 서비스(예: Azure Storage 또는 Azure Cosmos DB) 및 Azure에 호스팅되는 고객/파트너 서비스에 액세스할 수 있습니다.

관리형 프라이빗 엔드포인트를 사용하는 경우 Azure Synapse 작업 영역과 다른 Azure 리소스 간의 트래픽이 Microsoft 백본 네트워크를 통해 완전히 트래버스됩니다. 관리형 프라이빗 엔드포인트는 데이터 유출로부터 보호됩니다. 관리형 프라이빗 엔드포인트는 Managed Virtual Network의 개인 IP 주소를 사용하여 Azure Synapse 작업 영역이 Virtual Network로 통신하고 있는 Azure 서비스를 효과적으로 가져옵니다. 관리형 프라이빗 엔드포인트는 전체 서비스가 아닌 Azure의 특정 리소스에 매핑됩니다. 고객은 연결 범위를 조직에서 승인한 특정 리소스로 제한할 수 있습니다. 

[프라이빗 링크 및 프라이빗 엔드포인트](../../private-link/index.yml)에 대해 자세히 알아보세요.

>[!IMPORTANT]
>관리형 프라이빗 엔드포인트는 관리형 작업 영역 Virtual Network가 연결된 Azure Synapse 작업 영역에서만 지원됩니다.

>[!NOTE]
>Azure Synapse 작업 영역을 만들 때 Managed Virtual Network를 해당 작업 영역에 연결하도록 선택할 수 있습니다. Managed Virtual Network를 작업 영역에 연결하도록 선택하는 경우 작업 영역의 아웃바운드 트래픽을 승인된 대상으로만 제한하도록 선택할 수도 있습니다. 이러한 대상에 대한 관리형 프라이빗 엔드포인트를 만들어야 합니다. 


Azure Synapse에서 관리형 프라이빗 엔드포인트를 만들 때 프라이빗 엔드포인트 연결이 "보류 중" 상태로 생성됩니다. 승인 워크플로가 시작됩니다. 프라이빗 링크 리소스 소유자가 연결을 승인 또는 거부합니다. 소유자가 연결을 승인하면 프라이빗 링크가 설정됩니다. 하지만 소유자가 연결을 승인하지 않으면 프라이빗 링크가 설정되지 않습니다. 어떤 경우든 관리형 프라이빗 엔드포인트는 연결 상태로 업데이트 됩니다. 승인된 상태의 관리형 프라이빗 엔드포인트만 관리형 프라이빗 엔드포인트에 연결된 프라이빗 링크 리소스로 트래픽을 보내는 데 사용할 수 있습니다.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>전용 SQL 풀 및 서버리스 SQL 풀을 위한 관리형 프라이빗 엔드포인트

전용 SQL 풀 및 서버리스 SQL 풀은 Azure Synapse 작업 영역의 분석 기능입니다. 이러한 기능은 [관리형 작업 영역 Virtual Network](./synapse-workspace-managed-vnet.md)에 배포되지 않은 다중 테넌트 인프라를 사용합니다.

작업 영역이 만들어지면 Azure Synapse는 해당 작업 영역에 전용 SQL 풀용과 서버리스 SQL 풀용인 두 개의 관리형 프라이빗 엔드포인트를 만듭니다. 

이러한 두 개의 관리형 프라이빗 엔드포인트는 Synapse Studio에 나열됩니다. 왼쪽 탐색 영역에서 **관리** 를 선택한 다음, **관리형 프라이빗 엔드포인트** 를 선택하여 Studio에서 확인합니다.

SQL 풀을 대상으로 하는 관리형 프라이빗 엔드포인트를 *synapse-ws-sql--\<workspacename\>* 이라고 하고, 서버리스 SQL 풀을 대상으로 하는 관리형 프라이빗 엔드포인트를 *synapse-ws-sqlOnDemand--\<workspacename\>* 라고 합니다.

![전용 SQL 풀 및 서버리스 SQL 풀을 위한 관리형 프라이빗 엔드포인트](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

이러한 두 개의 관리형 프라이빗 엔드포인트는 Azure Synapse 작업 영역을 만들 때 자동으로 만들어집니다. 두 관리형 프라이빗 엔드포인트에 대한 요금은 청구되지 않습니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 [데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md) 문서로 이동하세요.