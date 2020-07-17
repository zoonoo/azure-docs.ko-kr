---
title: 가상 네트워크에서 풀 프로비저닝
description: 컴퓨팅 노드가 파일 서버와 같은 네트워크의 다른 VM과 안전하게 통신할 수 있도록 Azure 가상 네트워크에 Batch 풀을 만드는 방법입니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: seodec18
ms.openlocfilehash: cb5cda16cd9405f0cbe91a3f88be7dc3f582d21b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142771"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>가상 네트워크에서 Azure Batch 만들기

Azure Batch 풀을 만들 때는 지정한 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)(VNet)의 하위 집합에서 풀을 프로비전할 수 있습니다. 이 문서에서는 VNet에서 Batch 풀을 설정하는 방법을 설명합니다.

## <a name="why-use-a-vnet"></a>VNet을 사용하는 이유

풀의 계산 노드는 별도의 VNet을 요구 하지 않고 다중 인스턴스 작업을 실행 하기 위해와 같이 서로 통신할 수 있습니다. 그러나 기본적으로 풀의 노드는 라이선스 서버 또는 파일 서버와 같이 풀 외부에 있는 가상 컴퓨터와 통신할 수 없습니다.

계산 노드가 다른 가상 머신 또는 온-프레미스 네트워크와 안전 하 게 통신 하도록 허용 하려면 Azure VNet의 서브넷에 풀을 프로 비전 할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- **인증**. Azure VNet을 사용하려면 Batch 클라이언트 API가 Azure AD(Active Directory) 인증을 사용해야 합니다. Azure AD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다.

- **Azure VNet**. VNet 요구 사항 및 구성에 대한 다음 섹션을 참조하세요. 하나 이상의 서브넷으로 VNet을 미리 준비하기 위해 Azure Portal, Azure PowerShell, Azure CLI(명령줄 인터페이스) 또는 기타 방법을 사용할 수 있습니다.
  - Azure Resource Manager VNet을 만들려면 [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요. 리소스 관리자 기반 VNet은 새 배포에 권장 되며, 가상 컴퓨터 구성을 사용 하는 풀 에서만 지원 됩니다.
  - 클래식 VNet을 만들려면 [여러 서브넷이 있는 가상 네트워크(클래식) 만들기](/previous-versions/azure/virtual-network/create-virtual-network-classic)를 참조하세요. 클래식 VNet은 Cloud Services 구성을 사용 하는 풀 에서만 지원 됩니다.

## <a name="vnet-requirements"></a>VNet 요구 사항

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Azure Portal에서 VNet을 사용 하 여 풀 만들기

VNet을 만들고 서브넷을 할당한 후에는 해당 VNet으로 Batch 풀을 만들 수 있습니다. Azure Portal에서 풀을 만들려면 다음 단계를 수행합니다. 

1. Azure Portal에서 Batch 계정으로 이동합니다. 이 계정은 사용할 VNet이 포함된 리소스 그룹과 동일한 구독 및 지역에 있어야 합니다.
2. 왼쪽의 **설정** 창에서 **풀** 메뉴 항목을 선택합니다.
3. **풀** 창에서 **추가**를 선택 합니다.
4. **풀 추가** 창의 **이미지 형식** 드롭다운에서 사용할 옵션을 선택합니다.
5. 사용자 지정 이미지에 대해 올바른 **게시자/제품/Sku**를 선택합니다.
6. **노드 크기**, **대상 전용 노드** 및 **낮은 우선 순위 노드** 등 나머지 필수 설정과 선택 사항인 설정을 원하는 대로 지정합니다.
7. **가상 네트워크**에서 사용할 가상 네트워크와 서브넷을 선택합니다.

   ![가상 네트워크가 있는 풀 추가](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>강제 터널링을 위한 사용자 정의 경로

검사 및 로깅을 위해 조직의 요구 사항에 따라 서브넷의 인터넷 바인딩 트래픽을 온-프레미스 위치로 다시 리디렉션할 수 있습니다. 또한 VNet의 서브넷에 강제 터널링을 사용 하도록 설정 했을 수 있습니다.

강제 터널링을 사용 하는 VNet에서 풀의 노드가 작동 하도록 하려면 해당 서브넷에 대해 다음과 같은 udr ( [사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md) )을 추가 해야 합니다.

- 일괄 처리 서비스는 작업을 예약 하기 위해 노드와 통신 해야 합니다. 이 통신을 사용하려면 Batch 계정이 존재하는 지역의 Batch 서비스에서 사용하는 각 IP 주소에 대해 UDR을 추가해야 합니다. Batch 서비스의 IP 주소 목록을 가져오려면 [온-프레미스 서비스 태그](../virtual-network/service-tags-overview.md)를 참조 하세요.

- Azure Storage에 대 한 아웃 바운드 트래픽 (특히, 및 형식의 Url `<account>.table.core.windows.net` `<account>.queue.core.windows.net` `<account>.blob.core.windows.net` )이 온-프레미스 네트워크에 의해 차단 되지 않는지 확인 합니다.

UDR을 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 **다음 홉 유형**을 **인터넷**으로 설정합니다.

![사용자 정의 경로](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> Batch 서비스 IP 주소는 시간이 지남에 따라 변경될 수 있습니다. IP 주소 변경으로 인 한 중단을 방지 하려면 Batch 서비스 IP 주소를 자동으로 새로 고치는 프로세스를 만들어 경로 테이블에서 최신 상태로 유지 합니다.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Azure Portal에서 사용자 정의 경로를 만드는](../virtual-network/tutorial-create-route-table-portal.md)방법에 대해 알아봅니다.
