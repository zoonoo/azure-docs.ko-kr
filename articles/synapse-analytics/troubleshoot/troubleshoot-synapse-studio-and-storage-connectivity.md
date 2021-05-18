---
title: Synapse Studio와 스토리지 간의 연결 문제 해결
description: Synapse Studio와 스토리지 간의 연결 문제 해결
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117064"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Azure Synapse Analytics Synapse Studio와 스토리지 간 연결 문제 해결

Synapse Studio에서, 연결된 스토리지에 있는 데이터 리소스를 탐색할 수 있습니다. 이 가이드는 데이터 리소스에 액세스하려고 할 때 연결 문제를 해결하는 데 도움이 됩니다. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>사례 #1: 스토리지 계정에 적절한 권한이 없습니다.

스토리지 계정에 적절한 권한이 없는 경우 Synapse Studio "데이터" --> "연결"을 통해 스토리지 구조를 확장할 수 없습니다. 아래의 이슈 증상 스크린샷을 참조하세요. 

구체적 오류 메시지는 다를 수 있지만, 오류 메시지의 일반적인 의미는 "이 요청은 이 작업을 수행할 권한이 없습니다"입니다.

연결된 스토리지 노드의 경우,  
![스토리지 연결 이슈 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

스토리지 컨테이너 노드의 경우,  
![스토리지 연결 이슈 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**솔루션**: 계정을 적절한 역할에 할당하려면, [Azure Portal을 사용하여 Blob 및 큐 데이터에 액세스하기 위한 Azure 역할 할당 사용](../../storage/common/storage-auth-aad-rbac-portal.md)을 참조하세요.


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>사례 #2: 스토리지 서버에 요청을 보내지 못했습니다.

화살표를 선택하여 Synapse Studio "데이터" --> "연결"에서 스토리지 구조를 확장할 때 왼쪽 패널에 "REQUEST_SEND_ERROR" 이슈가 표시될 수 있습니다. 아래의 이슈 증상 스크린샷을 참조하세요.

연결된 스토리지 노드의 경우,  
![스토리지 연결 이슈 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

스토리지 컨테이너 노드의 경우,  
![스토리지 연결 이슈 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

이 이슈의 원인은 다음과 같은 여러 가지가 있을 수 있습니다.

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>스토리지 리소스는 vNet 뒤에 있으며, 스토리지 프라이빗 엔드포인트를 구성해야 합니다.

**솔루션**: 이 경우 스토리지 계정에 대한 스토리지 프라이빗 엔드포인트를 구성해야 합니다. VNet에 대해 스토리지 프라이빗 엔드포인트를 구성하는 방법에 대해서는 [Azure Portal을 사용하여 Blob 및 큐 데이터에 액세스하기 위한 Azure 역할 할당 사용](../security/how-to-connect-to-workspace-from-restricted-network.md)을 참조하세요.

"nslookup \<storage-account-name\>.dfs.core.windows.net" 명령을 사용하여 스토리지 프라이빗 엔드포인트가 구성된 후에 연결을 확인할 수 있습니다. 이것은 "\<storage-account-name\>.privatelink.dfs.core.windows.net"과 유사한 문자열을 반환해야 합니다.

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>스토리지 리소스가 vNet 뒤에 있지 않지만, 방화벽 구성으로 인해 Blob 서비스(Azure AD) 엔드포인트에 액세스할 수 없습니다.

**솔루션**: 이 경우 Azure Portal에서 스토리지 계정을 열어야 합니다. 왼쪽 탐색 영역에서 **지원 + 문제 해결** 까지 아래로 스크롤하고, **연결 확인** 을 선택하여 **Blob 서비스(Azure AD)** 연결 상태를 확인합니다. 액세스할 수 없는 경우, 추천된 가이드에 따라 스토리지 계정 페이지의 **방화벽 및 가상 네트워크** 구성을 확인합니다. 스토리지 방화벽에 대한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../../storage/common/storage-network-security.md)을 참조하세요.

### <a name="other-issues-to-check"></a>확인해야 할 다른 이슈 

* 액세스하는 스토리지 리소스가 Azure Data Lake Storage Gen2이고, 동시에 방화벽 및 vNet(스토리지 프라이빗 엔드포인트가 구성됨) 뒤에 있습니다.
* 액세스하는 컨테이너 리소스가 삭제되었거나 존재하지 않습니다.
* 교차 테넌트: 사용자가 로그인하는 데 사용한 작업 영역 테넌트가 스토리지 계정의 테넌트와 동일하지 않습니다. 


## <a name="next-steps"></a>다음 단계
이전 단계가 이슈를 해결하는 데 도움이 되지 않는 경우, [지원 티켓을 만듭니다](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).