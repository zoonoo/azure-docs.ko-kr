---
title: 개인 링크를 사용 하 여 Synapse Studio에 연결
description: 이 문서에서는 개인 링크를 사용 하 여 Azure Synapse Studio에 연결 하는 방법을 설명 합니다.
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: d49868199d8f9f2da97f08dd06f29afd8f553bd9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586978"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Azure 개인 링크 허브를 사용 하 여 Azure Synapse Studio에 연결 

이 문서에서는 Azure Synapse Analytics 개인 링크 허브 개인 링크를 사용 하 여 Synapse Studio에 안전 하 게 연결 하는 방법을 설명 합니다. 

## <a name="azure-private-link-hubs"></a>Azure 개인 링크 허브 
개인 링크를 사용 하 여 azure 가상 네트워크에서 Azure Synapse Studio에 안전 하 게 연결할 수 있습니다. Azure Synapse Analytics 개인 링크 허브는 보안 네트워크와 Synapse Studio 웹 환경 간의 커넥터 역할을 하는 Azure 리소스입니다. 

개인 링크를 사용 하 여 Synapse Studio에 연결 하는 두 가지 단계가 있습니다. 먼저 개인 링크 허브 리소스를 만들어야 합니다. 둘째,이 개인 링크 허브에 대 한 Azure 가상 네트워크에서 개인 끝점을 만들어야 합니다. 그런 다음 개인 끝점을 사용 하 여 Synapse Studio와 안전 하 게 통신할 수 있습니다. 개인 끝점을 온-프레미스 솔루션 또는 Azure 사설 DNS와 DNS 솔루션에 통합 해야 합니다. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure 개인 링크 허브 및 Azure Synapse Studio
Azure Synapse Studio를 사용 하 여 단일 Azure Synapse 개인 링크 허브 리소스를 사용 하 여 모든 Azure Synapse Analytics 작업 영역에 개인용으로 연결할 수 있습니다. 작업 영역은 Azure Synapse 개인 링크 허브와 동일한 지역에 있을 필요가 없습니다. Azure Synapse 개인 링크 허브 리소스는 다른 구독 또는 Azure AD 테 넌 트에서 Synapse 작업 영역에 연결 하는 데 사용할 수도 있습니다.

Azure Portal에서 *Synapse 개인 링크 허브* 를 검색 하 고 서비스에서 **Azure Synapse Analytics (개인 링크 허브)** 를 선택 하 여 개인 링크 허브를 만들 수 있습니다. 자세한 내용은 [제한 된 네트워크에서 작업 영역 리소스에 연결](./how-to-connect-to-workspace-from-restricted-network.md) 하는 방법에 대 한 가이드의 단계를 따르세요.

>[!NOTE]
>개인 링크 허브는 개인 링크를 통해 정적 콘텐츠 Synapse Studio를 안전 하 게 로드 하기 위한 것입니다. 프로 비전 된/전용 SQL 풀 또는 Spark 풀과 같이 작업 영역 내에서 연결 하려는 리소스에 대 한 **별도의 개인 끝점** 을 만들어야 합니다. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure 개인 링크 허브 및 Azure Virtual Network
Synapse Studio에 대 한 종단 간 연결을 보호 하려면 Azure 가상 네트워크를 Synapse 개인 링크 허브 리소스에 연결 해야 합니다. 이렇게 하려면 가상 네트워크에서 사용자가 만든 개인 링크 허브에 대 한 개인 끝점을 만들어야 합니다. 개인 링크 허브에 대 한 Azure Portal를 사용 하 고 개인 끝점 섹션으로 이동할 수 있습니다. "+ 개인 끝점"을 선택 하 여 개인 링크 허브에 연결 되는 새 개인 끝점을 만듭니다.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="개인 끝점 연결 페이지를 보여 주는 스크린샷":::

"리소스" 탭에서 "Synapse/privateLinkHubs" 리소스 종류를 선택 해야 합니다. ' :::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="리소스 종류 '가 강조 표시 된 ' 개인 끝점 만들기 ' 페이지가 표시 된 스크린샷":::

"구성" 탭에서 가상 네트워크 및 개인 DNS 영역과 통합할 때 사설 DNS 영역에 대해 "privatelink.azuresynapse.net"를 선택 합니다.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="개인 링크 허브에 대 한 개인 끝점 만들기":::

## <a name="next-steps"></a>다음 단계

[제한 된 네트워크에서 작업 영역 리소스에 연결](./how-to-connect-to-workspace-from-restricted-network.md)

[관리 작업 영역](./synapse-workspace-managed-vnet.md) 에 대 한 자세한 정보 Virtual Network

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보

[데이터 원본에 대한 관리형 프라이빗 엔드포인트 만들기](./how-to-create-managed-private-endpoints.md)

[개인 끝점을 사용 하 여 Synapse 작업 영역에 연결](./how-to-connect-to-workspace-with-private-links.md)

