---
title: Azure Vm에서 SQL Server 백업에 대 한 Azure Backup 지원 매트릭스
description: Azure Backup 서비스를 사용 하 여 Azure Vm의 SQL Server를 백업할 때 지원 설정 및 제한 사항에 대 한 요약을 제공 합니다.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: b189eceb6b5a7f2e508387c0b91b238ff5fcb088
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92174062"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Azure Vm의 SQL Server 백업에 대 한 지원 매트릭스

Azure Backup를 사용 하 여 Microsoft Azure 클라우드 플랫폼에서 호스트 되는 Azure Vm에서 SQL Server 데이터베이스를 백업할 수 있습니다. 이 문서에서는 Azure Vm에서 SQL Server 백업의 시나리오 및 배포에 대 한 일반 지원 설정 및 제한 사항을 요약 합니다.

## <a name="scenario-support"></a>시나리오 지원

**지원** | **세부 정보**
--- | ---
**지원되는 배포** | SQL Marketplace Azure VM 및 비 Marketplace VM(SQL Server가 수동 설치됨)이 지원됩니다.
**지원되는 지역** | 오스트레일리아 남동부(ASE), 오스트레일리아 동부(AE), 오스트레일리아 중부(AC), 오스트레일리아 중부 2(AC) <br> 브라질 남부(BRS)<br> 캐나다 중부(CNC), 캐나다 동부(CE)<br> 동남 아시아(SEA), 동아시아(EA) <br> 미국 동부(EUS), 미국 동부 2(EUS2), 미국 중서부(WCUS), 미국 서부(WUS), 미국 서부 2(WUS 2), 미국 중북부(NCUS), 미국 중부(CUS), 미국 중남부(SCUS) <br> 인도 중부(INC), 인도 남부(INS), 인도 서부 <br> 일본 동부(JPE), 일본 서부(JPW) <br> 한국 중부(KRC), 한국 남부(KRS) <br> 북유럽(NE), 서유럽 <br> 영국 남부(UKS), 영국 서부(UKW) <br> US Gov 애리조나, US Gov 버지니아, US Gov 텍사스, US DoD 중부, US DoD 동부 <br> 독일 북부, 독일 중서부 <br> 스위스 북부, 스위스 서부 <br> 프랑스 중부 <br> 중국 동부, 중국 동부 2, 중국 북부, 중국 북부 2
**지원 되는 운영 체제** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux는 현재 지원되지 않습니다.
**지원되는 SQL Server 버전** | [제품 수명 주기 페이지 검색](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)에 설명된 SQL Server 2019, SQL Server 2017, [제품 수명 주기 페이지 검색](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)에 설명된 SQL Server 2016 및 SP, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express<br><br>Express Local DB 버전은 지원 되지 않습니다.
**지원되는 .NET 버전** | VM에 설치된 .NET Framework 4.5.2 이상

## <a name="feature-considerations-and-limitations"></a>기능 고려 사항 및 제한 사항

|설정  |최대 제한 |
|---------|---------|
|서버 및 자격 증명 모음에서 보호할 수 있는 데이터베이스 수    |   2000      |
|지원 되는 데이터베이스 크기 (이 외에 성능 문제가 발생할 수 있음)   |   2TB      |
|데이터베이스에서 지원 되는 파일 수    |   1000      |

>[!NOTE]
> [자세한 Resource Planner를 다운로드](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) 하 여 VM 리소스, 대역폭 및 백업 정책에 따라 서버 별로 권장 되는 보호 된 데이터베이스의 대략적인 수를 계산 합니다.

* SQL Server 백업은 Azure Portal 또는 **PowerShell**에서 구성할 수 있습니다. CLI는 지원 되지 않습니다.
* 솔루션은 Azure Resource Manager VM과 클래식 VM의 두 종류 [배포](../azure-resource-manager/management/deployment-models.md)에서 모두 지원됩니다.
* 모든 백업 유형 (전체/차등/로그) 및 복구 모델 (단순/전체/대량 로그)이 지원 됩니다.
* 전체 및 복사 전용 전체 백업 유형은 **읽기 전용** 데이터베이스에 대해 지원 됩니다.
* SQL 네이티브 압축은 백업 정책에서 사용자가 명시적으로 사용 하도록 설정한 경우에만 지원 됩니다. Azure Backup은 사용자가 설정한 대로이 컨트롤의 값에 따라 압축/NO_COMPRESSION 절을 사용 하 여 인스턴스 수준 기본값을 재정의 합니다.
* TDE 사용 데이터베이스 백업이 지원 됩니다. TDE로 암호화 된 데이터베이스를 다른 SQL Server 복원 하려면 먼저 [대상 서버에 인증서를 복원](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)해야 합니다. SQL Server 2016 이상 버전에 대 한 TDE 지원 데이터베이스의 백업 압축을 사용할 수 있지만 [여기](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593)에 설명 된 대로 낮은 전송 크기를 사용할 수 있습니다.
* 미러 데이터베이스와 데이터베이스 스냅샷에 대한 백업 및 복원 작업은 지원되지 않습니다.
* SQL Server **장애 조치 (Failover) 클러스터 인스턴스 (FCI)** 는 지원 되지 않습니다.
* 둘 이상의 백업 솔루션을 사용 하 여 독립 실행형 SQL Server 인스턴스 또는 SQL Always on 가용성 그룹을 백업 하면 백업 오류가 발생할 수 있습니다. 그렇게 하지 마십시오. 같은 솔루션 또는 다른 솔루션을 사용하여 한 가용성 그룹의 두 노드를 개별적으로 백업해도 오류가 발생할 수 있습니다.
* 가용성 그룹이 구성 되 면 몇 가지 요소에 따라 다른 노드에서 백업이 수행 됩니다. 아래는 가용성 그룹에 대한 백업 동작을 요약한 것입니다.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Always On 가용성 그룹을 사용하여 백업 동작

가용성 그룹 (AG)의 한 노드에서만 백업을 구성 하는 것이 좋습니다. 항상 주 노드와 동일한 지역에 백업을 구성 합니다. 즉, 백업을 구성 하는 지역에 항상 주 노드가 있어야 합니다. AG의 모든 노드가 백업이 구성 된 동일한 지역에 있는 경우 아무 문제가 없습니다.

#### <a name="for-cross-region-ag"></a>지역 간 AG

* 백업 기본 설정에 관계 없이 백업은 백업이 구성 된 동일한 지역에 있는 노드에서만 실행 됩니다. 영역 간 백업이 지원 되지 않기 때문입니다. 노드가 두 개만 있고 보조 노드가 다른 지역에 있는 경우 백업 기본 설정이 ' 보조 전용 '이 아닌 경우 백업은 주 노드에서 계속 실행 됩니다.
* 노드가 백업이 구성 된 지역과 다른 지역으로 장애 조치 (failover) 되 면 장애 조치 (failover) 된 지역에서 노드에 대 한 백업이 실패 합니다.

백업 기본 설정 및 백업 유형(전체/차등/로그/복사 전용 전체)에 따라 특정 노드(주/보조)에서 백업이 수행됩니다.

#### <a name="backup-preference-primary"></a>백업 기본 설정: 기본

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  기본
복사 전용 전체 |  기본

#### <a name="backup-preference-secondary-only"></a>백업 기본 설정: 보조만

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  보조
복사 전용 전체 |  보조

#### <a name="backup-preference-secondary"></a>백업 기본 설정: 보조

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  보조
복사 전용 전체 |  보조

#### <a name="no-backup-preference"></a>백업 기본 설정 없음

**백업 유형** | **Node**
--- | ---
전체 | 기본
차등 | 기본
로그 |  보조
복사 전용 전체 |  보조

## <a name="next-steps"></a>다음 단계

Azure VM에서 실행 되는 [SQL Server 데이터베이스를 백업](backup-azure-sql-database.md) 하는 방법에 대해 알아봅니다.