---
title: 퍼블릭 엔드포인트 구성 - Azure SQL Managed Instance
description: Azure SQL Managed Instance의 퍼블릭 엔드포인트를 구성하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 02/08/2021
ms.openlocfilehash: 7d5f40be895aea26a234d9ae622aa5bf22528231
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981445"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[관리되는 인스턴스](./sql-managed-instance-paas-overview.md)의 퍼블릭 엔드포인트는 [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 외부에서 관리되는 인스턴스에 대한 데이터 액세스를 가능하게 합니다. Power BI, Azure App Service 또는 온-프레미스 네트워크와 같은 다중 테넌트 Azure 서비스에서 관리되는 인스턴스에 액세스할 수 있습니다. 관리되는 인스턴스의 퍼블릭 엔드포인트를 사용하여 VPN 처리량 문제를 방지하는 데 도움이 될 수 있는 VPN을 사용할 필요가 없습니다.

이 문서에서는 다음을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
>
> - Azure Portal에서 관리되는 인스턴스에 대한 퍼블릭 엔드포인트를 사용하도록 설정
> - PowerShell을 사용하여 관리되는 인스턴스에 대한 퍼블릭 엔드포인트를 사용하도록 설정
> - 관리되는 인스턴스 퍼블릭 엔드포인트에 대한 트래픽을 허용하도록 관리되는 인스턴스 네트워크 보안 그룹 구성
> - 관리되는 인스턴스 퍼블릭 엔드포인트 연결 문자열 가져오기

## <a name="permissions"></a>권한

관리되는 인스턴스에 있는 데이터의 민감도 때문에 관리되는 인스턴스 퍼블릭 엔드포인트를 사용하도록 구성하려면 2단계 프로세스가 필요합니다. 이 보안 조치는 SoD(의무 분리)를 따릅니다.

- 관리되는 인스턴스에서 퍼블릭 엔드포인트를 사용하도록 설정하는 작업은 관리되는 인스턴스 관리자가 수행해야 합니다. 관리되는 인스턴스 관리자는 관리되는 인스턴스 리소스의 **개요** 페이지에서 찾을 수 있습니다.
- 네트워크 보안 그룹을 사용하여 트래픽을 허용하는 작업은 네트워크 관리자가 수행해야 합니다. 자세한 내용은 [네트워크 보안 그룹 권한](../../virtual-network/manage-network-security-group.md#permissions)을 참조하세요.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Azure Portal에서 관리되는 인스턴스에 대한 퍼블릭 엔드포인트를 사용하도록 설정

1. <https://portal.azure.com/.>에서 Azure Portal을 시작합니다.
1. 관리되는 인스턴스를 사용하여 리소스 그룹을 열고 퍼블릭 엔드포인트를 구성하려는 **SQL Managed Instance** 를 선택합니다.
1. **보안** 설정에서 **가상 네트워크** 탭을 선택합니다.
1. 가상 네트워크 구성 페이지에서 **사용** 을 선택한 후 **저장** 아이콘을 선택하여 구성을 업데이트합니다.

![퍼블릭 엔드포인트를 사용하도록 설정한 SQL Managed Instance의 가상 네트워크 페이지를 보여 주는 스크린샷](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>PowerShell을 사용하여 관리되는 인스턴스에 대한 퍼블릭 엔드포인트를 사용하도록 설정

### <a name="enable-public-endpoint"></a>공용 엔드포인트 사용

다음 PowerShell 명령을 실행합니다. **subscription-id** 를 구독 ID로 바꿉니다. 또한 **rg-name** 을 관리형 인스턴스의 리소스 그룹으로 바꾸고 **mi-name** 을 관리형 인스턴스의 이름으로 바꿉니다.

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

### <a name="disable-public-endpoint"></a>퍼블릭 엔드포인트 사용 안 함

PowerShell을 사용하여 퍼블릭 엔드포인트를 사용하지 않도록 설정하려면 다음 명령을 실행합니다(구성한 경우 인바운드 포트 3342에 대한 NSG를 닫아야 함).

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>네트워크 보안 그룹에서 퍼블릭 엔드포인트 트래픽 허용

1. 관리되는 인스턴스의 구성 페이지가 열려 있는 경우 **개요** 탭으로 이동합니다. 그렇지 않으면 **SQL Managed Instance** 리소스로 돌아갑니다. **가상 네트워크/서브넷** 링크를 선택하면 가상 네트워크 구성 페이지로 연결됩니다.

    ![스크린샷은 가상 네트워크/서브넷 값을 찾을 수 있는 가상 네트워크 구성 페이지를 보여 줍니다.](./media/public-endpoint-configure/mi-overview.png)

1. 가상 네트워크의 왼쪽 구성 창에서 **서브넷** 탭을 선택하고 관리되는 인스턴스에 대한 **보안 그룹** 을 기록해 둡니다.

    ![스크린샷은 관리되는 인스턴스에 대한 보안 그룹을 가져올 수 있는 서브넷 탭을 보여 줍니다.](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. 관리되는 인스턴스가 포함된 리소스 그룹에 돌아갑니다. 위에 언급된 **네트워크 보안 그룹** 이름이 표시됩니다. 이름을 선택하여 네트워크 보안 그룹 구성 페이지로 이동합니다.

1. **인바운드 보안 규칙** 탭을 선택하고 다음 설정으로 **deny_all_inbound** 규칙보다 우선 순위가 높은 규칙을 **추가** 합니다. </br> </br>

    |설정  |제안 값  |설명  |
    |---------|---------|---------|
    |**원본**     |임의 IP 주소 또는 서비스 태그         |<ul><li>Power BI 같은 Azure 서비스의 경우 Azure 클라우드 서비스 태그를 선택합니다.</li> <li>컴퓨터 또는 Azure 가상 머신의 경우 NAT IP 주소를 사용합니다.</li></ul> |
    |**원본 포트 범위**     |* |원본 포트가 일반적으로 동적으로 할당되어 예측할 수 없게 되므로 *(모두)로 둡니다. |
    |**대상**     |모두         |관리되는 인스턴스 서브넷으로의 트래픽을 허용하려면 대상을 모두로 둡니다. |
    |**대상 포트 범위**     |3342         |대상 포트 범위를 관리되는 인스턴스 퍼블릭 TDS 엔드포인트인 3342로 지정합니다. |
    |**프로토콜**     |TCP         |SQL Managed Instance는 TDS에 대해 TCP 프로토콜을 사용합니다. |
    |**동작**     |허용         |퍼블릭 엔드포인트를 통해 관리되는 인스턴스에 대한 인바운드 트래픽을 허용합니다. |
    |**우선 순위**     |1300         |이 규칙을 **deny_all_inbound** 규칙보다 높은 우선 순위로 유지합니다. |

    ![스크린샷은 deny_all_inbound 규칙 위에 새 public_endpoint_inbound 규칙이 있는 인바운드 보안 규칙을 보여 줍니다.](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 포트 3342는 관리되는 인스턴스에 대한 퍼블릭 엔드포인트 연결에 사용되며, 지금은 변경할 수 없습니다.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>관리되는 인스턴스 퍼블릭 엔드포인트 연결 문자열 가져오기

1. 퍼블릭 엔드포인트에 대해 사용하도록 설정된 관리되는 인스턴스 구성 페이지로 이동합니다. **설정** 구성에서 **연결 문자열** 탭을 선택합니다.
1. 퍼블릭 엔드포인트 호스트 이름은 <mi_name>.**public**.<dns_zone>.database.windows.net 형식으로 제공되며 연결에 사용되는 포트는 3342입니다. SQL Server Management Studio 또는 Azure Data Studio 연결에 사용할 수 있는 퍼블릭 엔드포인트 포트를 나타내는 연결 문자열의 서버 값 예제는 `<mi_name>.public.<dns_zone>.database.windows.net,3342`입니다.

    ![스크린샷은 퍼블릭 및 프라이빗 엔드포인트에 대한 연결 문자열을 보여 줍니다.](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>다음 단계

[퍼블릭 엔드포인트로 안전하게 Azure SQL Managed Instance 사용](public-endpoint-overview.md)에 대해 자세히 알아봅니다.
