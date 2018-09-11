---
title: SQL 호스팅 서버를 Azure Stack의 | Microsoft Docs
description: SQL 어댑터 리소스 공급자를 통해 프로 비전에 대 한 SQL 인스턴스를 추가 하는 방법입니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: c33d1fe1385619420215ec0f0fa3b0a2f90dddc0
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299460"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>SQL 리소스 공급자에 대 한 호스팅 서버를 추가 합니다.

가상 컴퓨터 (VM)에서 SQL 인스턴스를 호스트할 수 있습니다 [Azure Stack](azure-stack-poc.md), 또는 SQL 리소스 공급자 인스턴스에 연결할 수 있는 만큼 Azure Stack 환경 외부 VM.

## <a name="overview"></a>개요

SQL 호스팅 서버를 추가 하기 전에 다음 필수 및 일반 요구 사항을 검토 합니다.

### <a name="mandatory-requirements"></a>필수 요구 사항

* SQL Server 인스턴스에서 SQL 인증을 사용 합니다. VM의 SQL 리소스 공급자에는 도메인에 가입 하지, 않기 때문에 SQL 인증을 사용 하는 호스팅 서버에만 연결할 수 있습니다.
* Azure Stack에 설치 된 경우 공용 SQL 인스턴스에 대 한 IP 주소를 구성 합니다. 리소스 공급자 및 웹 앱과 같은 사용자는 SQL 인스턴스에이 네트워크에 대 한 연결이 필요 하므로 사용자 네트워크를 통해 통신 합니다.

### <a name="general-requirements"></a>일반 요구 사항

* 전용 리소스 공급자 및 사용자 작업에 사용할 SQL 인스턴스를 지정 합니다. 다른 소비자에 의해 사용 되는 SQL 인스턴스를 사용할 수 없습니다. 이 제한 사항은 App Services에도 적용 됩니다.
* (아래 설명 참조) 리소스 공급자에 대 한 적절 한 권한 수준을 사용 하 여 계정을 구성 합니다.
* SQL 인스턴스 및 해당 호스트를 관리 하기 위한 담당 합니다.  리소스 공급자 하지 업데이트 적용, 백업, 처리 또는 처리 하는 예를 들어, 회전 자격 증명입니다.

### <a name="sql-server-virtual-machine-images"></a>SQL Server 가상 머신 이미지

SQL IaaS 가상 머신 이미지 Marketplace 관리 기능을 통해 사용할 수 있습니다. 이러한 이미지는 Azure에서 사용할 수 있는 SQL Vm와 동일 합니다.

항상 최신 버전을 다운로드할 수 있는지 확인 합니다 **SQL IaaS 확장** 마켓플레이스 항목을 사용 하 여 SQL VM을 배포 하기 전에 합니다. IaaS 확장 및 해당 포털을 자동으로 패치 하는 등 추가 기능을 제공 하 고 백업 하는 향상 된 기능입니다. 이 확장에 대 한 자세한 내용은 참조 하세요. [SQL Server 에이전트 확장을 사용 하 여 Azure Virtual Machines에서 관리 작업을 자동화](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)합니다.

> [!NOTE]
> SQL IaaS 확장 _필요한_ marketplace에서 Windows 이미지의 모든 SQL에 대 한 VM 확장을 다운로드 하지 않은 경우 배포에 실패 합니다. Linux 기반 SQL 가상 머신 이미지를 사용 하 여 사용 되지 않습니다.

템플릿을 포함 하 여 SQL Vm을 배포 하기 위한 다른 옵션은는 [Azure Stack 빠른 시작 갤러리](https://github.com/Azure/AzureStack-QuickStart-Templates)합니다.

> [!NOTE]
> 사용자 구독에서 기본 공급자 구독이 아닌 다중 노드 Azure Stack에 설치 된 모든 호스팅 서버를 만들어야 합니다. 사용자 포털에서 또는 적절 한 로그인을 사용 하 여 PowerShell 세션에서 생성 되어야 합니다. 모든 호스팅 서버는 청구 가능한 Vm 및 적절 한 SQL 라이선스가 있어야 합니다. 서비스 관리자 _수_ 해당 구독의 소유자 여야 합니다.

### <a name="required-privileges"></a>필요한 권한

SQL sysadmin 보다 낮은 권한을 가진 관리자를 만들 수 있습니다. 사용자는 다음 작업에 대 한 권한만 필요 합니다.

* 데이터베이스: 만들기, 변경 포함 (에 대 한 유일한 Always On), 삭제, 백업
* 가용성 그룹: 변경, 추가/제거 데이터베이스 조인
* 로그인: 만들기, 선택, Alter, Drop, 취소
* 선택 작업: \[마스터\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), sys.databases \[마스터\].\[ sys\].\[ dm_os_sys_memory\]를 SERVERPROPERTY \[마스터\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files

### <a name="additional-security-information"></a>추가 보안 정보

다음 정보를 추가 보안 지침을 제공 합니다.

* 모든 Azure Stack 저장소는 Azure Stack에서 SQL 인스턴스에 암호화 된 blob 저장소를 사용 하 여 BitLocker를 사용 하 여 암호화 됩니다.
* SQL 리소스 공급자는 TLS 1.2를 완벽 하 게 지원합니다. TLS 1.2에 대 한 SQL RP를 통해 관리 되는 모든 SQL Server 구성 되어 있는지 확인 _만_ RP는 기본값은입니다. 모든 지원 되는 버전의 SQL Server 지원 TLS 1.2를 참조 하세요 [Microsoft SQL Server에 대 한 TLS 1.2 지원을](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server)합니다.
* 사용 하 여 SQL Server 구성 관리자를 설정 합니다 **ForceEncryption** SQL server에 대 한 모든 통신을 확인 하는 옵션은 항상 암호화 됩니다. 참조 [암호화 된 연결을 강제 하도록 서버를 구성 하려면](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections)합니다.
* 모든 클라이언트 응용 프로그램도 통신 하는 데 암호화 된 연결을 확인 합니다.
* RP는 SQL Server 인스턴스에 사용 되는 인증서를 신뢰 하도록 구성 됩니다.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>독립 실행형 SQL server를 호스트에 연결 하 여 용량을 제공 합니다.

독립 실행형을 사용할 수 있습니다 (비-HA) SQL Server 2014 또는 SQL Server 2016의 모든 버전을 사용 하 여 SQL server입니다. Sysadmin 권한이 있는 계정의 자격 증명이 있는지 확인 합니다.

이미 설정 되어 있는 독립 실행형 호스팅 서버를 추가 하려면 다음이 단계를 수행 합니다.

1. 서비스 관리자로 Azure Stack 연산자 포털에 로그인 합니다.

2. 선택 **모든 서비스** &gt; **관리 리소스** &gt; **SQL 호스팅 서버**합니다.

   ![SQL 호스팅 서버](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   아래 **SQL 호스팅 서버**, 리소스 공급자의 백 엔드로 사용할 SQL Server 인스턴스의 SQL 리소스 공급자를 연결할 수 있습니다.

   ![SQL 어댑터 대시보드](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. 클릭 **추가** 후에 SQL Server 인스턴스에 대 한 연결 세부 정보를 제공 하 고는 **SQL 호스팅 서버 추가** 블레이드입니다.

   ![SQL 호스팅 서버를 추가 합니다.](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    필요에 따라는 인스턴스 이름을 제공 하 고 인스턴스는 기본 포트 1433에 할당 되지 경우 포트 번호를 지정 합니다.

   > [!NOTE]
   > 사용자 및 관리자 Azure Resource Manager에서 SQL 인스턴스를 액세스할 수 있습니다,으로 리소스 공급자의 컨트롤에서 배치할 수 있습니다. SQL 인스턴스 __해야__ 리소스 공급자에 게 독점적으로 할당 됩니다.

4. 서버를 추가 하면 기존 SKU에 할당 하거나 새로운 SKU를 만들어야 해야 합니다. 아래 **호스팅 서버 추가**를 선택 **Sku**합니다.

   * 기존 SKU를 사용 하려면 사용 가능한 SKU를 선택 하 고 선택한 **만들기**합니다.
   * SKU를 만들려면 **+ 새 SKU 만들기**합니다. **만들 SKU**, 필요한 정보를 입력 하 고 선택한 **확인**합니다.

     ![SKU 만들기](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>SQL Always On 가용성 그룹을 사용 하 여 고가용성 제공

SQL Always On 인스턴스를 구성 하려면 추가 단계가 필요 하며 3 개의 Vm (또는 물리적 컴퓨터입니다.) 이 문서에서는 Always On 가용성 그룹의 숙지 이미 있다고 가정 합니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure virtual machines에서 SQL Server Always On 가용성 그룹 소개](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On 가용성 그룹 (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL 어댑터 리소스 공급자 _만_ 나중에 Always On 가용성 그룹에 대 한 인스턴스 또는 SQL 2016 SP1 Enterprise를 지원 합니다. 이 어댑터 구성 자동 시드 같은 새로운 SQL 기능에 필요 합니다.

### <a name="automatic-seeding"></a>자동 시드

사용 하도록 설정 해야 [자동 시드](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) SQL Server의 각 인스턴스에 대 한 각 가용성 그룹에 있습니다.

모든 인스턴스에서 자동 시드를 사용 하려면 편집 하 고 각 보조 인스턴스에 대 한 주 복제본에서 다음 SQL 명령을 실행 하십시오.

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

가용성 그룹 대괄호로 묶어야 합니다.

보조 노드에서 다음 SQL 명령을 실행 합니다.

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>포함 된 데이터베이스 인증 구성

가용성 그룹에 포함된 된 데이터베이스에 추가 하기 전에 contained database authentication 서버 옵션 가용성 그룹에 대 한 가용성 복제본을 호스팅하는 모든 서버 인스턴스에서 1로 설정 되어 있는지 확인 합니다. 자세한 내용은 [contained database authentication 서버 구성 옵션](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017)합니다.

이러한 명령을 사용 하 여 각 인스턴스에 대해 contained database authentication 서버 옵션을 설정 합니다.

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>SQL Always On 호스팅 서버 추가

1. 서비스 관리자로 Azure Stack 관리 포털에 로그인

2. 선택 **찾아보기** &gt; **관리 리소스** &gt; **SQL 호스팅 서버** &gt; **추가+**.

   아래 **SQL 호스팅 서버**, 리소스 공급자의 백 엔드로 사용 되는 SQL Server 인스턴스의 실제에 SQL Server 리소스 공급자를 연결할 수 있습니다.

3. SQL Server 인스턴스에 대 한 연결 세부 정보로 양식을 작성 하세요. Always On 수신기 (및 선택적 포트 번호와 인스턴스 이름을)의 FQDN 주소를 사용 하 고 있는지 확인 합니다. Sysadmin 권한으로 구성 된 계정에 대 한 정보를 제공 합니다.

4. Always On 가용성 그룹 확인란 SQL Always On 가용성 그룹 인스턴스에 대 한 지원을 사용 하도록 설정 합니다.

   ![Always On 사용](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. SQL Always On 인스턴스 SKU를 추가 합니다.

   > [!IMPORTANT]
   > 동일한 SKU에서 Always On 인스턴스를 사용 하 여 독립 실행형 서버를 혼합할 수 없습니다. 오류가 첫 번째 호스팅 서버 결과 추가한 후 유형을 혼합 하려고 합니다.

## <a name="sku-notes"></a>SKU 정보

서비스 제공을 구분 하기 위해 Sku를 사용할 수 있습니다. 예를 들어, 다음과 같은 특징이 있는 Sql 인스턴스를 할 수 있습니다.
  
* 큰 용량
* 고성능
* 고가용성

Sku는 특정 사용자 또는이 릴리스에서 그룹에 할당할 수 없습니다.

 Sku는 포털에 표시 되도록 한 시간이 걸릴 수 있습니다. 사용자는 SKU를 완벽 하 게 만들 때까지 데이터베이스를 만들 수 없습니다.

>[!TIP]
>반영 하는 SKU 이름을 사용 하 여 용량 및 성능과 같은 SKU의 서버 기능을 설명 합니다. 사용자가 적절 한 SKU를 해당 데이터베이스를 배포 하는 데 대 한 지원으로 이름이 사용 됩니다.

모범 사례로, SKU에서 모든 호스팅 서버가 동일한 리소스 및 성능 특징 있어야 합니다.

## <a name="make-the-sql-databases-available-to-users"></a>사용자에 게 SQL 데이터베이스 제공

계획 및 제품은 사용자에 대 한 SQL database에서 사용할 수 있도록 만듭니다. 추가 합니다 **Microsoft.SqlAdapter** 새 할당량을 만들고 서비스를 계획 합니다.

## <a name="next-steps"></a>다음 단계

[데이터베이스 추가](azure-stack-sql-resource-provider-databases.md)
