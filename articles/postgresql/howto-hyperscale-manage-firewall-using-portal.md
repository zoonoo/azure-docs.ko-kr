---
title: 방화벽 규칙 관리 - 하이퍼스케일(Citus) - PostgreSQL용 Azure 데이터베이스
description: Azure 포털을 사용하여 PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에 대한 방화벽 규칙 생성 및 관리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977542"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 데이터베이스에 대한 방화벽 규칙 관리
서버 수준 방화벽 규칙을 사용하여 지정된 IP 주소 또는 IP 주소 범위에서 하이퍼스케일(Citus) 코디네이터 노드에 대한 액세스를 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 서버 그룹 [PostgreSQL - 하이퍼스케일(Citus) 서버 그룹에 대한 Azure 데이터베이스 만들기.](quickstart-create-hyperscale-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal에서 서버 수준 방화벽 규칙 만들기

> [!NOTE]
> 이러한 설정은 PostgreSQL - 하이퍼스케일(Citus) 서버 그룹에 대한 Azure 데이터베이스를 만드는 동안에도 액세스할 수 있습니다. **네트워킹** 탭에서 공용 **끝점을**클릭합니다.
> ![Azure 포털 - 네트워킹 탭](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. PostgreSQL 서버 그룹 페이지에서 보안 제목 아래에 **있는 네트워킹을** 클릭하여 방화벽 규칙을 엽니다.

   ![Azure 포털 - 네트워킹을 클릭합니다.](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. 도구 모음(아래 옵션 A) 또는 링크(옵션 B)에서 **클라이언트 IP 추가를**클릭합니다. 어느 쪽이든 Azure 시스템에서 인식한 대로 컴퓨터의 공용 IP 주소가 있는 방화벽 규칙을 자동으로 만듭니다.

   ![Azure 포털 - 클라이언트 IP 추가를 클릭합니다.](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

또는 **+Add 0.0.0 - 255.255.255.255(옵션** B의 오른쪽)를 클릭하면 IP뿐만 아니라 전체 인터넷이 코디네이터 노드의 포트 5432에 액세스할 수 있습니다. 이 경우 클라이언트는 여전히 클러스터를 사용 하려면 올바른 사용자 이름 및 암호로 로그인 해야 합니다. 그럼에도 불구하고 짧은 시간 동안만 전 세계에 액세스할 수 있도록 허용하고 비프로덕션 데이터베이스에만 액세스하는 것이 좋습니다.

3. 구성을 저장하기 전에 사용자의 IP 주소를 확인합니다. 상황에 따라 Azure Portal에서 관찰하는 IP 주소는 인터넷 및 Azure 서버에 액세스할 때 사용된 IP 주소와 다릅니다. 따라서, 규칙 함수를 예상대로 만들기 위해 시작 IP 및 끝 IP를 변경해야 할 수도 있습니다.
   검색 엔진 또는 다른 온라인 도구를 사용하여 사용자 고유의 IP 주소를 확인합니다. 예를 들어 "내 IP는 무엇입니까"를 검색합니다.

   ![내 IP 주소는 무엇입니까에 대한 Bing 검색](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 추가 주소 범위를 추가합니다. 방화벽 규칙에서 단일 IP 주소 또는 주소 범위를 지정할 수 있습니다. 단일 IP 주소에 규칙을 제한하려는 경우 시작 IP 및 끝 IP에 대한 필드에 동일한 주소를 입력합니다. 방화벽을 열면 관리자, 사용자 및 응용 프로그램이 포트 5432에서 코디네이터 노드에 액세스할 수 있습니다.

5. 도구 모음에서 **저장**을 클릭하여 이 서버 수준 방화벽 규칙을 저장합니다. 방화벽 규칙에 대한 업데이트가 성공적으로 수행되었는지 확인될 때까지 기다립니다.

## <a name="connecting-from-azure"></a>Azure에서 연결

Azure에서 호스팅되는 응용 프로그램(예: Azure Web Apps 응용 프로그램 또는 Azure VM에서 실행 중인 응용 프로그램)에 하이퍼스케일 데이터베이스 액세스를 쉽게 부여할 수 있습니다. **네트워킹** 창에서 포털에서 이 서버 그룹 옵션에 **액세스하도록 Azure 서비스 및 리소스 허용** 을 설정하고 **저장을**누르기만 하면 됩니다. **Yes**

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure 포털을 통해 기존 서버 수준 방화벽 규칙 관리
방화벽 규칙을 관리하는 단계를 반복합니다.
* 현재 컴퓨터를 추가하려면 + 클라이언트 **IP 추가**버튼을 클릭합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 추가 IP 주소를 추가 하려면 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력 합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 수정 하려면 규칙의 필드 중 하나를 클릭 후 변경 합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 삭제하려면 줄임표 [...]를 클릭하고 **삭제**를 클릭하여 규칙을 제거합니다. **저장**을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계
- 연결 문제 해결 방법을 비롯하여 [방화벽 개념 규칙에](concepts-hyperscale-firewall-rules.md)대해 자세히 알아봅니다.
