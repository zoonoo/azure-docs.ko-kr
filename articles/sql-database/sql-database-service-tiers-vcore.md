---
title: vCore 모델 개요
description: VCore 구매 모델을 통해 계산 및 저장소 리소스를 독립적으로 확장 하 고, 온-프레미스 성능과 일치 시키고, 가격을 최적화할 수 있습니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 78e01c854201e3c5253cd86aebcd85b62bf5568d
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629303"
---
# <a name="vcore-model-overview"></a>vCore 모델 개요

VCore (가상 코어) 모델은 다음과 같은 여러 가지 이점을 제공 합니다.

- 더 높은 계산, 메모리, IO 및 저장소 제한이 있습니다.
- 워크 로드에 대 한 계산 및 메모리 요구 사항을 더 잘 맞추려면 하드웨어 생성을 제어 합니다.
- [Azure 하이브리드 혜택 (AHB)](sql-database-azure-hybrid-benefit.md) 및 [예약 인스턴스 (RI)](sql-database-reserved-capacity.md)의 가격 할인.
- 계산을 지 원하는 하드웨어 세부 정보의 투명도 향상 온-프레미스 배포에서의 마이그레이션 계획을 용이 하 게 합니다.

## <a name="service-tiers"></a>서비스 계층

VCore 모델의 서비스 계층 옵션에는 범용, 중요 비즈니스용 및 Hyperscale이 포함 됩니다. 서비스 계층은 일반적으로 가용성 및 재해 복구와 관련 된 저장소 아키텍처, 공간 및 IO 제한 및 비즈니스 연속성 옵션을 정의 합니다.

||**범용**|**업무상 중요**|**하이퍼스케일**|
|---|---|---|---|
|적합한 대상|대부분의 비즈니스 워크로드. 는 예산 지향적이 고 균형이 조정 되며 확장 가능한 계산 및 저장소 옵션을 제공 합니다. |는 여러 개의 격리 된 복제본을 사용 하 여 비즈니스 응용 프로그램에서 오류에 대 한 가장 높은 복원 력을 제공 하 고, 데이터베이스 복제본 별로 최고 i/o 성능을 제공 합니다.|확장성이 뛰어난 저장소 및 읽기 확장 요구 사항에 대 한 대부분의 비즈니스 워크 로드.  에서는 둘 이상의 격리 된 데이터베이스 복제본의 구성을 허용 하 여 오류에 대 한 더 높은 복원 력을 제공 합니다. |
|스토리지|원격 저장소를 사용 합니다.<br/>**단일 데이터베이스 및 탄력적 풀 프로 비전 된 계산**:<br/>5GB~4TB<br/>**서버**를 사용 하지 않는 계산:<br/>5GB-3TB<br/>**Managed Instance**: 32 g b-8tb |로컬 SSD 저장소를 사용 합니다.<br/>**단일 데이터베이스 및 탄력적 풀 프로 비전 된 계산**:<br/>5GB~4TB<br/>**Managed Instance**:<br/>32GB~4TB |필요에 따라 저장소를 유연 하 게 자동 증가 는 최대 100 TB의 저장소를 지원 합니다. 로컬 버퍼 풀 캐시 및 로컬 데이터 저장소에 로컬 SSD 저장소를 사용 합니다. Azure 원격 저장소를 최종 장기 데이터 저장소로 사용 합니다. |
|IOPS 및 처리량 (근사치)|**단일 데이터베이스 및 탄력적 풀**: [단일 데이터베이스](../sql-database/sql-database-vcore-resource-limits-single-databases.md) 및 [탄력적 풀](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)에 대 한 리소스 제한을 참조 하세요.<br/>**Managed Instance**: [관리 되는 인스턴스 리소스 제한 Azure SQL Database 개요](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics)를 참조 하세요.|[단일 데이터베이스](../sql-database/sql-database-vcore-resource-limits-single-databases.md) 및 [탄력적 풀](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)에 대 한 리소스 제한을 참조 하세요.|Hyperscale은 여러 수준에서 캐싱을 사용 하는 다중 계층 아키텍처입니다. 효과적인 IOPS 및 처리량은 워크 로드에 따라 달라 집니다.|
|가용성|복제본 1 개, 읽기 확장 복제본 없음|3개 복제본, 1개 [읽기 크기 조정 복제본](sql-database-read-scale-out.md),<br/>영역 중복 HA (고가용성)|1 읽기/쓰기 복제본 및 0-4 [읽기 확장 복제본](sql-database-read-scale-out.md)|
|Backup|[읽기 액세스 지역 중복 저장소 (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 일 (기본적으로 7 일)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35일(기본값: 7일)|Azure 원격 저장소의 스냅숏 기반 백업 복원은 빠른 복구를 위해 이러한 스냅샷을 사용합니다. 백업은 즉시 수행 되며 계산 i/o 성능에 영향을 주지 않습니다. 복원은 빠르게 수행 하 고 데이터의 크기를 조정 하는 작업이 아닙니다 (몇 시간 또는 몇 일이 아닌 분 소요).|
|메모리 내|지원되지 않음|지원됨|지원되지 않음|
|||


### <a name="choosing-a-service-tier"></a>서비스 계층 선택

특정 워크 로드에 대 한 서비스 계층을 선택 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [범용 서비스 계층을 선택 하는 경우](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [중요 비즈니스용 서비스 계층을 선택 하는 경우](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Hyperscale 서비스 계층을 선택 하는 경우](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Compute 계층

VCore 모델의 compute 계층 옵션에는 프로 비전 및 서버를 사용 하지 않는 계산 계층이 포함 됩니다.


### <a name="provisioned-compute"></a>프로비저닝된 컴퓨팅

프로 비전 된 계산 계층은 작업 활동에 독립적으로 지속적으로 프로 비전 되는 특정 양의 계산 리소스를 제공 하 고 시간당 고정 된 가격으로 프로 비전 된 계산의 양에 대 한 요금을 제공 합니다.


### <a name="serverless-compute"></a>서버리스 컴퓨팅

서버를 사용 하지 않는 [계산 계층](sql-database-serverless.md) 은 작업 활동에 따라 계산 리소스를 자동으로 조정 하 고, 초당 사용 된 계산 양에 대 한 요금을 청구 합니다.



## <a name="hardware-generations"></a>하드웨어 생성

VCore 모델의 하드웨어 생성 옵션에는 Gen 4/5, M 시리즈 (미리 보기) 및 Fsv2 시리즈 (미리 보기)가 포함 됩니다. 하드웨어 생성은 일반적으로 계산 및 메모리 제한과 작업의 성능에 영향을 주는 기타 특성을 정의 합니다.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5 하드웨어는 균형 잡힌 계산 및 메모리 리소스를 제공 하며, Fsv2 시리즈 또는 M 시리즈에서 제공 되는 것과 같은 더 높은 메모리, 더 높은 vCore 또는 더 빠른 단일 vCore 요구 사항이 없는 대부분의 데이터베이스 작업에 적합 합니다.

Gen4/Gen5을 사용할 수 있는 지역의 경우 [Gen4/Gen5 availability](#gen4gen5-1)를 참조 하세요.

### <a name="fsv2-seriespreview"></a>Fsv2 시리즈 (미리 보기)

- Fsv2 시리즈는 cpu 대기 시간이 낮고 CPU가 까다로운 워크 로드에 대해 높은 클록 속도를 제공 하는 계산에 최적화 된 하드웨어 옵션입니다.
- 워크 로드에 따라 Fsv2 시리즈는 Gen5 보다 vCore 당 더 많은 CPU 성능을 제공할 수 있으며, 72 vCore 크기는 Gen5에서 80 Vcore 보다 적은 비용으로 더 많은 CPU 성능을 제공할 수 있습니다. 
- Fsv2는 다른 하드웨어 보다 vCore의 메모리와 tempdb를 더 작게 제공 하므로 이러한 한도에 영향을 주는 워크 로드는 Gen5 또는 M 시리즈를 대신 고려 하는 것이 좋습니다.  

Fsv2 시리즈는 일반적인 용도의 계층 에서만 지원 됩니다.  Fsv2 시리즈를 사용할 수 있는 지역의 경우 [Fsv2 시리즈 가용성](#fsv2-series)을 참조 하세요.


### <a name="m-seriespreview"></a>M 시리즈 (미리 보기)

- M 시리즈는 Gen5에서 제공 하는 것 보다 더 많은 메모리를 필요로 하는 워크 로드에 대 한 메모리 액세스에 최적화 된 하드웨어 옵션입니다.
- M 시리즈는 vCore 및 128 Vcore 당 30GB를 제공 하 여 Gen5 by 8x를 기준으로 약 4 TB까지 메모리 제한을 늘립니다.

M 시리즈는 중요 비즈니스용 계층 에서만 지원 되며 영역 중복성은 지원 하지 않습니다.

구독 및 지역에 M 시리즈 하드웨어를 사용 하도록 설정 하려면 지원 요청을 열어야 합니다. 구독은 종 량 제 또는 기업계약 (EA)를 포함 한 유료 제안 유형 이어야 합니다.  지원 요청이 승인 되 면 M 시리즈의 선택 및 프로 비전 환경은 다른 하드웨어 세대와 동일한 패턴을 따릅니다. M 시리즈를 사용할 수 있는 지역에 대해서는 [m 시리즈 가용성](#m-series)을 참조 하세요.


### <a name="compute-and-memory-specifications"></a>계산 및 메모리 사양


|하드웨어 세대  |컴퓨팅  |메모리  |
|:---------|:---------|:---------|
|Gen4     |-Intel E5-2673 v3 (Haswell) 2.4 GHz 프로세서<br>-최대 24 개의 vCores 프로 비전 (1 개 Vcores = 1 실제 코어)  |-vCore 당 7GB<br>-최대 168 GB 프로 비전|
|5세대     |**프로비저닝된 컴퓨팅**<br>-Intel E5-2673 v4 (Broadwell) 2.3 GHz 및 Intel SP-8160 (Skylake) * 프로세서<br>-최대 80 Vcores 프로 비전 (1 개 Vcores = 1 개 하이퍼 스레드)<br><br>**서버리스 컴퓨팅**<br>-Intel E5-2673 v4 (Broadwell) 2.3 GHz 및 Intel SP-8160 (Skylake) * 프로세서<br>-최대 16 개의 Vcores 자동 확장 (1 개 Vcores = 1 개 하이퍼 스레드)|**프로비저닝된 컴퓨팅**<br>-vCore 당 5.1 GB<br>-최대 408 GB 프로 비전<br><br>**서버리스 컴퓨팅**<br>-VCore 당 최대 24gb까지 자동 확장<br>-최대 48 GB까지 자동 확장|
|Fsv2 시리즈     |-Intel Xeon Platinum 8168 (SkyLake) 프로세서<br>-3.4 GHz의 모든 코어 터보 클록 속도와 3.7 g h z의 싱글 코어 터보 클록 속도를 모두 유지 합니다.<br>-72 Vcores 프로 비전 (1 개 Vcores = 1 개 하이퍼 스레드)|-vCore 당 1.9 GB<br>-136 GB 프로 비전|
|M 시리즈     |-Intel Xeon E7-8890 v3 2.5 g h z 및 Intel Xeon Platinum 8280M 2.7 GHz (캐스케이드 Lake) 프로세서<br>-128 Vcores 프로 비전 (1 개 Vcores = 1 개 하이퍼 스레드)|-vCore 당 30GB<br>-3.7 TB 프로 비전|

\*Gen5 동적 관리 뷰에서 Intel SP-8160 (Skylake) 프로세서를 사용 하는 데이터베이스용 하드웨어 생성 [dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 은 Gen6로 표시 됩니다. 모든 Gen5 데이터베이스에 대 한 리소스 제한은 프로세서 유형 (Broadwell 또는 Skylake)에 관계 없이 동일 합니다.

리소스 제한에 대 한 자세한 내용은 [단일 데이터베이스에 대 한 리소스 제한 (vcore)](sql-database-vcore-resource-limits-single-databases.md)또는 [탄력적 풀에 대 한 리소스 제한 (vcore)](sql-database-vcore-resource-limits-elastic-pools.md)을 참조 하세요.

### <a name="selecting-a-hardware-generation"></a>하드웨어 생성 선택

Azure Portal에서 만들 때 SQL database 또는 풀에 대 한 하드웨어 생성을 선택 하거나 기존 SQL 데이터베이스 또는 풀의 하드웨어 생성을 변경할 수 있습니다.

**SQL 데이터베이스 또는 풀을 만들 때 하드웨어 생성을 선택 하려면**

자세한 내용은 [SQL 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조 하세요.

**기본** 탭의 **Compute + storage** 섹션에서 **데이터베이스 구성** 링크를 선택 하 고 **구성 변경** 링크를 선택 합니다.

  ![데이터베이스 구성](media/sql-database-service-tiers-vcore/configure-sql-database.png)

원하는 하드웨어 생성을 선택 합니다.

  ![하드웨어 선택](media/sql-database-service-tiers-vcore/select-hardware.png)


**기존 SQL 데이터베이스 또는 풀의 하드웨어 생성을 변경 하려면**

데이터베이스의 경우 개요 페이지에서 **가격 책정 계층** 링크를 선택 합니다.

  ![하드웨어 변경](media/sql-database-service-tiers-vcore/change-hardware.png)

풀의 경우 개요 페이지에서 **구성**을 선택 합니다.

단계에 따라 구성을 변경 하 고 이전 단계에서 설명한 대로 하드웨어 생성을 선택 합니다.

**관리 되는 인스턴스를 만들 때 하드웨어 생성을 선택 하려면**

자세한 내용은 [관리 되는 인스턴스 만들기](sql-database-managed-instance-get-started.md)를 참조 하세요.

**기본** 탭의 **Compute + storage** 섹션에서 **데이터베이스 구성** 링크를 선택 하 고 원하는 하드웨어 생성을 선택 합니다.

  ![관리 되는 인스턴스 구성](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**기존 관리 되는 인스턴스의 하드웨어 생성을 변경 하려면**

# <a name="portal"></a>[포털](#tab/azure-portal)

관리 되는 인스턴스 페이지의 설정 섹션 아래에 있는 **가격 책정 계층** 링크를 선택 합니다.

![관리 되는 인스턴스 하드웨어 변경](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

**가격 책정 계층** 페이지에서 이전 단계에 설명 된 대로 하드웨어 생성을 변경할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 스크립트를 사용합니다.

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

자세한 내용은 [AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) 명령을 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 사용 합니다.

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

자세한 내용은 확인 [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) command를 참조 하십시오.

---

### <a name="hardware-availability"></a>고가용성

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

Gen4 하드웨어가 [단계적으로 진행](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) 되 고 있으며 새 배포에 대해 더 이상 사용할 수 없습니다. 모든 새 데이터베이스는 Gen5 하드웨어에 배포 되어야 합니다.

Gen5는 전 세계 대부분의 지역에서 사용할 수 있습니다.

#### <a name="fsv2-series"></a>Fsv2 시리즈

Fsv2 시리즈는 오스트레일리아 중부, 오스트레일리아 중부 2, 오스트레일리아 동부, 오스트레일리아 남동쪽, 브라질 남부, 캐나다 중부, 동아시아, 미국 동부, 프랑스 중부, 인도 중부, 인도 서 부, 대한민국 중부, 한국 남부, 북부 유럽, 남아프리카 공화국 북부, 동남 아시아, 영국 남부, 영국 서부, 유럽 서부, 미국 서 부 2 지역에서 사용할 수 있습니다.


#### <a name="m-series"></a>M 시리즈

M 시리즈는 미국 동부, 유럽 서 부, 유럽 서부, 미국 서 부 2 지역에서 사용할 수 있습니다.
M 시리즈는 추가 지역의 가용성이 제한 될 수도 있습니다. 여기에 나열 된 것과 다른 지역을 요청할 수 있지만 다른 지역에서의 처리는 불가능 합니다.

구독에서 M 시리즈 가용성을 사용 하도록 설정 하려면 [새 지원 요청](#create-a-support-request-to-enable-m-series)을 작성 하 여 액세스를 요청 해야 합니다.


##### <a name="create-a-support-request-to-enable-m-series"></a>M 시리즈를 사용 하도록 지원 요청을 만듭니다. 

1. 포털에서 **도움말 + 지원** 을 선택 합니다.
2. **새 지원 요청**을 선택 합니다.

**기본 사항** 페이지에서 다음을 제공 합니다.

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.
2. **Subscription** = M 시리즈를 사용 하도록 설정할 구독을 선택 합니다.
3. **할당량 유형**에서 **SQL database**를 선택 합니다.
4. **다음** 을 선택 하 여 **세부 정보** 페이지로 이동 합니다.

**세부 정보** 페이지에서 다음을 제공 합니다.

1. **문제 세부 정보** 섹션에서 **세부 정보 제공** 링크를 선택 합니다. 
2. **SQL Database 할당량 유형** **으로 M 시리즈**를 선택 합니다.
3. **지역**에서 M 시리즈를 사용할 지역을 선택 합니다.
    M 시리즈를 사용할 수 있는 지역에 대해서는 [m 시리즈 가용성](#m-series)을 참조 하세요.

승인 된 지원 요청은 보통 5 영업일 이내에 수행 됩니다.


## <a name="next-steps"></a>다음 단계

- SQL database를 만들려면 [Azure Portal를 사용 하 여 sql 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조 하세요.
- 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목은 [단일 데이터베이스에 대 한 vCore 기반 리소스 제한 SQL Database](sql-database-vcore-resource-limits-single-databases.md)을 참조 하세요.
- 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목에 대해서는 [탄력적 풀에 대 한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md)을 참조 하세요.
- 가격 책정에 대 한 자세한 내용은 [Azure SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)를 참조 하세요.
