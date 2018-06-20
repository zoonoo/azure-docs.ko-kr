---
title: SQL 호스팅 서버에 Azure 스택 | Microsoft Docs
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265231"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>SQL 리소스 공급자에 대 한 호스팅 서버를 추가 합니다.

가상 컴퓨터 (VM)에서 SQL 인스턴스를 호스트할 [Azure 스택](azure-stack-poc.md), 또는 SQL 리소스 공급자의 인스턴스에 연결할 수 만큼 Azure 스택 환경 외부 VM에서 합니다.

## <a name="overview"></a>개요

SQL 호스팅 서버에 대 한 일반적인 요구 사항은 같습니다.

* 리소스 공급자 및 사용자 작업에 사용 하기 위해 SQL 인스턴스 전용 이어야 합니다. 다른 소비자에 의해 사용 되는 SQL 인스턴스를 사용할 수 없습니다. 이 제한은 응용 프로그램 서비스에도 적용 됩니다.
* VM의 SQL 리소스 공급자 도메인에 가입 없고 SQL 인증을 사용 하 여 연결할 수 있습니다.
* 리소스 공급자에서 사용 하기 위해 적절 한 권한이 있는 계정을 구성 해야 합니다.
* 리소스 공급자와 같이 웹 응용 프로그램 사용자를이 네트워크에서 SQL 인스턴스 연결이 필요 하므로 사용자 네트워크를 사용 합니다. 이 요구 사항은 SQL 인스턴스에 대 한 IP는 공용 네트워크에 있어야 합니다. 일반적으로 의미 합니다.
* SQL 인스턴스 및 해당 호스트의 관리 사용자의 책임입니다. 리소스 공급자 하지 않는 업데이트 적용, 백업, 처리 또는 처리 하는 예를 들어 자격 증명을 회전 합니다.
* Sku를 지 원하는 다양 한 종류의 성능 및 AlwaysOn을 사용 하 여 고가용성과 같은 SQL 기능을 사용할 수 있습니다.

### <a name="sql-server-virtual-machine-images"></a>SQL Server 가상 컴퓨터 이미지

SQL IaaS 가상 컴퓨터 이미지는 마켓플레이스 관리 기능을 통해 사용할 수 있습니다. 이러한 이미지는 Azure에서 사용할 수 있는 SQL Vm와 동일 합니다.

항상 최신 버전의를 다운로드할 수 있는지 확인은 **SQL IaaS 확장** 마켓플레이스 항목을 사용 하는 VM을 배포 하기 전에 합니다.  IaaS 확장 및 해당 포털 자동 패치 하는 등 추가 기능을 제공 하 고 백업 하는 향상 된 기능입니다.

서식 파일을 비롯 한 SQL Vm을 배포 하기 위한 다른 옵션은는 [Azure 스택 퀵 스타트 갤러리](https://github.com/Azure/AzureStack-QuickStart-Templates)합니다.

> [!NOTE]
> 사용자 구독에서 multi-node Azure 스택에 설치 된 모든 호스팅 서버를 만들어야 합니다. 기본 공급자 구독에서 만들 수 없습니다. PowerShell 세션을 적절 한 로그인 이나 사용자 포털에서 만들 수 있어야 합니다. 모든 호스팅 서버가 청구 한 대의 vm 및 적절 한 SQL 라이선스가 있어야 합니다. 서비스 관리자 _수_ 해당 구독의 소유자 여야 합니다.

### <a name="required-privileges"></a>필요한 권한

만들 수 있습니다 관리 사용자 권한이 낮은 사용 권한이 있는 SQL sysadmin입니다. 사용자는 다음 작업에 대 한 권한만 필요 합니다.

- 데이터베이스: 만들기, 변경 포함 (에 대 한 유일한 Always On), 삭제, 백업
- 가용성 그룹: 변경, 추가/제거 데이터베이스 조인
- 로그인: 만들기, 선택, Alter, Drop, 해지
- 작업 선택: \[마스터\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn), sys.databases \[마스터\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[마스터\].\[ sys\].\[ availability_groups\] (AlwaysOn) sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>독립 실행형 SQL server를 호스트에 연결 하 여 용량을 제공 합니다.

독립 실행형을 사용할 수 있습니다 (비-HA) SQL Server 2014 또는 SQL Server 2016의 모든 버전을 사용 하 여 SQL server. 시스템 관리자 권한이 있는 계정의 자격 증명을가지고 있는지 확인 합니다.

이미 설정 하는 독립 실행형 호스팅 서버를 추가 하려면 다음이 단계를 수행 합니다.

1. 서비스 관리자는 Azure 스택 연산자 포털에 로그인 합니다.

2. 선택 **찾아보기** &gt; **관리 리소스** &gt; **SQL 호스팅 서버**합니다.

   ![SQL 호스팅 서버](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   아래 **SQL 호스팅 서버**, 리소스 공급자의 백 엔드도 사용 되는 SQL Server의 인스턴스를 SQL 리소스 공급자에 연결할 수 있습니다.

   ![SQL 어댑터 대시보드](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. SQL Server 인스턴스의 연결 세부 정보도 폼을 채웁니다.

   ![SQL 호스팅 서버를 추가 합니다.](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    필요에 따라에 인스턴스 이름이 포함 하 고 인스턴스는 기본 포트 1433에 할당 되지 않은 경우 포트 번호를 지정 수 있습니다.

   > [!NOTE]
   > 사용자 및 관리자 Azure 리소스 관리자에서 SQL 인스턴스를 액세스할 수 있습니다,으로 리소스 공급자에 의해 제어 배치할 수 있습니다. SQL 인스턴스 __해야__ 리소스 공급자에만 할당할 수 있습니다.

4. 서버를 추가 하면 서비스 제공을 구분 하는 새로운 또는 기존 SKU에 할당 해야 합니다. 예를 들어 제공 하는 SQL Enterprise 인스턴스를 가질 수 있습니다.
  
   - 데이터베이스 용량
   - 자동 백업
   - 개별 부서에 대 한 고성능 서버 예약

   SKU에 있는 모든 호스팅 서버와 동일한 기능 있어야 합니다. **이름** 사용자가 적절 한 SKU에 데이터베이스를 배포할 수 있도록 SKU의 속성을 반영 해야 합니다.

   > [!IMPORTANT]
   > 특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다는 **제품군** 또는 **계층** SQL 및 MySQL 리소스 공급자에 대 한 SKU를 만들 때 이름을 지정 합니다.

   예: 

   ![SKU 만들기](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > Sku 포털에 표시 되도록 최대 한 시간이 걸릴 수 있습니다. SKU 완전히 생성 될 때까지 사용자가 데이터베이스를 만들 수 없습니다.

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>SQL Always On 가용성 그룹을 사용 하 여 고가용성을 제공 합니다.

추가 단계가 필요 합니다에 항상 SQL 인스턴스를 구성 하 고 최소 세 개의 Vm 또는 물리적 컴퓨터입니다. 이 문서는 Always On 가용성 그룹에 잘 알고 이미 있다고 가정 합니다. 자세한 내용은 다음을 참조하세요.

* [Azure 가상 컴퓨터에 SQL Server Always On 가용성 그룹을 소개](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On 가용성 그룹 (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL 어댑터 리소스 공급자 _만_ 나중에 Always On에 대 한 인스턴스 또는 SQL 2016 SP1 Enterprise를 지원 합니다. 이 어댑터 구성 자동 시드 같은 새로운 SQL 기능에 필요 합니다.

앞의 요구 사항 목록 외에도 설정 해야 [자동 시드](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) SQL Server의 각 인스턴스에 대해 각 가용성 그룹에 있습니다.

모든 인스턴스에 대 한 자동 시드를 사용 하려면 편집 하며 각 인스턴스에 대해 다음 SQL 명령을 실행 합니다.

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

보조 인스턴스에서 편집 하며 각 인스턴스에 대해 다음 SQL 명령을 실행 합니다.

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>SQL Always On 호스팅 서버를 추가 하려면

1. 서비스 관리자로 Azure 스택 관리 포털에 로그인

2. 선택 **찾아보기** &gt; **관리 리소스** &gt; **SQL 호스팅 서버** &gt; **+추가**.

   아래 **SQL 호스팅 서버** 리소스 공급자의 백 엔드도 사용 되는 SQL Server의 실제 인스턴스를 SQL Server 리소스 공급자를 연결할 수 있습니다.

3. SQL Server 인스턴스에 대 한 연결 세부 정보는 양식을 작성 합니다. 항상 수신기 (및 선택적 포트 번호입니다.)의 FQDN 주소를 사용 하 고 있는지 확인 Sysadmin 권한으로 구성 된 계정에 대 한 정보를 제공 합니다.

4. Always On 가용성 그룹 확인란 SQL Always On 가용성 그룹의 인스턴스에 대 한 지원을 사용 하도록 설정 합니다.

   ![항상 사용 하도록 설정](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. SKU에에 항상 SQL 인스턴스를 추가 합니다.

   > [!IMPORTANT]
   > Always On의 인스턴스와 SKU가 같은 독립 실행형 서버를 혼합할 수 없습니다. 오류가 첫 번째 호스팅 서버 결과 추가한 후 유형을 혼합 하려고 합니다.

## <a name="make-the-sql-databases-available-to-users"></a>사용자에 게 SQL 데이터베이스를 사용할 수 있게

계획 및 사용자에 대 한 SQL 데이터베이스를 사용할 수 있게 제안을 만듭니다. 추가 **Microsoft.SqlAdapter** 에서 계획에 서비스 및 기본 할당량을 추가 하거나 새 할당량을 만듭니다.

![계획 및 데이터베이스를 포함 하도록 제안 만들기](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>다음 단계

[데이터베이스를 추가 합니다.](azure-stack-sql-resource-provider-databases.md)
