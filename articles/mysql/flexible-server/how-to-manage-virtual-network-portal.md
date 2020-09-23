---
title: 가상 네트워크 관리-Azure Portal-Azure Database for MySQL 유연한 서버
description: Azure Portal를 사용 하 여 Azure Database for MySQL 유연한 서버를 위한 가상 네트워크 만들기 및 관리
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 28506ea5afe085e9f52e40495d9931ae0aeddf32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90937313"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL 유연한 서버를 위한 가상 네트워크 만들기 및 관리

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Database for MySQL 유동 서버는 유동 서버에 연결하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원합니다. 다음은 두 가지 옵션입니다.

- 퍼블릭 액세스(허용된 IP 주소)
- 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure Portal를 사용 하 여 **개인 액세스 (VNet 통합)** 로 MySQL 서버를 만드는 방법에 중점을 둡니다. 개인 액세스 (VNet 통합)를 사용 하 여 유연한 서버를 [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 배포할 수 있습니다. Azure 가상 네트워크는 개인 및 보안 네트워크 통신을 제공 합니다. 개인 액세스를 사용 하면 MySQL server에 대 한 연결이 가상 네트워크로 제한 됩니다. 자세히 알아보려면 [개인 액세스 (VNet 통합)](./concepts-networking.md#private-access-vnet-integration)를 참조 하세요.

서버를 만드는 동안 유동 서버를 가상 네트워크 및 서브넷에 배포할 수 있습니다. 유동 서버를 배포한 후에는 다른 가상 네트워크, 서브넷 또는 *퍼블릭 액세스(허용된 IP 주소)* 로 이동할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항
가상 네트워크에서 유연한 서버를 만들려면 다음이 필요 합니다.
- [가상 네트워크](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 가상 네트워크 및 서브넷은 유연한 서버와 동일한 지역 및 구독에 있어야 합니다.

-  [서브넷](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)을 **Microsoft DBforMySQL/flexibleServers**에 위임. 이렇게 위임하면 Azure Database for MySQL 유동 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>기존 가상 네트워크에 Azure Database for MySQL 유연한 서버 만들기

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**(+)를 선택합니다.
2. **데이터베이스** > **Azure Database for MySQL**을 차례로 선택합니다. 검색 상자에서 **MySQL**을 입력하여 해당 서비스를 찾을 수도 있습니다.
3. **유연한 서버**를 배포 옵션으로 선택합니다.
4. **기본** 양식을 작성 합니다.
5. **네트워킹** 탭으로 이동 하 여 서버에 연결 하는 방법을 구성 합니다.
6. **연결 방법**에서 **개인 액세스 (VNet 통합)** 를 선택 합니다. **Virtual Network** 로 이동 하 고 위의 필수 구성 요소의 일부로 만든 기존 *가상 네트워크* 및 *서브넷* 을 선택 합니다.
7. **검토 + 만들기**를 선택하여 유연한 서버 구성을 검토합니다.
8. **만들기**를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

>[!Note]
> 유연한 서버가 가상 네트워크 및 서브넷에 배포 된 후에는이를 공용 액세스 (허용 된 IP 주소)로 이동할 수 없습니다.

## <a name="next-steps"></a>다음 단계
- [Azure CLI를 사용 하 여 Azure Database for MySQL 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
- [유연한 서버 Azure Database for MySQL의 네트워킹](./concepts-networking.md) 에 대 한 자세한 정보
- [유연한 서버 가상 네트워크 Azure Database for MySQL](./concepts-networking.md#private-access-vnet-integration)에 대해 자세히 알아봅니다.
