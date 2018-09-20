---
title: Azure Stack에서 항상 사용 가능한 SQL database 만들기 | Microsoft Docs
description: Azure Stack을 사용 하 여 호스트 컴퓨터 및 항상 사용 가능한 SQL AlwaysOn 데이터베이스를 SQL Server 리소스 공급자를 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b353b1c359ea663201b3fa5a72b28ab3298acd09
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369161"
---
# <a name="tutorial-create-highly-available-sql-databases"></a>자습서: 항상 사용 가능한 SQL 데이터베이스 만들기

Azure Stack 테 넌 트 사용자로 데이터베이스를 호스트할 SQL Server server Vm을 구성할 수 있습니다. Sql 호스팅 서버를 성공적으로 SQL 서비스에 가입한 사용자 SQL 데이터베이스를 쉽게 만들 수 생성 하 고 Azure Stack에서 관리 합니다.

이 자습서는 Azure Stack 빠른 시작 템플릿을 사용 하 여 만드는 방법을 보여 줍니다.는 [SQL Server AlwaysOn 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)으로 Azure Stack SQL 호스팅 서버를 추가 하 고 다음 항상 사용 가능한 SQL 데이터베이스를 만듭니다.

학습할 내용:

> [!div class="checklist"]
> * 템플릿에서 SQL Server AlwaysOn 가용성 그룹 만들기
> * Azure Stack SQL 호스팅 서버 만들기
> * 항상 사용 가능한 SQL database 만들기

이 자습서에서는 두 VM의 SQL Server AlwaysOn 가용성 그룹이 만들어지고 사용 가능한 Azure Stack의 마켓플레이스 항목을 사용 하 여 구성 합니다. 

이 자습서의 단계를 시작 하기 전에 Azure Stack 운영자가 사용할 수 있도록 다음 항목이 Azure Stack marketplace에서 확인 합니다.

> [!IMPORTANT]
> 다음의 모든 요소가 Azure Stack 빠른 시작 템플릿을 사용할 필요 합니다.

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) marketplace 이미지입니다.
- SQL Server 2016 SP1 또는 SP2 (Enterprise, Standard 또는 Developer) Windows Server 2016 서버 이미지에 있습니다. 이 자습서에서는 합니다 [Windows Server 2016에서 SQL Server 2016 SP2 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.sqlserver2016sp2enterprisewindowsserver2016) marketplace 이미지입니다.
- [SQL Server IaaS 확장](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) 1.2.30 버전 이상. 모든 Windows 버전에 대 한 SQL Server Marketplace 항목에 필요한 필수 구성 요소를 설치 하는 SQL IaaS 확장 합니다. SQL 관련 설정을 SQL virtual machines에서 구성할 수 있습니다. 확장 Marketplace 로컬에 설치 되어 있지 않으면, SQL의 프로 비전 하지 못합니다.
- [Windows 용 사용자 지정 스크립트 확장](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) 1.9.1 버전 이상. 사용자 지정 스크립트 확장은 자동으로 배포 후 VM 사용자 지정 작업을 시작 하려면 사용할 수 있는 도구입니다.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) 2.76.0.0 버전 이상. DSC는 배포 및 소프트웨어 서비스에 대 한 구성 데이터를 관리 하 고, 이러한 서비스가 실행 되는 환경 관리를 사용 하도록 설정 하는 Windows PowerShell의 관리 플랫폼입니다.

  > [!TIP]
  > 사용자 포털에서 VM을 만들 때 SQL Server IaaS 확장, 사용자 지정 스크립트 확장 또는 PowerSHell DSC 마켓플레이스 항목을 볼 수 없습니다. 이 자습서의 단계를 시작 하기 전에 Azure에서 이러한 항목 다운로드 된 되도록 Azure Stack 운영자에 게 문의 하십시오.

Azure Stack marketplace에 항목을 추가 하는 방법에 대 한 자세한 내용은 참조는 [Azure Stack Marketplace 개요](.\.\azure-stack-marketplace.md)합니다.

## <a name="create-a-sql-server-alwayson-availability-group"></a>SQL Server AlwaysOn 가용성 그룹 만들기
이 단원의 단계를 사용 하 여 사용 하 여 SQL Server AlwaysOn 가용성 그룹을 배포 합니다 [alwayson-2016-sql Azure Stack 빠른 시작 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson)합니다. 이 템플릿은 두 개의 SQL Server Enterprise, Standard 또는 Developer 인스턴스는 Always On 가용성 그룹에 배포합니다. 이 템플릿은 다음 리소스를 만듭니다.

- 네트워크 보안 그룹
- 가상 네트워크
- 네 개의 저장소 계정 (Active Directory (AD)에 대해 하나, SQL에 대 한, 파일 공유 감시에 대 한 및 VM 진단에 대 한)
- (각 SQL VM 및 SQL AlwaysOn 수신기에 바인딩된 공용 부하 분산 장치에 대 한 AD에 대해 하나씩) 4 개 공용 IP 주소
- SQL AlwaysOn 수신기에 바인딩된 공용 IP를 사용 하 여 SQL Vm에 대 한 하나의 외부 부하 분산 장치
- 단일 도메인을 사용 하 여 새 포리스트의 도메인 컨트롤러로 구성 된 하나의 VM (Windows Server 2016)
- 두 Vm (Windows Server 2016) 클러스터 및 SQL Server 2016 SP1 또는 SP2 Enterprise, Standard 또는 Developer Edition을 사용 하 여 구성 합니다. Marketplace 이미지 이어야 합니다.
- 클러스터에 대 한 파일 공유 감시로 구성 된 하나의 VM (Windows Server 2016)
- Vm의 SQL 및 파일 공유 감시를 포함 하는 하나의 가용성 집합  

> [!NOTE]
> (계산, 네트워크, 저장소 서비스)에 IaaS 기능을 제공 하는 구독을 사용 하 여 테 넌 트 사용자로 Azure Stack 사용자 포털에서 이러한 단계를 실행 합니다.

1. 사용자 포털에 로그인 합니다.
    - 통합된 시스템 배포의 경우 솔루션의 지역 및 외부 도메인 이름에 포털 주소 따라 달라 집니다. 형식의 됩니다 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
    - Azure Stack 개발 키트 (ASDK)를 사용 하는 경우 사용자 포털 주소가 [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external)합니다.

2. 선택 **\+** **리소스 만들기** > **Custom**를 차례로 **템플릿 배포**.

   ![사용자 지정 템플릿 배포](media/azure-stack-tutorial-sqlrp/custom-deployment.png)


3. 에 **사용자 지정 배포** 블레이드에서 **템플릿 편집** > **빠른 시작 템플릿** 다음에 사용 가능한 사용자 지정 템플릿 드롭다운 목록을 사용 하 여 선택 합니다 **alwayson-sql-2016** 템플릿을 클릭 **확인**를 차례로 **저장**합니다.

   ![빠른 시작 템플릿 선택](./media/azure-stack-tutorial-sqlrp/quickstart-template.png)


4. 에 **사용자 지정 배포** 블레이드에서 **매개 변수 편집** 기본값을 검토 하 고 있습니다. 모든 필수 매개 변수 정보를 제공 하 고 클릭 한 다음 필요에 따라 값을 수정 **확인**합니다.<br><br> 최소:

    - ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD, 및 SQLAUTHPASSWORD 매개 변수에 대 한 복잡 한 암호를 제공 합니다.
    - DNSSUFFIX 매개 변수의 모든 소문자 역방향 조회에 대 한 DNS 접미사를 입력 합니다. (**azurestack.external** ASDK 설치의 경우).
    
    ![사용자 지정 배포 매개 변수](./media/azure-stack-tutorial-sqlrp/edit-parameters.png)

5. 에 **사용자 지정 배포** 블레이드에서 새 리소스 그룹을 만들고 사용 하 여 구독을 선택 하거나 사용자 지정 배포에 대 한 기존 리소스 그룹을 선택 합니다.<br><br> 다음으로, 리소스 그룹 위치를 선택 (**로컬** ASDK 설치의 경우)을 클릭 한 다음 **만들기**합니다. 사용자 지정 배포 설정을 확인 하면 하 고 배포를 시작 하는 합니다.

    ![사용자 지정 배포 매개 변수](./media/azure-stack-tutorial-sqlrp/create-deployment.png)


6. 사용자 포털에서 선택 **리소스 그룹** 한 다음 리소스 그룹의 이름에 대해 만든 사용자 지정 배포 및 (단순히 **리소스 그룹** 이 예제에 대 한). 모든 배포가 성공적으로 완료 되도록 배포의 상태를 봅니다.<br><br>그런 다음, 리소스 그룹 항목을 검토 하 고 선택 합니다 **SQLPIPsql\<리소스 그룹 이름\>**  공용 IP 주소 항목입니다. 공용 IP 주소 및 부하 분산 장치 공용 IP의 전체 FQDN을 기록 합니다. 이 SQL AlwaysOn 가용성 그룹을 활용 하 여 SQL 호스팅 서버를 만들 수 있도록 Azure Stack 운영자에 게 제공 해야 합니다.

   > [!NOTE]
   > 템플릿 배포를 완료 하는 데 몇 시간이 걸립니다.

   ![사용자 지정 배포 매개 변수](./media/azure-stack-tutorial-sqlrp/deployment-complete.png)

### <a name="enable-automatic-seeding"></a>자동 시드를 사용 하도록 설정
템플릿이 성공적으로 배포 하 고 후에 SQL AlwaysON 가용성 그룹 구성, 설정 해야 합니다 [자동 시드](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) SQL Server 가용성 그룹의 각 인스턴스에 있습니다. 

자동 시드로 가용성 그룹을 만들 때 SQL Server 자동으로 만듭니다 모든 데이터베이스에 대 한 보조 복제본의 그룹 다른 수동 개입 없이 AlwaysOn 데이터베이스의 고가용성을 보장 하는 데 필요한.

이러한 SQL 명령을 사용 하 여 AlwaysOn 가용성 그룹에 대 한 자동 시드를 구성 합니다.

기본 SQL 인스턴스 (대체 <InstanceName> 주 인스턴스에서 SQL Server 이름):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![기본 SQL 인스턴스 스크립트](./media/azure-stack-tutorial-sqlrp/sql1.png)

보조 SQL 인스턴스 (AlwaysOn 가용성 그룹 이름으로 대체 < availability_group_name >):

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![보조 SQL 인스턴스 스크립트](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>포함 된 데이터베이스 인증 구성
가용성 그룹에 포함된 된 데이터베이스에 추가 하기 전에 contained database authentication 서버 옵션 가용성 그룹에 대 한 가용성 복제본을 호스팅하는 모든 서버 인스턴스에서 1로 설정 되어 있는지 확인 합니다. 자세한 내용은 [포함 된 데이터베이스 인증](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017)합니다.

이러한 명령을 사용 하 여 가용성 그룹에서 각 SQL Server 인스턴스에 대 한 포함 된 데이터베이스 인증 서버 옵션을 설정 합니다.

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![집합에 포함 된 데이터베이스 인증](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Azure Stack SQL 호스팅 서버 만들기
SQL Server AlwayOn 가용성 그룹 생성 되 고 올바르게 구성 된 후 Azure Stack 운영자 Azure Stack SQL 호스팅 서버를 추가 용량 사용자가 데이터베이스를 만들 수 있게 만들어야 합니다. 

SQL AlwaysOn 가용성 그룹의 리소스 그룹을 만들 때 했던 SQL 부하 분산 장치의 공용 IP 이전에 기록에 대 한 공용 IP 또는 FQDN 전체 Azure Stack 운영자를 제공 해야 합니다. (**SQLPIPsql\<리소스 그룹 이름\>**). 또한 연산자는 SQL Server AlwaysOn 가용성 그룹에서 SQL 인스턴스를 액세스 하는 데 인증 자격 증명을 알아야 할 수 있습니다.

> [!NOTE]
> 이 단계는 Azure Stack 운영자가 Azure Stack 관리 포털에서 실행 되어야 합니다.

SQL AlwaysOn 가용성 그룹의 부하 분산 장치 수신기 테 넌 트 사용자가 제공 된 공용 IP 및 SQL 인증 로그인 정보를 사용 하 여 Azure Stack 운영자 수 이제 [SQL AlwaysOn 가용성 그룹을 사용 하 여 SQL 호스팅 서버 만들기 ](.\.\azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

또한 Azure Stack 운영자가 계획을 생성 하 고 SQL AlwaysOn 데이터베이스 생성을 사용자에 대해 사용할 수 있도록 제공을 확인 합니다. 연산자를 추가 해야 합니다 **Microsoft.SqlAdapter** 계획에 서비스 및 항상 사용 가능한 데이터베이스에 맞게 새 할당량 만들기. 계획을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [계획, 제안, 할당량 및 구독 개요](.\.\azure-stack-plan-offer-quota-overview.md)합니다.

> [!TIP]
> 합니다 **Microsoft.SqlAdapter** 서비스까지 계획에 추가할 수 없습니다는 [SQL Server 리소스 공급자가 배포 된](.\.\azure-stack-sql-resource-provider-deploy.md)합니다.

## <a name="create-a-highly-available-sql-database"></a>항상 사용 가능한 SQL database 만들기
SQL AlwaysOn 가용성 그룹 생성, 구성 및 Azure Stack SQL 호스팅 서버와 Azure Stack 운영자가 추가 된 후 SQL Server 데이터베이스 기능을 포함 하 여 구독을 사용 하 여 테 넌 트 사용자를 지 원하는 SQL 데이터베이스를 만들 수 있습니다. 이 섹션의 단계를 수행 하 여 AlwaysOn 기능입니다. 

> [!NOTE]
> SQL Server 기능 (Microsoft.SQLAdapter 서비스)을 제공 하는 구독을 사용 하 여 테 넌 트 사용자로 Azure Stack 사용자 포털에서 이러한 단계를 실행 합니다.

1. 사용자 포털에 로그인 합니다.
    - 통합된 시스템 배포의 경우 솔루션의 지역 및 외부 도메인 이름에 포털 주소 따라 달라 집니다. 형식의 됩니다 https://portal.&lt; *지역*&gt;.&lt; *FQDN*&gt;합니다.
    - Azure Stack 개발 키트 (ASDK)를 사용 하는 경우 사용자 포털 주소가 [ https://portal.local.azurestack.external ](https://portal.local.azurestack.external)합니다.

2. 선택 **\+** **리소스 만들기** > **데이터 \+ Storage**를 차례로 **SQL Database**합니다.<br><br>이름, 데이터 정렬, 최대 크기 및 구독, 리소스 그룹 및 위치는 배포에 사용할를 포함 하 여 필요한 데이터베이스 속성 정보를 제공 합니다. 

   ![SQL database 만들기](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. 선택 **SKU** 를 선택한 다음 적절 한 SQL 호스팅 서버 SKU를 사용 합니다. 이 예제에서는 Azure Stack 운영자 만들었습니다 합니다 **엔터프라이즈-HA** SQL AlwaysOn 가용성 그룹에 대 한 고가용성을 지원 하려면 SKU입니다.

   ![SKU 선택](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. 선택 **로그인** > **새 로그인을 만듭니다** 새 데이터베이스에 사용 되는 SQL 인증 자격 증명을 입력 합니다. 완료 되 면, 클릭 **확인** 차례로 **만들기** 데이터베이스 배포 프로세스를 시작 합니다.

   ![로그인 만들기](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. SQL database 배포가 성공적으로 완료 되 면 새 항상 사용 가능한 데이터베이스에 연결할 때 사용할 연결 문자열을 검색 하는 데이터베이스 속성을 검토 합니다. 

   ![연결 문자열 보기](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 템플릿에서 SQL Server AlwaysOn 가용성 그룹 만들기
> * Azure Stack SQL 호스팅 서버 만들기
> * 항상 사용 가능한 SQL database 만들기

에 대해 알아보려면 다음 자습서로 이동 하는 방법.
> [!div class="nextstepaction"]
> [항상 사용 가능한 MySQL 데이터베이스 만들기](azure-stack-tutorial-mysql.md)