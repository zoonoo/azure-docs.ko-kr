---
title: 공용 끝점 - 관리되는 인스턴스 구성
description: 관리되는 인스턴스에 대한 공용 끝점을 구성하는 방법 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256160"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Azure SQL Database 관리형 인스턴스에서 공용 엔드포인트 구성

[관리되는 인스턴스에](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) 대한 공용 끝점을 사용하면 가상 [네트워크](../virtual-network/virtual-networks-overview.md)외부에서 관리되는 인스턴스에 대한 데이터 액세스를 사용할 수 있습니다. Power BI, Azure 앱 서비스 또는 온-프레미스 네트워크와 같은 다중 테넌트 Azure 서비스에서 관리되는 인스턴스에 액세스할 수 있습니다. 관리되는 인스턴스에서 공용 엔드포인트를 사용하면 VPN 처리량 문제를 방지할 수 있는 VPN을 사용할 필요가 없습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> - Azure 포털에서 관리되는 인스턴스에 대한 공용 끝점을 사용하도록 설정합니다.
> - PowerShell을 사용하여 관리되는 인스턴스에 대한 공용 끝점 사용
> - 관리되는 인스턴스 공용 끝점에 대한 트래픽을 허용하도록 관리되는 인스턴스 네트워크 보안 그룹을 구성합니다.
> - 관리되는 인스턴스 공용 끝점 연결 문자열 구하기

## <a name="permissions"></a>사용 권한

관리되는 인스턴스에 있는 데이터의 민감도로 인해 관리되는 인스턴스 공용 끝점을 사용하도록 설정하는 구성에는 2단계 프로세스가 필요합니다. 이 보안 조치는 관세 분리(SoD)를 준수합니다.

- 관리되는 인스턴스에서 공용 끝점을 사용하도록 설정하려면 관리되는 인스턴스 관리자가 수행해야 합니다. 관리되는 인스턴스 관리자는 SQL 관리 인스턴스 리소스의 **개요** 페이지에서 찾을 수 있습니다.
- 네트워크 관리자가 수행해야 하는 네트워크 보안 그룹을 사용하여 트래픽을 허용합니다. 자세한 내용은 [네트워크 보안 그룹 사용 권한을](../virtual-network/manage-network-security-group.md#permissions)참조하십시오.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Azure 포털에서 관리되는 인스턴스에 대한 공용 끝점 사용

1. 에서 Azure 포털을 시작합니다.<https://portal.azure.com/.>
1. 관리되는 인스턴스를 사용하여 리소스 그룹을 열고 공용 끝점을 구성할 **SQL 관리 인스턴스를** 선택합니다.
1. **보안** 설정에서 가상 **네트워크** 탭을 선택합니다.
1. 가상 네트워크 구성 페이지에서 **활성화를** 선택한 다음 **저장** 아이콘을 선택하여 구성을 업데이트합니다.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>PowerShell을 사용하여 관리되는 인스턴스에 대한 공용 끝점 사용

### <a name="enable-public-endpoint"></a>공용 엔드포인트 사용

다음 PowerShell 명령을 실행합니다. **구독 ID를 구독 ID로** 바꿉습니다. 또한 **rg 이름을** 관리되는 인스턴스의 리소스 그룹으로 바꾸고 **mi 이름을** 관리되는 인스턴스의 이름으로 바꿉니다.

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

### <a name="disable-public-endpoint"></a>공용 끝점 사용 안 함

PowerShell을 사용하여 공용 끝점을 사용하지 않도록 설정하려면 다음 명령을 실행하고 인바운드 포트 3342에 대한 NSG를 닫는 것을 잊지 마십시오.

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>네트워크 보안 그룹의 공용 끝점 트래픽 허용

1. 관리되는 인스턴스의 구성 페이지가 여전히 열려 있는 경우 **개요** 탭으로 이동합니다. **SQL managed instance** 가상 **네트워크/서브넷** 링크를 선택하여 가상 네트워크 구성 페이지로 이동합니다.

    ![mi-개요.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. 가상 네트워크의 왼쪽 구성 창에 있는 **Subnets** 탭을 선택하고 관리되는 인스턴스에 대한 **보안 그룹을** 기록합니다.

    ![mi-vnet-서브넷.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. 관리되는 인스턴스가 포함된 리소스 그룹으로 돌아갑니다. 위에 명시된 **네트워크 보안 그룹** 이름이 표시됩니다. 네트워크 보안 그룹 구성 페이지로 이동할 이름을 선택합니다.

1. **인바운드 보안 규칙** 탭을 선택하고 다음 설정을 사용하여 **deny_all_inbound** 규칙보다 우선 순위가 높은 규칙을 **추가합니다.** </br> </br>

    |설정  |제안 값  |설명  |
    |---------|---------|---------|
    |**원본**     |모든 IP 주소 또는 서비스 태그         |<ul><li>Power BI와 같은 Azure 서비스의 경우 Azure 클라우드 서비스 태그를 선택합니다.</li> <li>컴퓨터 또는 Azure VM의 경우 NAT IP 주소를 사용합니다.</li></ul> |
    |**소스 포트 범위**     |*         |소스 포트는 일반적으로 동적으로 할당되어 예측할 수 없기 때문에 *(모든)에 둡니다. |
    |**대상**     |모두         |관리되는 인스턴스 서브넷으로의 트래픽을 허용하기 위해 대상을 Any로 둡게 합니다. |
    |**대상 포트 범위**     |3342         |관리되는 인스턴스 공용 TDS 끝점인 3342로의 범위 대상 포트 |
    |**프로토콜**     |TCP         |관리되는 인스턴스는 TDS에 TCP 프로토콜을 사용합니다. |
    |**작업**     |Allow         |공용 끝점을 통해 인바운드 트래픽관리 인스턴스 허용 |
    |**Priority**     |1300         |이 규칙이 **deny_all_inbound** 규칙보다 우선 순위가 높은지 확인합니다. |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Port 3342는 관리되는 인스턴스에 대한 공용 끝점 연결에 사용되며 이 시점에서 변경할 수 없습니다.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>관리되는 인스턴스 공용 끝점 연결 문자열 구하기

1. 공용 끝점에 대해 활성화된 SQL 관리 인스턴스 구성 페이지로 이동합니다. **설정** 구성에서 문자열 연결 탭을 **선택합니다.**
1. 공용 끝점 호스트 이름은> mi_name <형식으로 제공됩니다. **public**.<dns_zone>.database.windows.net이며 연결에 사용되는 포트는 3342입니다.

    ![mi-퍼블릭 엔드포인트-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>다음 단계

- 공용 [끝점을 사용하여 Azure SQL Database 관리 인스턴스를 안전하게 사용하는](sql-database-managed-instance-public-endpoint-securely.md)방법에 대해 알아봅니다.