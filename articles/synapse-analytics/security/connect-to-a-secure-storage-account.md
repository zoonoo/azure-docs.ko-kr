---
title: Azure Synapse 작업 영역에서 보안 저장소 계정에 연결
description: 이 문서에서는 Azure Synapse 작업 영역에서 보안 저장소 계정에 연결 하는 방법을 설명 합니다.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101674319"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Synapse 작업 영역에서 보안 Azure storage 계정에 연결

이 문서에서는 Azure Synapse 작업 영역에서 보안 Azure storage 계정에 연결 하는 방법을 설명 합니다. 작업 영역을 만들 때 Azure storage 계정을 Synapse 작업 영역에 연결할 수 있습니다. 작업 영역을 만든 후 더 많은 저장소 계정을 연결할 수 있습니다.


## <a name="secured-azure-storage-accounts"></a>보안 Azure storage 계정
Azure storage는 저장소 계정에 대 한 액세스를 보호 하 고 제어 하는 데 사용할 수 있는 계층화 된 보안 모델을 제공 합니다. 선택한 공용 IP 주소 범위에서 저장소 계정에 대 한 액세스를 허용 하도록 IP 방화벽 규칙을 구성할 수 있습니다. 선택한 가상 네트워크에서 저장소 계정에 액세스 하는 트래픽을 허용 하도록 네트워크 규칙을 구성할 수도 있습니다. 선택한 IP 주소 범위 및 동일한 저장소 계정에서 선택한 가상 네트워크의 액세스를 허용 하는 네트워크 규칙에서 액세스를 허용 하는 IP 방화벽 규칙을 결합할 수 있습니다. 이러한 규칙은 저장소 계정의 공용 끝점에 적용 됩니다. 작업 영역에서 만든 관리 되는 개인 끝점에서 저장소 계정으로의 트래픽을 허용 하는 액세스 규칙은 필요 하지 않습니다. 저장소 방화벽 규칙을 만들 때 기존 저장소 계정 또는 새 저장소 계정에 적용할 수 있습니다. 저장소 계정을 보호 하는 방법에 대 한 자세한 내용은 [여기](../../storage/common/storage-network-security.md)를 참조 하세요.

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse 작업 영역 및 가상 네트워크
Synapse 작업 영역을 만들 때 관리 되는 가상 네트워크를 연결할 수 있도록 선택할 수 있습니다. 작업 영역을 만들 때 작업 영역에 대해 관리 되는 가상 네트워크를 사용 하도록 설정 하지 않으면 작업 영역이 공유 가상 네트워크와 연결 된 관리 되는 가상 네트워크를 포함 하지 않는 다른 Synapse 작업 영역과 함께 사용 됩니다. 작업 영역을 만들 때 관리 되는 가상 네트워크를 사용 하도록 설정한 경우 작업 영역은 Azure Synapse에서 관리 하는 전용 가상 네트워크와 연결 됩니다. 이러한 가상 네트워크는 고객 구독에서 생성 되지 않습니다. 따라서 위에 설명 된 네트워크 규칙을 사용 하 여 이러한 가상 네트워크에서 보안 저장소 계정에 액세스 하는 트래픽을 부여할 수 없습니다.  

## <a name="access-a-secured-storage-account"></a>보안 저장소 계정 액세스
Synapse는 네트워크 규칙에 포함할 수 없는 네트워크에서 작동 합니다. 작업 영역에서 보안 저장소 계정에 액세스할 수 있도록 하려면 다음을 수행 해야 합니다.

* 연결 된 관리 되는 가상 네트워크를 사용 하 여 Azure Synapse 작업 영역을 만들고,이 작업 영역에서 보안 저장소 계정으로 관리 되는 개인 끝점을 만듭니다.
* Azure Synapse 작업 영역에 보안 저장소 계정에 대 한 액세스를 신뢰할 수 있는 Azure 서비스로 부여 합니다. Azure Synapse는 신뢰할 수 있는 서비스로 서 강력한 인증을 사용 하 여 저장소 계정에 안전 하 게 연결 합니다.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>관리 되는 가상 네트워크를 사용 하 여 Synapse 작업 영역을 만들고 저장소 계정에 대 한 관리 되는 개인 끝점을 만듭니다.
[이러한 단계](./synapse-workspace-managed-vnet.md) 를 수행 하 여 연결 된 관리 가상 네트워크를 포함 하는 Synapse 작업 영역을 만들 수 있습니다. 연결 된 관리 되는 가상 네트워크가 있는 작업 영역을 만든 후에는 [여기](./how-to-create-managed-private-endpoints.md)에 나열 된 단계를 수행 하 여 보안 저장소 계정에 대 한 관리 되는 개인 끝점을 만들 수 있습니다. 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Azure Synapse 작업 영역에 보안 저장소 계정에 대 한 액세스 권한 부여를 신뢰할 수 있는 Azure 서비스로 부여
전용 SQL 풀 및 서버를 사용 하지 않는 SQL 풀과 같은 분석 기능은 관리 되는 가상 네트워크에 배포 되지 않은 다중 테 넌 트 인프라를 사용 합니다. 이러한 기능의 트래픽이 보안 저장소 계정에 액세스 하도록 하려면 다음 단계를 수행 하 여 작업 영역의 시스템 할당 관리 id에 따라 저장소 계정에 대 한 액세스를 구성 해야 합니다.

Azure Portal에서 보안 저장소 계정으로 이동 합니다. 왼쪽 탐색 창에서 **네트워킹** 을 선택 합니다. **리소스 인스턴스** 섹션에서 **리소스 유형** 으로 *Synapse/작업 영역* 을 선택 하 고 **인스턴스 이름** 에 작업 영역 이름을 입력 합니다. **저장** 을 선택합니다.

![저장소 계정 네트워크 구성.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

이제 작업 영역에서 보안 저장소 계정에 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[관리 작업 영역 가상 네트워크](./synapse-workspace-managed-vnet.md)에 대해 자세히 알아보세요.

[관리 되는 개인 끝점](./synapse-workspace-managed-private-endpoints.md)에 대해 자세히 알아보세요.