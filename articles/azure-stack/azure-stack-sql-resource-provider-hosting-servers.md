---
title: "SQL 호스팅 서버에 Azure 스택 | Microsoft Docs"
description: "SQL 어댑터 리소스 공급자를 통해 프로 비전에 대 한 SQL 인스턴스를 추가 하는 방법"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 58c83b74041e0e2e82729f569c53aca59f3aed43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>SQL 어댑터에서 사용 하기 위해 호스팅 서버 추가

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

내부 Vm에서 SQL 인스턴스를 사용할 수 있습니다 프로그램 [Azure 스택](azure-stack-poc.md), 또는 리소스 공급자를 제공 하 여 Azure 스택 환경 외부에서 인스턴스 데이터베이스에 연결할 수 있습니다. 일반 요구 사항은 같습니다.

* SQL 인스턴스 RP 및 사용자 작업에 사용 하기 위해 전용 이어야 합니다. 응용 프로그램 서비스를 포함 한 모든 다른 소비자에 의해 사용 되는 SQL 인스턴스를 사용할 수 없습니다.
* RP 어댑터 도메인에 가입 되지 않은 및 SQL 인증을 사용 하 여 연결할 수 있습니다.
* RP에서 사용 하기 위해 적절 한 권한이 있는 계정을 구성 해야 합니다.
* RP에서 SQL로 네트워크 트래픽을 포트 1433을 사용 하 고 변경할 수 없습니다.
* RP 및 웹 앱 등 사용자가이 네트워크에서 SQL 인스턴스 연결이 필요 하므로 사용자 네트워크를 사용 합니다. 이 요구 사항은 SQL 인스턴스에 대 한 IP는 공용 네트워크에 있어야 합니다. 일반적으로 의미 합니다.
* SQL 인스턴스 및 호스트의 관리가 결정 합니다. RP가 패치, 백업 수행 하지, 회전 등 자격 증명.
* Sku 항상에 등 SQL 기능, 성능 등의 다양 한 클래스를 만드는 데 사용할 수 있습니다.



SQL IaaS 가상 컴퓨터 이미지의 많은 마켓플레이스 관리 기능을 통해 사용할 수 있습니다. 사용 하면 항상 마켓플레이스 항목을 사용 하는 VM을 배포 하기 전에 SQL IaaS 확장의 최신 버전을 다운로드 합니다. SQL 이미지는 Azure에서 사용할 수 있는 SQL Vm와 동일 합니다. 이러한 이미지를 IaaS 확장에서 만들어지고 포털의 향상 된 기능에 해당 vm의 SQL 자동 패치 및 백업 기능 등의 기능을 제공 합니다.

서식 파일을 비롯 한 SQL Vm을 배포 하기 위한 다른 옵션은는 [Azure 스택 퀵 스타트 갤러리](https://github.com/Azure/AzureStack-QuickStart-Templates)합니다.

> [!NOTE]
> 사용자 구독에서 multi-node Azure 스택에 설치 된 모든 호스팅 서버를 만들어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. PowerShell 세션을 적절 한 로그인 이나 사용자 포털에서 만들 수 있어야 합니다. 모든 호스팅 서버가 유료 대의 vm 및 적절 한 SQL 라이선스가 있어야 합니다. 서비스 관리자 _수_ 해당 구독의 소유자 여야 합니다.


### <a name="required-privileges"></a>필요한 권한

보다 작은 전체 sysadmin 권한으로 새 관리자를 만들 수 있습니다. 허용 해야 하는 특정 작업은 같습니다.

- 데이터베이스: 만들기, 삭제, 백업 (Always On만), 포함 된 변경
- 가용성 그룹: 변경, 추가/제거 데이터베이스 조인
- 로그인: 만들기, 선택, Alter, Drop, 해지
- 작업 선택: \[마스터\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), sys.databases \[마스터\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[마스터\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>독립 실행형 SQL server를 호스트에 연결 하 여 용량을 제공 합니다.
독립 실행형을 사용할 수 있습니다 (비-HA) SQL Server 2014 또는 SQL Server 2016의 모든 버전을 사용 하 여 SQL server. 시스템 관리자 권한이 있는 계정의 자격 증명을가지고 있는지 확인 합니다.

이미 프로 비전 하는 서버를 호스트 하는 독립 실행형을 추가 하려면 다음이 단계를 수행 합니다.

1. 서비스 관리자는 Azure 스택 관리자 포털에 로그인

2. 클릭 **찾아보기** &gt; **관리 리소스** &gt; **SQL 호스팅 서버**합니다.

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  **SQL 호스팅 서버** 블레이드는 위치에 리소스 공급자의 백 엔드도 처리 하는 실제 SQL Server 인스턴스에서 SQL Server 리소스 공급자를 연결할 수 있습니다.

  ![호스팅 서버](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. SQL Server 인스턴스의 연결 세부 정보도 폼을 채웁니다.

  ![새 호스팅 서버](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

  > [!NOTE]
  > 사용자 및 관리자 Azure 리소스 관리자에서 SQL 인스턴스를 액세스할 수 있습니다,으로 리소스 공급자에 의해 제어 배치할 수 있습니다. SQL 인스턴스 __해야__ RP에 독점적으로 할당할 수 있습니다.

4. 서버를 추가 하면 서비스 제공을 구분 하는 새로운 또는 기존 SKU에 할당 해야 합니다. 예를 들어 제공 하는 SQL Enterprise 인스턴스를 가질 수 있습니다.
  - 데이터베이스 용량
  - 자동 백업
  - 개별 부서에 대 한 고성능 서버 예약
  - 등에입니다.

  사용자가 해당 데이터베이스를 적절 하 게 배치할 수 있도록 SKU 이름 속성을 반영 해야 합니다. SKU의 모든 호스팅 서버와 동일한 기능 있어야 합니다.

    예제:

    ![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
Sku 포털에 표시 되도록 최대 한 시간이 걸릴 수 있습니다. SKU 완전히 생성 될 때까지 데이터베이스를 만들 수 없습니다.

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>SQL Always On 가용성 그룹을 사용 하 여 용량을 제공 합니다.
SQL Always On 인스턴스를 구성 하는 단계가 더 필요와 적어도 3 개의 Vm 또는 물리적 컴퓨터를 포함 합니다.

> [!NOTE]
> SQL 어댑터 RP _만_ 자동 시드 같은 새로운 SQL 기능을 해야 하므로 Always On에 대 한 SQL 2016 SP1 Enterprise 또는 이상 인스턴스를 지원 합니다. 목록 외에도 위의 일반적인 요구 사항:

* 파일 서버 컴퓨터에 항상 SQL도 제공 해야 합니다. 한 [Azure 스택 퀵 스타트 템플릿](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha) 드립니다이 환경을 만들 수 있는 합니다. 인스턴스를 직접 작성 하는 기준으로 사용할 수 있습니다 것입니다.

* SQL 서버를 설정 해야 합니다. 특히,를 사용 해야 [자동 시드](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) SQL Server의 각 인스턴스에 대해 각 가용성 그룹에 있습니다.

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

보조 인스턴스에서
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



에 항상 SQL 호스팅 서버를 추가 하려면 다음이 단계를 수행 합니다.

1. 서비스 관리자와 Azure 스택 관리자 포털에 로그인

2. 클릭 **찾아보기** &gt; **관리 리소스** &gt; **SQL 호스팅 서버** &gt; **+추가**.

    **SQL 호스팅 서버** 블레이드는 위치에 리소스 공급자의 백 엔드도 처리 하는 실제 SQL Server 인스턴스에서 SQL Server 리소스 공급자를 연결할 수 있습니다.


3. 항상에 수신기의 FQDN 또는 IPv4 주소를 사용 하 여 SQL Server 인스턴스의 연결 세부 정보도 폼을 채웁니다. 시스템 관리자 권한으로 구성 된 계정에 대 한 계정 정보를 제공 합니다.

4. SQL Always On 가용성 그룹의 인스턴스에 대 한 지원을 사용 하도록 설정 하려면이 확인란을 선택 합니다.

    ![호스팅 서버](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. SKU에에 항상 SQL 인스턴스를 추가 합니다. Always On의 인스턴스와 SKU가 같은 독립 실행형 서버를 혼합할 수 없습니다. 첫 번째 호스팅 서버를 추가할 때 결정 됩니다. 나중에 유형을 혼합 하는 동안 오류가 발생 합니다.


## <a name="making-sql-databases-available-to-users"></a>사용자에 게 SQL 데이터베이스 사용 가능

계획 및 사용자에 대 한 SQL 데이터베이스를 사용할 수 있게 제안을 만듭니다. 계획에 Microsoft.SqlAdapter 서비스를 추가 하 고 기존 할당량을 추가 하거나 새를 만듭니다. 할당량을 만들면 사용자 수 있도록 용량을 지정할 수 있습니다.

![계획 및 데이터베이스를 포함 하도록 제안 만들기](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>SQL 어댑터 RP의 유지 관리

SQL 인스턴스의 유지 관리 암호 회전 정보를 제외 하 고 여기에서 설명 하지 않습니다. 사용자는 패치 및 SQL 어댑터와 함께 사용 되는 데이터베이스 서버 백업/복구에 대 한입니다.

### <a name="patching-and-updating"></a>패치 및 업데이트
 SQL 어댑터는 추가 구성 요소 이므로 Azure 스택의 일환으로 서비스 되지 있습니다. Microsoft 업데이트를 필요에 따라 SQL 어댑터 제공할 것입니다. SQL 어댑터는 인스턴스화되는 _사용자_ 공급자 기본 구독에서 가상 컴퓨터. 따라서은 Windows 패치, 바이러스 백신 서명 등을 제공 해야 합니다. Windows는 Windows VM에 업데이트를 적용할 패치 업데이트 주기의 일부로 사용할 수 있습니다에 제공 되는 패키지를 업데이트 합니다. 업데이트 된 어댑터를 놓을 때 업데이트를 적용 하는 스크립트 제공 됩니다. 이 스크립트는 새 RP VM 만들고 이미 있는 모든 상태를 마이그레이션할 합니다.

 ### <a name="backuprestoredisaster-recovery"></a>백업/복원/장애 복구
 SQL 어댑터 백업 되지 않습니다 Azure 스택 BC DR 프로세스의 일부로 추가 구성 요소 이므로 합니다. 용이 하 게 스크립트를 제공 합니다.
- 필요한 상태 정보 (Azure 스택 저장소 계정에 저장)를 백업 합니다.
- 전체 스택 복구를 수행 해야 경우에 RP를 복원 합니다.
데이터베이스 서버를 복구 해야 먼저 (필요한 경우), RP 복원 되기 전에 합니다.

### <a name="updating-sql-credentials"></a>SQL 자격 증명 업데이트

사용자는 생성 및 SQL server에서 시스템 관리자 계정 유지 관리에 대 한입니다. RP 필요한 이러한 권한이 데이터베이스 사용자를 대신 하 여 관리할 수 있는 계정을-해당 데이터베이스의 데이터에 액세스할 필요는 없습니다. SQL server의 sa 암호를 업데이트 해야 할 경우에 RP에서 사용 되는 저장 된 암호를 변경 하려면 RP의 관리자 인터페이스의 업데이트 기능을 사용할 수 있습니다. 이 암호는 Azure 스택 인스턴스에서 키 자격 증명 모음에 저장 됩니다.

설정을 수정 하려면 **찾아보기** &gt; **관리 리소스** &gt; **SQL 호스팅 서버** &gt; **SQL 로그인** 로그인 이름을 선택 합니다. 변경 내용을 SQL 인스턴스에서 먼저 수행 되어야 합니다 (및 필요한 경우 모든 복제본). 에 **설정** 패널에서 클릭 **암호**합니다.

![관리자 암호를 업데이트 합니다.](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>다음 단계

[데이터베이스를 추가 합니다.](azure-stack-sql-resource-provider-databases.md)
