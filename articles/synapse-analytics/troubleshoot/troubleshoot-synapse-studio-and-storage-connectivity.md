---
title: Synapse Studio와 storage 간의 연결 문제 해결
description: Synapse Studio와 storage 간의 연결 문제 해결
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: 0b8a64d24242e6fb34c963b14429fdfee2398f62
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557870"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Azure Synapse Analytics Synapse Studio와 저장소 간 연결 문제 해결

Synapse Studio (미리 보기)에서 연결 된 저장소에 있는 데이터 리소스를 탐색할 수 있습니다. 이 가이드는 데이터 리소스에 액세스 하려고 할 때 연결 문제를 해결 하는 데 도움이 됩니다. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Case #1: 저장소 계정에 적절 한 사용 권한이 부족 합니다.

저장소 계정에 적절 한 권한이 없는 경우 Synapse Studio에서 "Data"--> "연결"을 통해 저장소 구조를 확장할 수 없습니다. 아래의 문제 증상 스크린샷을 참조 하세요. 

자세한 오류 메시지는 다를 수 있지만 오류 메시지의 일반적인 의미는 "이 요청은이 작업을 수행할 권한이 없습니다."입니다.

![저장소 연결 문제 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue.1.png)

**해결** 방법: 계정을 적절 한 역할에 할당 하려면 Azure Portal를 [사용 하 여 blob 및 큐 데이터에 액세스 하기 위한 Azure 역할 할당](../../storage/common/storage-auth-aad-rbac-portal.md) 을 참조 하세요.


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Case #2: 저장소 서버에 요청을 보내지 못했습니다.

Synapse Studio에서 "Data"--> "연결 됨"의 저장소 구조를 확장 하는 화살표를 선택 하면 왼쪽 패널에 "REQUEST_SEND_ERROR" 문제가 표시 될 수 있습니다. 아래 문제 증상 스크린샷을 참조 하세요.

![저장소 연결 문제 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue.2.png)

이 문제는 다음과 같은 몇 가지 이유로 발생할 수 있습니다.

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>저장소 리소스가 vNet 뒤에 있으며 저장소 개인 끝점을 구성 해야 합니다.

**해결** 방법:이 경우 저장소 계정에 대 한 저장소 개인 끝점을 구성 해야 합니다. VNet에 대 한 저장소 개인 끝점을 구성 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 blob 및 큐 데이터에 액세스할 수 있도록 Azure 역할 할당을](../security/how-to-connect-to-workspace-from-restricted-network.md)참조 하세요.

"Dfs.core.windows.net" 명령을 사용 하 여 \<storage-account-name\> 저장소 개인 끝점이 구성 된 후의 연결을 확인할 수 있습니다. "Privatelink.dfs.core.windows.net"과 유사한 문자열을 반환 해야 합니다. \<storage-account-name\>

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>저장소 리소스가 vNet 뒤에 있지 않지만 방화벽 구성으로 인해 Blob service (Azure AD) 끝점에 액세스할 수 없습니다.

**해결** 방법:이 경우 Azure Portal에서 저장소 계정을 열어야 합니다. 왼쪽 탐색 영역에서 **지원 + 문제 해결** 까지 아래로 스크롤하고 **연결 확인** 을 선택 하 여 **Blob service (Azure AD)** 연결 상태를 확인 합니다. 액세스할 수 없는 경우 승격 된 가이드에 따라 저장소 계정 페이지의 **방화벽 및 가상 네트워크** 구성을 확인 합니다. 저장소 방화벽에 대 한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../../storage/common/storage-network-security.md)을 참조 하세요.

### <a name="other-issues-to-check"></a>확인할 기타 문제 

* 액세스 하는 저장소 리소스가 Azure Data Lake Storage Gen2 되 고 방화벽 및 vNet (저장소 개인 끝점이 구성 된) 뒤에 있습니다.
* 액세스 하는 컨테이너 리소스가 삭제 되었거나 존재 하지 않습니다.


## <a name="next-steps"></a>다음 단계
이전 단계에서 문제를 해결 하는 데 도움이 되지 않는 경우 [지원 티켓을 만듭니다](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).
