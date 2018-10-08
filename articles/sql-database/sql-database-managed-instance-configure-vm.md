---
title: 클라이언트 VM 연결 - Azure SQL Database Managed Instance | Microsoft Docs
description: Azure 가상 머신에서 SQL Server Management Studio를 사용하여 Azure SQL Database Managed Instance에 연결합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlrab, srbozovi, bonova
manager: craigg
ms.date: 09/13/2018
ms.openlocfilehash: 08b8e37493ea7bc549a2352aaa6714ef7c65bfdb
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159519"
---
# <a name="configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 연결하도록 Azure VM 구성

이 빠른 시작에서는 SSMS(SQL Server Management Studio)를 사용하여 Azure SQL Database Managed Instance에 연결하도록 Azure 가상 머신을 구성하는 방법을 보여줍니다. 지점 및 사이트 간 연결을 사용하여 온-프레미스 클라이언트 컴퓨터에서 연결하는 방법을 보여주는 빠른 시작은 [지점 및 사이트 간 연결 구성](sql-database-managed-instance-configure-p2s.md)을 참조하세요. 

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md) 빠른 시작에서 만든 리소스를 시작 지점으로 사용합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Managed Instance VNet에 새 서브넷 만들기

다음은 Azure 가상 머신이 Managed Instance에 연결할 수 있도록 Managed Instance VNet에 새 서브넷을 만드는 단계입니다. Managed Instance 서브넷은 Managed Instance 전용이며 해당 서브넷에 다른 리소스(예: Azure Virtual Machines)를 만들 수 없습니다. 

1. [Managed Instance 만들기](sql-database-managed-instance-get-started.md) 빠른 시작에서 만든 Managed Instance의 리소스 그룹을 열고 Managed Instance의 가상 네트워크를 클릭한 다음, **서브넷**을 클릭합니다.

   ![Managed Instance 리소스](./media/sql-database-managed-instance-configure-vm/resources.png)

2. **서브넷** 옆에 있는 **+** 기호를 클릭하여 새 서브넷을 만듭니다.

   ![Managed Instance 서브넷](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. 다음 표의 정보를 사용하여 양식에서 요청하는 정보를 입력합니다.

   | 설정| 제안 값 | 설명 |
   | ---------------- | ----------------- | ----------- | 
   | **Name** | 모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   | **주소 범위(CIDR 블록)** | 유효 범위 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **네트워크 보안 그룹** | 없음 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **경로 테이블** | 없음 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **서비스 엔드포인트** | 0개 선택됨 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **서브넷 위임** | 없음 | 이 빠른 시작에는 기본값이 적합합니다.|
 
   ![클라이언트 VM의 새 Managed Instance 서브넷](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. **확인**을 클릭하여 Managed Instance VNet에 추가 서브넷을 만듭니다.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Vnet의 새 서브넷에 가상 머신 만들기

다음 단계에서는 새 서브넷에서 Managed Instance에 연결하는 가상 머신을 만드는 방법을 보여줍니다. 

## <a name="prepare-the-azure-virtual-machine"></a>Azure 가상 머신 준비

SQL Managed Instance가 개인 Virtual Network에 배치되므로 Managed Instance에 연결하여 쿼리를 실행할 수 있도록 SQL Server Management Studio 또는 Azure Data Studio 같은 SQL 클라이언트 도구가 설치된 Azure VM을 만들어야 합니다. 이 빠른 시작에서는 SQL Server Management Studio를 사용합니다.

필요한 도구가 모두 포함된 클라이언트 가상 머신을 만드는 가장 쉬운 방법은 Azure Resource Manager 템플릿을 사용하는 것입니다.

1. 다음 단추를 클릭하여 클라이언트 가상 머신을 만들고 SQL Server Management Studio를 설치합니다(다른 브라우저 탭에서 Azure Portal에 로그인되었는지 확인).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. 다음 표의 정보를 사용하여 양식에서 요청하는 정보를 입력합니다.

   | 설정| 제안 값 | 설명 |
   | ---------------- | ----------------- | ----------- |
   | **구독** | 유효한 구독 | 새 리소스를 만들 권한이 있는 구독이어야 합니다. |
   | **리소스 그룹** |[Managed Instance 만들기](sql-database-managed-instance-get-started.md) 빠른 시작에서 지정한 리소스 그룹입니다.|VNet이 있는 리소스 그룹이어야 합니다.|
   | **위치**: | 리소스 그룹의 위치 | 선택한 리소스 그룹에 따라 이 값이 채워집니다. | 
   | **가상 머신 이름**  | 모든 유효한 이름 | 유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**관리 사용자 이름**|모든 유효한 사용자 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. "serveradmin"을 예약된 서버 수준 역할로 사용하지 않습니다.| 
   |**암호**|유효한 암호|암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
   | **가상 머신 크기** | 모든 유효한 크기 | 이 빠른 시작에서는 이 템플릿의 기본값인 **Standard_B2s로 충분합니다. |
   | **위치**:|[resourceGroup().location].| 이 값을 변경하지 마세요. |
   | **가상 네트워크 이름**|이전에 선택한 위치|지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.|
   | **서브넷 이름**|이전 절차에서 만든 서브넷의 이름| Managed Instance를 만든 서브넷을 선택하지 마세요.|
   | **아티팩트 위치** | [deployment().properties.templateLink.uri]. 이 값을 변경하지 마세요e |
   | **아티팩트 위치 Sas 토큰** | 비워 둡니다. | 이 값을 변경하지 마세요. |

   ![클라이언트 VM 만들기](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   [Managed Instance 만들기](sql-database-managed-instance-get-started.md)에서 추천 VNet 이름과 기본 서브넷을 사용한 경우 마지막 두 매개 변수를 변경할 필요가 없습니다. 그렇지 않은 경우 두 값을 네트워크 환경을 설정할 때 입력한 값으로는 변경해야 합니다.

3. **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다.
4. **구매**를 클릭하여 네트워크에서 Azure VM을 배포합니다.
5. **알림** 아이콘을 클릭하여 배포 상태를 확인합니다.
   
   Azure 가상 머신이 생성되기 전에는 진행하지 마세요. 

## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

다음 단계에서는 원격 데스크톱 연결을 사용하여 새로 만든 가상 머신에 연결하는 방법을 보여 줍니다.

1. 배포가 완료되면 가상 머신 리소스로 이동합니다.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. **Connect**를 클릭합니다. 
   
   가상 머신의 공용 IP 주소 및 포트 번호를 사용하여 원격 데스크톱 프로토콜 파일(.rdp 파일) 양식이 나타납니다. 

   ![RDP 양식](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. **RDP 파일 다운로드**를 클릭합니다.
 
   > [!NOTE]
   > SSH를 사용하여 VM에 연결할 수도 있습니다.

4. **가상 머신에 연결** 양식을 닫습니다.
5. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 
6. 메시지가 표시되면 **연결**을 클릭합니다. Mac의 Mac 앱 스토어에서 이 [원격 데스크톱 클라이언트](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)와 같은 RDP 클라이언트가 필요합니다.

6. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다.

7. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.

서버 관리자 대시보드에서 가상 머신에 연결되어 있습니다.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>SSMS를 사용하여 Managed Instance에 연결

1. 가상 머신에서 SSMS(SQL Server Management Studio)를 엽니다.
 
   열릴 때까지 몇 분이 걸립니다. SSMS를 처음 시작하는 것이므로 구성을 완료해야 하기 때문입니다.
2. **서버에 연결** 대화 상자에서, **서버 이름** 상자에 Managed Instance의 정규화된 **호스트 이름**을 입력하고, **SQL Server 인증**을 선택하고, 로그인 및 암호를 입력한 다음, **연결**을 클릭합니다.

    ![ssms 연결](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

연결되면 데이터베이스 노드에서 시스템 및 사용자 데이터베이스를 보고, 보안, 서버 개체, 복제, 관리, SQL Server 에이전트 및 XEvent 프로파일러 노드에서 다양한 개체를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 온-프레미스 클라이언트 컴퓨터에서 지점 및 사이트 간 연결을 사용하여 연결하는 방법을 보여주는 빠른 시작은 [지점 및 사이트 간 연결 구성](sql-database-managed-instance-configure-p2s.md)을 참조하세요.
- 응용 프로그램의 연결 옵션 개요는 [응용 프로그램을 Managed Instance에 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.
- 온-프레미스의 기존 SQL Server 데이터베이스를 Managed Instance로 복원하려면 [마이그레이션용 Azure DMS(Database Migration Service)](../dms/tutorial-sql-server-to-managed-instance.md)를 사용하여 데이터베이스 백업 파일에서 복원하거나 [T-SQL RESTORE 명령](sql-database-managed-instance-get-started-restore.md)을 사용하여 데이터베이스 백업 파일에서 복원하면 됩니다.
