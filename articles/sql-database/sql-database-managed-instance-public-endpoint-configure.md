---
title: 공용 끝점-Azure SQL Database 관리 되는 구성 인스턴스 | Microsoft Docs
description: 관리 되는 인스턴스에 대 한 공용 끝점을 구성 하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465126"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 되는 인스턴스에 공용 끝점을 구성 합니다.

에 대 한 공용 끝점을 [관리 되는 인스턴스](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) 외부에서 관리 되는 인스턴스에 대 한 데이터 액세스를 사용 하도록 설정 합니다 [가상 네트워크](../virtual-network/virtual-networks-overview.md)합니다. Power BI, Azure App Service 또는 온-프레미스 네트워크와 같은 다중 테 넌 트 Azure 서비스에서 관리 되는 인스턴스를 액세스할 수 있습니다. 공용 끝점에서 관리 되는 인스턴스를 사용 하 여 VPN 처리량 문제를 방지 하는 데 도움이 되는 VPN을 사용할 필요가 없습니다.

이 문서에서는 알아봅니다 방법:

> [!div class="checklist"]
> - Azure portal에서 관리 되는 인스턴스에 대 한 공용 끝점을 사용 하도록 설정
> - PowerShell을 사용 하 여 관리 되는 인스턴스에 대 한 공용 끝점을 사용 하도록 설정
> - 관리 되는 인스턴스 공용 끝점에 트래픽을 허용 하도록 관리 되는 인스턴스에 네트워크 보안 그룹 구성
> - 관리 되는 인스턴스 공용 끝점 연결 문자열 가져오기

## <a name="permissions"></a>권한

관리 되는 인스턴스에서 데이터의 민감도,으로 인해 관리 되는 인스턴스 공용 끝점을 사용 하도록 구성을 이루어집니다가 필요 합니다. 이 보안 조치 SoD (직무 분리)의 분리를 준수 합니다.

- 관리 되는 인스턴스 관리자가 수행 해야 하는 관리 되는 인스턴스에서 공용 끝점을 사용 하도록 설정 관리 되는 인스턴스 관리자에서 찾을 수 있습니다 **개요** SQL 페이지 인스턴스 리소스를 관리 합니다.
- 네트워크 관리자가 수행 해야 하는 네트워크 보안 그룹을 사용 하 여 트래픽 허용 자세한 내용은 [네트워크 보안 그룹 사용 권한](../virtual-network/manage-network-security-group.md#permissions)합니다.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Azure portal에서 관리 되는 인스턴스에 대 한 공용 끝점을 사용 하도록 설정

1. Azure portal 시작 <https://portal.azure.com/.>
1. 관리 되는 인스턴스를 사용 하 여 리소스 그룹을 열고 선택 합니다 **SQL 관리 되는 인스턴스** 에서 공용 끝점을 구성 하려면.
1. 에 **보안** 설정을 선택 합니다 **Virtual network** 탭 합니다.
1. 가상 네트워크 구성 페이지에서 선택 **을 사용 하도록 설정** 차례로 합니다 **저장** 구성을 업데이트 하는 아이콘입니다.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>PowerShell을 사용 하 여 관리 되는 인스턴스에 대 한 공용 끝점을 사용 하도록 설정

### <a name="enable-public-endpoint"></a>공용 끝점을 사용 하도록 설정

다음 PowerShell 명령을 실행합니다. 바꿉니다 **구독 id** 를 구독 ID 바꿔야 **rg 이름** 에 관리 되는 인스턴스 및 바꾸기에 대 한 리소스 그룹을 사용 하 여 **mi 이름** 관리 되는 인스턴스의 이름입니다.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>공용 끝점을 사용 하지 않도록 설정

PowerShell을 사용 하 여 공용 끝점을 사용 하지 않으려면 다음 명령을 실행 (하도 잊지 말고 3342 인바운드 포트에 대 한 NSG 구성 된 경우 닫기):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>네트워크 보안 그룹에서 공용 끝점 트래픽을 허용합니다

1. 아직 열려 관리 되는 인스턴스 구성 페이지에 있는 경우으로 이동 합니다 **개요** 탭 합니다. 이 고, 그렇지로 다시 이동 하 여 **SQL 관리 되는 인스턴스** 리소스. 선택 된 **가상 네트워크/서브넷** 링크 하 여 가상 네트워크 구성 페이지로 이동 됩니다.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. 선택 합니다 **서브넷** 가상 네트워크의 왼쪽된 구성 창에서 탭을 기록해 합니다 **보안 그룹** 관리 되는 인스턴스에 대 한 합니다.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. 관리 되는 인스턴스를 포함 하는 리소스 그룹으로 돌아갑니다. 표시 되어야 합니다 **네트워크 보안 그룹** 위에서 언급 한 이름입니다. 네트워크 보안 그룹 구성 페이지에 이름을 선택 합니다.

1. 선택 합니다 **인바운드 보안 규칙** 탭 및 **추가** 보다 더 높은 우선 순위의 규칙을 **deny_all_inbound** 다음 설정 사용 하 여 규칙: </br> </br>

    |설정  |제안 값  |설명  |
    |---------|---------|---------|
    |**원본**     |모든 IP 주소 또는 서비스 태그         |<ul><li>Power BI와 같은 Azure 서비스에 대 한 Azure 클라우드 서비스 태그를 선택 합니다.</li> <li>컴퓨터나 Azure VM에 대 한 NAT IP 주소를 사용 합니다.</li></ul> |
    |**원본 포트 범위**     |*         |이를 그대로 * (any) 원본 포트는 일반적으로 동적으로 할당 되 고와 같이 예측할 수 |
    |**대상**     |모두         |관리 되는 인스턴스 서브넷에 트래픽을 허용 하도록 모든 대상 유지 |
    |**대상 포트 범위**     |3342         |관리 되는 인스턴스 공용 TDS 끝점 인 3342에 범위 대상 포트 |
    |**프로토콜**     |TCP         |인스턴스는 TCP 프로토콜을 관리 되는 tds |
    |**작업**     |허용         |공용 끝점을 통해 관리 되는 인스턴스에 대 한 인바운드 트래픽을 허용 합니다. |
    |**Priority**     |1300         |이 규칙 보다 높은 우선 순위 인지 확인 합니다 **deny_all_inbound** 규칙 |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 3342 포트는 공용 끝점에 대 한 연결 인스턴스를 관리 하 고이 시점에서 변경할 수 없습니다.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>관리 되는 인스턴스 공용 끝점 연결 문자열 가져오기

1. 공용 끝점을 사용할 수 있는 SQL 관리 되는 인스턴스 구성 페이지로 이동 합니다. 선택 합니다 **연결 문자열** 탭의 **설정** 구성 합니다.
1. 공용 끝점 호스트 이름을 < mi_name > 형식으로 제공 되는 참고 합니다. **공용**. < dns_zone >. database.windows.net 및 연결에 사용 되는 포트가 3342 합니다.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [관리 되는 공용 엔드포인트를 사용 하 여 안전 하 게 인스턴스를 Azure SQL Database를 사용 하 여](sql-database-managed-instance-public-endpoint-securely.md)입니다.