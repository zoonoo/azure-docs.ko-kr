---
title: 'Azure Portal: SQL Managed Instance 만들기 | Microsoft Docs'
description: SQL Managed Instance, 네트워크 환경 및 액세스용 클라이언트 VM을 만듭니다.
keywords: sql database 자습서, sql managed instance 만들기
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: quickstart
ms.date: 08/13/2018
ms.author: jovanpop-msft
ms.openlocfilehash: cb378c2d2773096992ef688653fd77b2625f8754
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42022691"
---
# <a name="create-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance 만들기

이 빠른 시작에서는 Azure에서 SQL Managed Instance를 만드는 방법을 안내합니다. Azure SQL Database Managed Instance는 Azure 클라우드에서 고가용성 SQL Server 데이터베이스를 실행하고 크기를 조정할 수 있게 해주는 PaaS(Platform-as-a-Service) SQL Server 데이터베이스 엔진입니다. 이 빠른 시작에서는 SQL Managed Instance를 만들어서 시작하는 방법을 보여줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="prepare-network-environment"></a>네트워크 환경 준비

SQL Managed Instance는 고객 고유의 VNet(Azure Virtual Network)에 배치되는 보안 서비스입니다. Managed Instance를 만들려면 다음이 포함된 Azure 네트워크 환경을 준비해야 합니다.
 - Managed Instance가 배치될 Azure VNet.
 - Managed Instance가 배치될 Azure VNet의 서브넷.
 - Managed Instance가 인스턴스를 제어하고 관리하는 Azure 서비스와 통신할 수 있게 해주는 사용자 정의 경로.

서브넷은 Managed Instance 전용이며 해당 서브넷에 다른 리소스(예: Azure Virtual Machines)를 만들 수 없습니다. 이 빠른 시작에서는 Managed Instance 전용 서브넷에 Managed Instance를 배치하고 기본 서브넷에 다른 리소스를 배치할 수 있도록 Azure VNet에 두 개의 서브넷을 만들 것입니다.

1. 다음 단추를 클릭하여 Azure SQL Database Managed Instance용으로 준비된 Azure 네트워크 환경을 배포합니다.

    <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener"> <img src="http://azuredeploy.net/deploybutton.png"> </a>

    이 단추를 누르면 Azure Portal에서 네트워크 환경을 배포하기 전에 구성할 수 있는 양식이 열립니다.

2. 필요에 따라 VNet 및 서브넷의 이름을 변경하고 네트워킹 리소스에 연결되는 IP 범위를 조정합니다. 그 후 "구매" 단추를 눌러 환경을 만들고 구성합니다.

    ![Managed Instance 환경 만들기](./media/sql-database-managed-instance-get-started/create-mi-network-arm.png)

    > [!Note]
    > 이 Azure Resource Manager 배포는 VNet에 두 개의 서브넷을 만듭니다. 하나는 **ManagedInstances**라고 하는 Managed Instance용이고, 다른 하나는 Managed Instance에 연결하는 데 사용할 수 있는 가상 클라이언트 머신 같은 기타 리소스에 사용되는 **Default**입니다. 두 개의 서브넷이 필요 없는 경우 두 번째 기본 서브넷을 삭제해도 되지만, 이 경우 이 빠른 시작 가이드의 3단계 [클라이언트 머신 준비](#prepare-client-machine)를 완료할 수 없게 됩니다.

    > [!Note]
    > VNet 및 서브넷 이름을 변경하는 경우 새 이름을 기억해 두어야 합니다. 그 다음 섹션에서 필요합니다. 자습서의 나머지 부분에서는 **MyNewVNet**이라는 VNet, SQL Managed Instances용 **ManagedInstances** 서브넷, 가상 머신용 **Default** 서브넷 및 기타 리소스를 만들었다고 가정합니다.

## <a name="create-a-managed-instance"></a>Managed Instance 만들기

다음 단계에서는 미리 보기가 승인된 후에 Managed Instance를 만드는 방법을 보여줍니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. 
  **Managed Instance**를 찾은 다음, **Azure SQL Database Managed Instance(미리 보기)** 를 선택합니다.
3. **만들기**를 클릭합니다.

   ![관리되는 인스턴스 만들기](./media/sql-database-managed-instance-tutorial/managed-instance-create.png)

4. 구독을 선택하고 미리 보기 약관이 **승인됨**으로 표시되는지 확인합니다.

   ![승인된 관리되는 인스턴스 미리 보기](./media/sql-database-managed-instance-tutorial/preview-accepted.png)

5. 다음 표의 정보를 사용하여 요청된 정보가 포함된 Managed Instance 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   |
  **관리되는 인스턴스 이름**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |
  **Managed Instance 관리자 로그인**|모든 유효한 사용자 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. "serveradmin"을 예약된 서버 수준 역할로 사용하지 않습니다.| 
   |**암호**|유효한 암호|암호는 16자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
   |**리소스 그룹**|이전에 만든 리소스 그룹||
   |**위치**:|이전에 선택한 위치|지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.|
   |**가상 네트워크**|이전에 만든 가상 네트워크| 이전 단계에서 이름을 변경하지 않은 경우 **MyNewVNet/ManagedInstances** 항목을 선택합니다. 변경한 경우 이전 섹션에서 입력한 VNet 이름 및 관리되는 인스턴스 서브넷을 선택합니다. **기본 서브넷은 Managed Instance를 호스트하도록 구성되지 않았기 때문에 사용하면 안 됩니다**. |

   ![관리되는 인스턴스 만들기 양식](./media/sql-database-managed-instance-tutorial/managed-instance-create-form.png)

6. **가격 책정 계층**을 클릭하여 계산 및 저장소 리소스의 크기를 조정하고 가격 책정 옵션을 검토합니다. 기본적으로 인스턴스에서 32GB의 저장소 공간을 추가 비용 없이 제공하며, **응용 프로그램에 충분하지 않을 수 있습니다**.
7. 슬라이더 또는 텍스트 상자를 사용하여 저장소 공간 및 가상 코어 수를 지정합니다. 
   
  ![관리되는 인스턴스 가격 책정 계층](./media/sql-database-managed-instance-tutorial/managed-instance-pricing-tier.png)

8. 완료되면 **적용**을 클릭하여 선택 사항을 저장합니다.  
9. 
  **만들기**를 클릭하여 Managed Instance를 배포합니다.
10. **알림** 아이콘을 클릭하여 배포 상태를 확인합니다.
 
   ![배포 진행률](./media/sql-database-managed-instance-tutorial/deployment-progress.png)

11. 
  **배포 진행 중**을 클릭하여 Managed Instance 창을 열어 배포 진행 상황을 자세히 모니터링합니다.
 
   ![배포 진행률 2](./media/sql-database-managed-instance-tutorial/managed-instance.png)

배포가 수행되는 동안 다음 절차를 계속합니다.

> [!IMPORTANT]
> 서브넷의 첫 번째 인스턴스에서는 배포 시간이 일반적으로 후속 인스턴스의 경우보다 훨씬 더 길며, 때로는 완료하는 데 24시간 이상 걸립니다. 배포 작업이 예상보다 오래 지속되므로 취소하지 마세요. 첫 번째 인스턴스를 배포하는 데 걸리는 시간은 일시적인 상황입니다. 공개 미리 보기를 시작한 직후에는 배포 시간이 크게 줄어들 것으로 예상합니다. 서브넷에 두 번째 Managed Instance를 만드는 작업은 몇 분 정도 걸립니다.

## <a name="prepare-client-machine"></a>클라이언트 머신 준비

SQL Managed Instance가 개인 Virtual Network에 배치되므로 Managed Instance에 연결하여 쿼리를 실행할 수 있도록 SQL Server Management Studio 또는 SQL Operations Studio 같은 SQL 클라이언트 도구가 설치된 Azure VM을 만들어야 합니다.

> [!Note]
> 클라이언트 Azure 가상 머신 대신, [지점 및 사이트 간](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 연결을 구성하고 로컬 머신에서 Managed Instance에 연결합니다.

필요한 도구가 모두 포함된 클라이언트 가상 머신을 만드는 가장 쉬운 방법은 Azure Resource Manager 템플릿을 사용하는 것입니다.

1. 다음 단추를 클릭합니다(다른 브라우저 탭에서 Azure Portal에 로그인되었는지 확인해야 함).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. 열리는 양식에서, VM에 연결하는 데 사용할 가상 머신 이름, 관리자 사용자 이름 및 암호를 입력합니다.

    ![클라이언트 VM 만들기](./media/sql-database-managed-instance-get-started/create-client-sql-vm.png)

    VNet 이름 및 기본 서브넷을 변경하지 않은 경우 마지막 두 매개 변수를 변경할 필요가 없습니다. 변경한 경우 이 값을 네트워크 환경을 설정할 때 입력한 값으로 변경해야 합니다.

3. "구매" 단추를 클릭하면 준비한 네트워크에 Azure VM이 배포됩니다.

4. 원격 데스크톱 연결을 사용하여 VM에 연결하고, 자동으로 VM에 설치된 SQL Server Management Studio 또는 SQL Operation Studio를 찾습니다.

5. SSMS를 열고 **서버 이름** 상자에 Managed Instance의 **호스트 이름**을 입력하고, **SQL Server 인증**을 선택하고, **서버에 연결** 대화 상자에 로그인 및 암호를 입력한 다음, **연결**을 클릭합니다.

    ![ssms 연결](./media/sql-database-managed-instance-tutorial/ssms-connect.png)  

연결되면 데이터베이스 노드에서 시스템 및 사용자 데이터베이스를 보고, 보안, 서버 개체, 복제, 관리, SQL Server 에이전트 및 XEvent 프로파일러 노드에서 다양한 개체를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

 - [응용 프로그램을 Managed Instance에 연결합니다](sql-database-managed-instance-connect-app.md).
 - [온-프레미스에서 Managed Instance로 데이터베이스를 마이그레이션합니다](sql-database-managed-instance-migrate.md).


