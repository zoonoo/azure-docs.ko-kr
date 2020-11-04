---
title: 제한 된 네트워크에서 Synapse Studio 작업 영역 리소스에 연결
description: 이 문서에서는 제한 된 네트워크에서 Azure Synapse Studio 작업 영역 리소스에 연결 하는 방법을 설명 합니다.
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 5d28b8f2ff3045c9fdf5e8a866419a22bfbc6504
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321789"
---
# <a name="connect-to-synapse-studio-workspace-resources-from-a-restricted-network"></a>제한 된 네트워크에서 Synapse Studio 작업 영역 리소스에 연결

이 문서의 대상 독자는 회사의 제한 된 네트워크를 관리 하는 회사 IT 관리자입니다. IT 관리자는이 제한 된 네트워크 내에서 Azure Synapse Studio와 워크스테이션 간의 네트워크 연결을 사용 하도록 설정 하려고 합니다.

이 문서에서는 제한 된 네트워크 환경에서 Azure Synapse 작업 영역에 연결 하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독** : Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Synapse 작업 영역** : Synapse Studio가 없는 경우 Azure Synapse Analytics에서 Synapse 작업 영역을 만듭니다. 작업 영역 이름은 4 단계에서 필요 합니다.
* **제한 된 네트워크** : 회사 it 관리자가 제한 된 네트워크를 유지 관리 합니다. it 관리자에 게는 네트워크 정책을 구성할 수 있는 권한이 있습니다. 3 단계에서는 가상 네트워크 이름 및 서브넷이 필요 합니다.



## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>1 단계: 제한 된 네트워크에 네트워크 아웃 바운드 보안 규칙 추가

4 개의 서비스 태그를 사용 하 여 4 개의 네트워크 아웃 바운드 보안 규칙을 추가 해야 합니다. [서비스 태그 개요](/azure/virtual-network/service-tags-overview.md) 에 대 한 자세한 정보 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (선택 사항. Microsoft에 데이터를 공유 하려는 경우에만이 유형의 규칙을 추가 합니다.

다음과 같이 아웃 바운드 규칙 세부 정보를 **Azure Resource Manager** 합니다. 다른 세 가지 규칙을 만드는 경우 드롭다운 선택 목록에서 서비스 태그 이름 " **AzureFrontDoor** ", " **AzureActiveDirectory** ", " **azuremonitor** "를 선택 하 여 " **대상 서비스 태그** " 값을 바꿉니다.

![AzureResourceManager](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)


## <a name="step-2-create-azure-synapse-analytics-private-link-hubs"></a>2 단계: Azure Synapse Analytics 만들기 (개인 링크 허브)

Azure Portal에서 Azure Synapse Analytics (개인 링크 허브)를 만들어야 합니다. Azure Portal에서 " **Azure Synapse Analytics (개인 링크 허브)** "를 검색 하 고 필요한 필드를 입력 하 여 만듭니다. 

> [!Note]
> 지역은 Synapse 작업 영역이 있는 위치와 동일 해야 합니다.

![Synapse Analytics 개인 링크 허브를 만드는 중](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-private-link-endpoint-for-synapse-studio-gateway"></a>3 단계: Synapse Studio 게이트웨이에 대 한 개인 링크 엔드포인트 만들기

Synapse Studio 게이트웨이에 액세스 하려면 Azure Portal에서 개인 링크 끝점을 만들어야 합니다. Azure Portal를 통해 " **개인 링크** "를 검색 합니다. " **개인 링크 센터** "에서 " **개인 끝점 만들기** "를 선택한 다음 필요한 필드를 입력 하 고 만듭니다. 

> [!Note]
> 지역은 Synapse 작업 영역이 있는 위치와 동일 해야 합니다.

![Synapse studio에 대 한 개인 끝점 만들기 1](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

" **리소스** "의 다음 탭에서 위의 2 단계에서 만든 개인 링크 허브를 선택 합니다.

![Synapse studio 2에 대 한 개인 끝점을 만드는 중](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

" **구성** "의 다음 탭에서 
* " **가상 네트워크** "에 대해 제한 된 가상 네트워크 이름을 선택 합니다.
* " **서브넷** "에 대해 제한 된 가상 네트워크의 서브넷을 선택 합니다. 
* " **예** "를 선택 하 여 " **개인 DNS 영역으로 통합** " 합니다.

![Synapse studio 3에 대 한 개인 끝점을 만드는 중](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

개인 링크 끝점이 생성 된 후 Synapse studio 웹 도구의 로그인 페이지에 액세스할 수 있습니다. 그러나 다음 단계를 완료 해야 할 때까지 Synapse 작업 영역 내의 리소스에는 액세스할 수 없습니다.

## <a name="step-4-create-private-link-endpoints-for-synapse-studio-workspace-resource"></a>4 단계: Synapse Studio 작업 영역 리소스에 대 한 개인 링크 엔드포인트 만들기

Synapse Studio 작업 영역 리소스 내의 리소스에 액세스 하려면 " **Dev** " 유형의 " **대상 하위 리소스** "를 포함 하는 개인 링크 엔드포인트를 하나 이상 만들어야 하며, " **Sql** " 또는 " **SqlOnDemand** " 유형의 두 가지 선택적 개인 링크 끝점은 액세스 하려는 Synapse Studio 작업 영역에 있는 리소스에 따라 달라 집니다. Synapse studio 작업 영역에 대 한이 개인 링크 끝점 생성은 끝점을 만드는 것과 유사 합니다.  

" **리소스** " 탭의 아래 영역에 주의 하세요.
* " **리소스 종류** "를 " **Synapse/작업 영역** "으로 선택 합니다.
* 이전에 만든 " **Resource** "를 " **YourWorkSpaceName** "으로 선택 합니다.
* " **대상 하위 리소스** "에서 끝점 형식을 선택 합니다.
  * **Sql: sql** 풀에서 sql 쿼리를 실행 하는 데 사용할 수 있습니다.
  * **SqlOnDemand** : SQL 기본 제공 쿼리 실행 용입니다.
  * **Dev** : Synapse Studio 작업 영역 내에서 다른 모든 항목에 액세스 하는 데 사용할 수 있습니다. 이 형식의 개인 링크 끝점을 하나 이상 만들어야 합니다.

![Synapse studio 작업 영역에 대 한 개인 끝점을 만드는 중](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)

이제 모든 집합이 있습니다. Synapse studio 작업 영역 리소스에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[관리 작업 영역](./synapse-workspace-managed-vnet.md) 에 대 한 자세한 정보 Virtual Network

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대한 자세한 정보
