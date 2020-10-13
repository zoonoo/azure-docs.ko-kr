---
title: 방화벽 규칙 관리-Azure Portal-Azure Database for PostgreSQL 유연한 서버
description: Azure Portal를 사용 하 여 Azure Database for PostgreSQL 유연한 서버에 대 한 방화벽 규칙 만들기 및 관리
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8c107b88f964d8e657d6833fc3a2e8425d9053ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936920"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for PostgreSQL 유연한 서버에 대 한 방화벽 규칙 만들기 및 관리

> [!IMPORTANT]
> Azure Database for PostgreSQL-유연한 서버는 현재 공개 미리 보기로 제공 됩니다.

Azure Database for PostgreSQL 유연한 서버는 유연한 서버에 연결 하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원 합니다. 다음은 두 가지 옵션입니다.

* 퍼블릭 액세스(허용된 IP 주소)
* 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure Portal를 사용 하 여 **공용 액세스 (허용 된 IP 주소)** 로 PostgreSQL 서버를 만드는 방법에 중점을 두고 유연한 서버를 만든 후 방화벽 규칙을 관리 하는 방법에 대 한 개요를 제공 합니다. *공용 액세스 (허용 되는 ip 주소)* 를 사용 하는 경우 PostgreSQL 서버에 대 한 연결은 허용 된 ip 주소만으로 제한 됩니다. 클라이언트 IP 주소는 방화벽 규칙에서 허용 되어야 합니다. 자세히 알아보려면 [공용 액세스 (허용 된 IP 주소)](./concepts-networking.md#public-access-allowed-ip-addresses)를 참조 하세요. 방화벽 규칙은 서버를 만들 때 정의할 수 있습니다 (권장). 하지만 나중에 추가할 수도 있습니다. 이 문서에서는 공용 액세스 (허용 된 IP 주소)를 사용 하 여 방화벽 규칙을 만들고 관리 하는 방법에 대 한 개요를 제공 합니다.

## <a name="create-a-firewall-rule-when-creating-a-server"></a>서버를 만들 때 방화벽 규칙 만들기

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**(+)를 선택합니다.
2. **데이터베이스** > **PostgreSQL용 Azure Database**를 차례로 선택합니다. 검색 상자에 **PostgreSQL** 를 입력 하 여 서비스를 찾을 수도 있습니다.
3. **유연한 서버**를 배포 옵션으로 선택합니다.
4. **기본** 양식을 작성 합니다.
5. **네트워킹** 탭으로 이동 하 여 서버에 연결 하는 방법을 구성 합니다.
6. **연결 방법**에서 *공용 액세스 (허용 된 IP 주소)* 를 선택 합니다. **방화벽 규칙**을 만들려면 방화벽 규칙 이름 및 단일 IP 주소 또는 주소 범위를 지정 합니다. 규칙을 단일 IP 주소로 제한 하려는 경우 시작 IP 주소와 끝 IP 주소에 대 한 필드에 동일한 주소를 입력 합니다. 방화벽을 열면 관리자, 사용자 및 응용 프로그램이 유효한 자격 증명이 있는 PostgreSQL 서버의 데이터베이스에 액세스할 수 있습니다.
   > [!Note]
   > Azure Database for PostgreSQL 유연한 서버는 서버 수준에서 방화벽을 만듭니다. 특정 IP 주소에 대한 방화벽을 열기 위한 규칙을 만들지 않는 한, 이 방화벽은 외부 애플리케이션과 도구에서 서버 및 서버의 모든 데이터베이스에 연결하는 것을 방지합니다.
7. **검토 + 만들기**를 선택하여 유연한 서버 구성을 검토합니다.
8.  **만들기**를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

## <a name="create-a-firewall-rule-after-server-is-created"></a>서버를 만든 후 방화벽 규칙 만들기

1. [Azure Portal](https://portal.azure.com/)에서 방화벽 규칙을 추가 하려는 Azure Database for PostgreSQL 유연한 서버를 선택 합니다.
2. 유연한 서버 페이지의 **설정** 머리글에서 **네트워킹** 을 클릭 하 여 유연한 서버에 대 한 네트워킹 페이지를 엽니다.

   <!--![Azure portal - click Connection Security](./media/howto-manage-firewall-portal/1-connection-security.png)-->

3. 방화벽 규칙에서 **현재 클라이언트 IP 주소 추가** 를 클릭 합니다. 이렇게 하면 Azure 시스템에서 감지한 컴퓨터의 공용 IP 주소를 사용하는 방화벽 규칙이 자동으로 만들어집니다.

   <!--![Azure portal - click Add My IP](./media/howto-manage-firewall-portal/2-add-my-ip.png)-->

4. 구성을 저장하기 전에 사용자의 IP 주소를 확인합니다. 상황에 따라 Azure Portal에서 관찰하는 IP 주소는 인터넷 및 Azure 서버에 액세스할 때 사용된 IP 주소와 다릅니다. 따라서 규칙 기능이 예상 대로 작동 하도록 하려면 시작 IP 주소와 끝 IP 주소를 변경 해야 할 수 있습니다.

   검색 엔진 또는 기타 온라인 도구를 사용 하 여 사용자 고유의 IP 주소를 확인할 수 있습니다. 예를 들어 "내 IP는 무엇입니까"를 검색합니다.

   <!--![Bing search for What is my IP](./media/howto-manage-firewall-portal/3-what-is-my-ip.png)-->

5. 추가 주소 범위를 추가합니다. Azure Database for PostgreSQL 유연한 서버에 대 한 방화벽 규칙에서 단일 IP 주소 또는 주소 범위를 지정할 수 있습니다. 규칙을 단일 IP 주소로 제한 하려는 경우 시작 IP 주소와 끝 IP 주소에 대 한 필드에 동일한 주소를 입력 합니다. 방화벽을 열면 관리자, 사용자 및 응용 프로그램이 유효한 자격 증명이 있는 PostgreSQL 서버의 데이터베이스에 액세스할 수 있습니다.

   <!--![Azure portal - firewall rules](./media/howto-manage-firewall-portal/4-specify-addresses.png)-->

6. 도구 모음에서 **저장** 을 클릭 하 여이 방화벽 규칙을 저장 합니다. 방화벽 규칙에 대한 업데이트가 성공적으로 수행되었는지 확인될 때까지 기다립니다.

   <!--![Azure portal - click Save](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)-->

## <a name="connecting-from-azure"></a>Azure에서 연결

Azure에 배포 된 리소스 또는 응용 프로그램을 사용 하도록 설정 하 여 유연한 서버에 연결할 수 있습니다. 여기에는 Azure VM에서 실행 되는 Azure App Service에서 호스트 되는 웹 응용 프로그램, Azure Data Factory 데이터 관리 게이트웨이 등이 포함 됩니다. 

Azure 내의 응용 프로그램이 서버에 연결 하려고 하면 방화벽은 Azure 연결이 허용 되는지 확인 합니다. **네트워킹** 탭에서 포털의 azure **서비스 및 리소스에서이 서버에 대 한 공용 액세스 허용** 옵션을 선택 하 고 **저장**을 누릅니다.

이러한 연결을 사용 하도록 설정 하려면 리소스가 방화벽 규칙에 대해 동일한 VNet (가상 네트워크) 또는 리소스 그룹에 있을 필요가 없습니다. 연결 시도가 허용 되지 않으면 요청은 Azure Database for PostgreSQL 유연한 서버에 도달 하지 않습니다.

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
>
> 유연한 서버에 안전 하 게 액세스 하려면 **개인 액세스 (VNet 통합)** 를 선택 하는 것이 좋습니다.
>
## <a name="manage-existing-firewall-rules-through-the-azure-portal"></a>Azure Portal를 통해 기존 방화벽 규칙 관리

방화벽 규칙을 관리 하려면 다음 단계를 반복 합니다.

- 현재 컴퓨터를 추가 하려면 방화벽 규칙에서 + **현재 클라이언트 IP 주소 추가** 를 클릭 합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
- 추가 IP 주소를 추가 하려면 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력 합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
- 기존 규칙을 수정 하려면 규칙의 필드 중 하나를 클릭 후 변경 합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
- 기존 규칙을 삭제하려면 줄임표 [...]를 클릭하고 **삭제**를 클릭하여 규칙을 제거합니다. **저장**을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for PostgreSQL 유연한 서버에서 네트워킹](./concepts-networking.md) 에 대 한 자세한 정보
- [Azure Database for PostgreSQL 유연한 서버 방화벽 규칙](./concepts-networking.md#public-access-allowed-ip-addresses) 에 대해 자세히 알아보기
- [Azure CLI를 사용 하 여 Azure Database for PostgreSQL 방화벽 규칙을 만들고 관리](./how-to-manage-firewall-cli.md)합니다.