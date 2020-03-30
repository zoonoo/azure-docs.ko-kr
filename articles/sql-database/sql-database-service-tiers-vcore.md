---
title: vCore 모델 개요
description: vCore 구매 모델을 사용하면 컴퓨팅 및 스토리지 리소스를 독립적으로 확장하고 온-프레미스 성능과 일치하며 가격을 최적화할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481085"
---
# <a name="vcore-model-overview"></a>vCore 모델 개요

가상 코어(vCore) 모델은 다음과 같은 몇 가지 이점을 제공합니다.

- 높은 컴퓨팅, 메모리, IO 및 스토리지 제한.
- 워크로드의 컴퓨팅 및 메모리 요구 사항을 더 잘 일치시키기 위해 하드웨어 생성을 제어합니다.
- Azure 하이브리드 [혜택(AHB)](sql-database-azure-hybrid-benefit.md) 및 [예약 인스턴스(RI)에](sql-database-reserved-capacity.md)대한 가격 할인.
- 컴퓨팅을 강화하는 하드웨어 세부 사항의 투명성 향상; 온-프레미스 배포에서 마이그레이션을 계획할 수 있습니다.

## <a name="service-tiers"></a>서비스 계층

vCore 모델의 서비스 계층 옵션에는 범용, 비즈니스 중요 및 하이퍼스케일이 포함됩니다. 서비스 계층은 일반적으로 가용성 및 재해 복구와 관련된 저장소 아키텍처, 공간 및 IO 제한 및 비즈니스 연속성 옵션을 정의합니다.

||**범용**|**중요 비즈니스용**|**하이퍼스케일**|
|---|---|---|---|
|적합한 대상|대부분의 비즈니스 워크로드. 예산 지향, 균형 잡힌 확장 가능한 컴퓨팅 및 스토리지 옵션을 제공합니다. |여러 격리된 복제본을 사용하여 비즈니스 응용 프로그램에 가장 높은 복원력을 제공하고 데이터베이스 복제본당 최고 I/O 성능을 제공합니다.|확장성이 뛰어난 스토리지 및 읽기 규모 요구 사항이 있는 대부분의 비즈니스 워크로드.  두 개 이상의 격리된 데이터베이스 복제본을 구성할 수 있으므로 오류에 대한 복원력이 향상됩니다. |
|스토리지|원격 저장소를 사용합니다.<br/>**단일 데이터베이스 및 탄력적 풀 프로비전 컴퓨팅:**<br/>5GB~4TB<br/>**서버리스 컴퓨팅**:<br/>5GB - 3TB<br/>**관리인스턴스**: 32GB - 8TB |로컬 SSD 스토리지를 사용합니다.<br/>**단일 데이터베이스 및 탄력적 풀 프로비전 컴퓨팅:**<br/>5GB~4TB<br/>**관리인스턴스:**<br/>32GB~4TB |필요에 따라 스토리지의 유연한 자동 성장. 최대 100TB의 스토리지를 지원합니다. 로컬 버퍼 풀 캐시 및 로컬 데이터 저장소에 로컬 SSD 저장소를 사용합니다. Azure 원격 저장소를 최종 장기 데이터 저장소로 사용합니다. |
|IOPS 및 처리량(대략적인)|**단일 데이터베이스 및 탄력적 풀**: [단일 데이터베이스](../sql-database/sql-database-vcore-resource-limits-single-databases.md) 및 탄력적 풀에 대한 리소스 제한을 [확인합니다.](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)<br/>**관리되는 인스턴스**: [Azure SQL Database 관리 인스턴스 리소스 제한 개요](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)참조.|[단일 데이터베이스](../sql-database/sql-database-vcore-resource-limits-single-databases.md) 및 [탄력적 풀에](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)대한 리소스 제한을 참조하십시오.|하이퍼스케일은 여러 수준에서 캐싱이 있는 다중 계층 아키텍처입니다. 효과적인 IOPS 및 처리량은 워크로드에 따라 달라집니다.|
|가용성|복제본 1개, 읽기 크기 복제본 없음|3개 복제본, 1개 [읽기 크기 조정 복제본](sql-database-read-scale-out.md),<br/>영역 중복 고가용성(HA)|읽기-쓰기 복제본 1개 및 0-4읽기 [크기 복제본](sql-database-read-scale-out.md)|
|Backup|[읽기 액세스 지리적 중복 저장소(RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md)- 7-35일(기본적으로 7일)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35일(기본값: 7일)|Azure 원격 저장소의 스냅숏 기반 백업입니다. 복원은 빠른 복구를 위해 이러한 스냅샷을 사용합니다. 백업은 즉각적이며 컴퓨팅 I/O 성능에 영향을 미치지 않습니다. 복원은 빠르며 데이터 크기 가 아닌 작업(몇 시간 또는 며칠이 아닌 몇 분 소요)이 아닙니다.|
|메모리 내|지원되지 않음|지원됨|지원되지 않음|
|||


### <a name="choosing-a-service-tier"></a>서비스 계층 선택

특정 워크로드에 대한 서비스 계층 선택에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [범용 서비스 계층을 선택하는 경우](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [비즈니스 크리티컬 서비스 계층을 선택하는 시기](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [하이퍼스케일 서비스 계층을 선택하는 경우](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>계층 계산

vCore 모델의 계산 계층 옵션에는 프로비저닝된 계산 계층과 서버리스 계산 계층이 포함됩니다.


### <a name="provisioned-compute"></a>프로비저닝된 컴퓨팅

프로비저닝된 계산 계층은 워크로드 활동과 독립적으로 지속적으로 프로비저닝되는 특정 양의 계산 리소스와 시간당 고정 된 가격으로 프로비저닝된 계산 양에 대한 청구서를 제공합니다.


### <a name="serverless-compute"></a>서버리스 컴퓨팅

[서버리스 컴퓨팅 계층은](sql-database-serverless.md) 워크로드 활동을 기반으로 리소스를 자동 확장하고 초당 사용되는 계산 양에 대한 요금을 청구합니다.



## <a name="hardware-generations"></a>하드웨어 세대

vCore 모델의 하드웨어 생성 옵션에는 Gen 4/5, M 시리즈(미리 보기) 및 Fsv2 시리즈(미리 보기)가 포함됩니다. 하드웨어 생성은 일반적으로 계산 및 메모리 제한 및 워크로드의 성능에 영향을 주는 기타 특성을 정의합니다.

### <a name="gen4gen5"></a>4세대/5세대

- Gen4/Gen5 하드웨어는 균형 잡힌 컴퓨팅 및 메모리 리소스를 제공하며 Fsv2 시리즈 또는 M 시리즈에서 제공하는 높은 메모리, 높은 vCore 또는 더 빠른 단일 vCore 요구 사항이 없는 대부분의 데이터베이스 워크로드에 적합합니다.

Gen4/Gen5를 사용할 수 있는 지역은 [Gen4/Gen5 가용성을](#gen4gen5-1)참조하십시오.

### <a name="fsv2-seriespreview"></a>Fsv2 시리즈 (미리보기)

- Fsv2 시리즈는 CPU가 가장 까다로운 워크로드에 대해 낮은 CPU 대기 시간과 높은 클럭 속도를 제공하는 컴퓨팅 에 최적화된 하드웨어 옵션입니다.
- 워크로드에 따라 Fsv2 시리즈는 Gen5보다 vCore당 더 많은 CPU 성능을 제공할 수 있으며, 72 vCore 크기는 Gen5의 80vCore보다 저렴한 비용으로 더 많은 CPU 성능을 제공할 수 있습니다. 
- Fsv2는 vCore당 메모리와 tempdb를 다른 하드웨어보다 적게 제공하므로 이러한 제한에 민감한 워크로드는 Gen5 또는 M-시리즈를 대신 고려할 수 있습니다.  

범용 계층에서만 지원되는 Fsv2 시리즈입니다.  Fsv2 시리즈를 사용할 수 있는 지역은 [Fsv2 시리즈 가용성을](#fsv2-series)참조하십시오.


### <a name="m-seriespreview"></a>M 시리즈(미리 보기)

- M 시리즈는 Gen5에서 제공하는 것보다 더 많은 메모리와 더 높은 컴퓨팅 제한을 요구하는 워크로드에 최적화된 메모리 최적화 하드웨어 옵션입니다.
- M 시리즈는 vCore당 29GB와 128vCore를 제공하므로 Gen5에 비해 메모리 제한이 8배에서 거의 4TB로 증가합니다.

M 시리즈는 비즈니스 크리티컬 계층에서만 지원되며 영역 중복성을 지원하지 않습니다.

구독 및 리전에 대해 M 시리즈 하드웨어를 사용하려면 지원 요청을 열어야 합니다. 구독은 종량제 또는 EA(기업 계약)를 포함한 유료 제안 유형이어야 합니다.  지원 요청이 승인되면 M 시리즈의 선택 및 프로비저닝 환경은 다른 하드웨어 세대와 동일한 패턴을 따릅니다. M 시리즈를 사용할 수 있는 지역은 [M 시리즈 가용성을](#m-series)참조하십시오.


### <a name="compute-and-memory-specifications"></a>컴퓨팅 및 메모리 사양


|하드웨어 세대  |컴퓨팅  |메모리  |
|:---------|:---------|:---------|
|Gen4     |- 인텔 E5-2673 v3 (하스웰) 2.4 GHz 프로세서<br>- 최대 24개의 vCore 프로비저닝(1 vCore = 1 물리적 코어)  |- vCore당 7GB<br>- 최대 168GB 프로비저닝|
|5세대     |**프로비저닝된 컴퓨팅**<br>- 인텔 E5-2673 v4 (브로드 웰) 2.3 GHz와 인텔 SP-8160 (스카이 레이크)* 프로세서<br>- 최대 80vCore프로비전(1vCore = 1 하이퍼 스레드)<br><br>**서버리스 컴퓨팅**<br>- 인텔 E5-2673 v4 (브로드 웰) 2.3 GHz와 인텔 SP-8160 (스카이 레이크)* 프로세서<br>- 최대 16개의 vCore(1vCore = 1하이퍼 스레드)까지 자동 확장|**프로비저닝된 컴퓨팅**<br>- vCore당 5.1GB<br>- 최대 408GB 프로비저닝<br><br>**서버리스 컴퓨팅**<br>- vCore당 최대 24GB까지 자동 확장<br>- 최대 48GB까지 자동 확장|
|Fsv2 시리즈     |- 인텔 제온 플래티넘 8168 (스카이 레이크) 프로세서<br>- 3.4GHz의 모든 코어 터보 클럭 속도와 3.7GHz의 최대 단일 코어 터보 클럭 속도를 특징으로 합니다.<br>- 프로비저닝 72 vCore (1 vCore = 1 하이퍼 스레드)|- vCore당 1.9GB<br>- 조항 136 GB|
|M 시리즈     |- 인텔 제온 E7-8890 v3 2.5 GHz 프로세서<br>- 프로비저닝 128 vCore (1 vCore = 1 하이퍼 스레드)|- vCore당 29GB<br>- 조항 3.7 TB|

\*[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 동적 관리 보기에서 인텔 SP-8160(스카이레이크) 프로세서를 사용하는 Gen5 데이터베이스의 하드웨어 생성은 Gen6으로 나타납니다. 모든 Gen5 데이터베이스에 대한 리소스 제한은 프로세서 유형(Broadwell 또는 Skylake)에 관계없이 동일합니다.

리소스 제한에 대한 자세한 내용은 [단일 데이터베이스(vCore)에 대한 리소스 제한](sql-database-vcore-resource-limits-single-databases.md)또는 [탄력적 풀(vCore)에 대한 리소스 제한을](sql-database-vcore-resource-limits-elastic-pools.md)참조하십시오.

### <a name="selecting-a-hardware-generation"></a>하드웨어 생성 선택

Azure 포털에서 생성 시 SQL 데이터베이스 또는 풀에 대한 하드웨어 생성을 선택하거나 기존 SQL 데이터베이스 또는 풀의 하드웨어 생성을 변경할 수 있습니다.

**SQL 데이터베이스 또는 풀을 만들 때 하드웨어 생성을 선택하려면**

자세한 내용은 [SQL 데이터베이스 만들기를](sql-database-single-database-get-started.md)참조하십시오.

**기본** 탭에서 **계산 + 저장소** 섹션에서 데이터베이스 **구성** 링크를 선택한 다음 **구성** 변경 링크를 선택합니다.

  ![데이터베이스 구성](media/sql-database-service-tiers-vcore/configure-sql-database.png)

원하는 하드웨어 생성을 선택합니다.

  ![하드웨어 선택](media/sql-database-service-tiers-vcore/select-hardware.png)


**기존 SQL 데이터베이스 또는 풀의 하드웨어 생성을 변경하려면**

데이터베이스의 경우 개요 페이지에서 **가격 책정 계층** 링크를 선택합니다.

  ![하드웨어 변경](media/sql-database-service-tiers-vcore/change-hardware.png)

풀의 경우 개요 페이지에서 **구성을**선택합니다.

구성을 변경하는 단계를 수행하고 이전 단계에 설명된 대로 하드웨어 생성을 선택합니다.

**관리되는 인스턴스를 만들 때 하드웨어 생성을 선택하려면**

자세한 내용은 [관리되는 인스턴스 만들기를](sql-database-managed-instance-get-started.md)참조하십시오.

**기본** 탭에서 **Compute + 저장소** 섹션에서 **데이터베이스 구성** 링크를 선택한 다음 원하는 하드웨어 생성을 선택합니다.

  ![관리되는 인스턴스 구성](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**기존 관리형 인스턴스의 하드웨어 생성을 변경하려면**

# <a name="portal"></a>[포털](#tab/azure-portal)

관리되는 인스턴스 페이지에서 설정 섹션 아래에 배치된 **가격 책정 계층** 링크를 선택합니다.

![관리되는 인스턴스 하드웨어 변경](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

가격 **책정 계층** 페이지에서 이전 단계에 설명된 대로 하드웨어 생성을 변경할 수 있습니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

다음 PowerShell 스크립트를 사용합니다.

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

자세한 내용은 [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) 명령을 확인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 사용합니다.

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

자세한 내용은 [az sql mi 업데이트](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) 명령을 확인하십시오.

---

### <a name="hardware-availability"></a>고가용성

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>4세대/5세대

Gen4 하드웨어는 [단계적으로 폐지되고](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) 있으며 새 배포에 더 이상 사용할 수 없습니다. 모든 새 데이터베이스는 Gen5 하드웨어에 배포해야 합니다.

Gen5는 전 세계 대부분의 지역에서 사용할 수 있습니다.

#### <a name="fsv2-series"></a>Fsv2 시리즈

Fsv2 시리즈는 호주 중부, 오스트레일리아 중부 2, 오스트레일리아 동부, 호주 남동부, 브라질 남부, 캐나다 중부, 동아시아, 동미국, 프랑스 중부, 인도 중부, 인도 서부, 한국 중부, 한국 남부, 북부 지역에서 사용할 수 있습니다. 유럽, 남아프리카 공화국 북부, 동남 아시아, 영국 남부, 영국 서부, 서유럽, 서부 미국 2.


#### <a name="m-series"></a>M 시리즈

M 시리즈는 미국 동부, 북유럽, 서유럽, 미국 서부 2 지역에서 사용할 수 있습니다.
M 시리즈는 추가 리전에서도 가용성이 제한될 수 있습니다. 여기에 나열된 지역과 다른 지역을 요청할 수 있지만 다른 리전에서의 이행이 불가능할 수 있습니다.

구독에서 M-시리즈 가용성을 활성화하려면 [새 지원 요청을 제출하여](#create-a-support-request-to-enable-m-series)액세스를 요청해야 합니다.


##### <a name="create-a-support-request-to-enable-m-series"></a>M-시리즈를 사용하도록 지원 요청을 만듭니다. 

1. 포털에서 **도움말 + 지원을** 선택합니다.
2. **새 지원 요청**을 선택합니다.

기본 페이지에서 다음을 **제공합니다.**

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.
2. **구독의** 경우 = M 시리즈를 사용하도록 설정하려면 구독을 선택합니다.
3. **할당량 유형의**경우 **SQL 데이터베이스를**선택합니다.
4. **세부 정보** 페이지로 이동하려면 **다음을** 선택합니다.

세부 **정보** 페이지에서 다음을 제공합니다.

1. 문제 **세부 정보** 섹션에서 **세부 정보 제공** 링크를 선택합니다. 
2. **SQL 데이터베이스 할당량 유형의** 경우 M 시리즈를 **선택합니다.**
3. **영역의**경우 M 시리즈를 사용할 영역을 선택합니다.
    M 시리즈를 사용할 수 있는 지역은 [M 시리즈 가용성을](#m-series)참조하십시오.

승인된 지원 요청은 일반적으로 영업일 5일 이내에 이행됩니다.


## <a name="next-steps"></a>다음 단계

- SQL 데이터베이스를 만들려면 [Azure 포털을 사용하여 SQL 데이터베이스 만들기를](sql-database-single-database-get-started.md)참조하십시오.
- 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 사항은 [단일 데이터베이스에 대한 SQL Database vCore 기반 리소스 제한을](sql-database-vcore-resource-limits-single-databases.md)참조하십시오.
- 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 사항은 [탄력적 풀에 대한 SQL Database vCore 기반 리소스 제한을](sql-database-vcore-resource-limits-elastic-pools.md)참조하십시오.
- 가격 에 대한 자세한 내용은 [Azure SQL Database 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/sql-database/single/)참조하십시오.
