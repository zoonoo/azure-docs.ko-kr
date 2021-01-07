---
title: Compute 및 storage 옵션-Azure Database for MySQL 유연한 서버
description: 이 문서에서는 Azure Database for MySQL 유연한 서버에서 계산 및 저장소 옵션을 설명 합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 36f31ee390a6a208b202698ec9bda59b644c9e30
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534673"
---
# <a name="compute-and-storage-options-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL 유연한 서버에서 계산 및 저장소 옵션 (미리 보기)

> [!IMPORTANT] 
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

서로 다른 세 가지 계산 계층 중 하나에서 유연 하 게 사용할 수 있는 Azure Database for MySQL 서버를 만들 수 있습니다. 계산 계층은 B 시리즈, D 시리즈 및 E 시리즈를 사용 하는 기본 VM SKU로 구분 됩니다. 계산 계층과 크기를 선택 하면 서버에서 사용할 수 있는 메모리 및 vCores가 결정 됩니다. 모든 계산 계층에서 동일한 저장소 기술이 사용 됩니다. 모든 리소스는 MySQL 서버 수준에서 프로비전됩니다. 서버는 하나 이상의 데이터베이스를 갖출 수 있습니다.

| 리소스/계층 | **버스터 블** | **범용** | **메모리 액세스에 최적화** |
|:---|:----------|:--------------------|:---------------------|
| VM 시리즈| B 시리즈 | Ddsv4 시리즈 | Edsv4 시리즈|
| vCore 수 | 1, 2 | 2, 4, 8, 16, 32, 48, 64 | 2, 4, 8, 16, 32, 48, 64 |
| vCore 메모리 | 변수 | 4 GiB | 8 GiB * |
| 스토리지 크기 | 5 GiB ~ 16 TiB | 5 GiB ~ 16 TiB | 5 GiB ~ 16 TiB |
| 데이터베이스 백업 보존 기간 | 1 ~ 35 일 | 1 ~ 35 일 | 1 ~ 35 일 |

\* 504 GB의 메모리가 있는 E64ds_v4 (메모리 액세스에 최적화 된) SKU를 제외 하 고

계산 계층을 선택 하려면 다음 표를 시작 지점으로 사용 합니다.

| 컴퓨팅 계층 | 대상 워크로드 |
|:-------------|:-----------------|
| 버스터 블 | 전체 CPU가 지속적으로 필요 하지 않은 작업에 적합 합니다. |
| 범용 | 확장 가능한 I/O 처리량을 갖춘 부하 분산된 컴퓨팅 및 메모리가 필요한 대부분의 비즈니스 워크로드. 예를 들어 웹 및 모바일 앱을 호스트하는 서버와 기타 엔터프라이즈 애플리케이션이 있습니다.|
| 메모리 최적화 | 빠른 트랜잭션 처리와 높은 동시성을 위해 메모리 내 성능이 필요한 고성능 데이터베이스 워크로드. 예를 들어 실시간 데이터를 처리하는 서버 및 고성능 트랜잭션 또는 분석 앱이 있습니다.|

서버를 만든 후에는 계산 계층, 계산 크기 및 저장소 크기가 변경 됩니다. 계산 크기 조정에는 다시 시작이 필요 하 고 60-120 초 정도 걸리며, 저장소 크기 조정을 다시 시작 하지 않아도 됩니다. 또한 백업 보존 기간을 독립적으로 조정할 수 있습니다. 자세한 내용은 [리소스 크기 조정](#scale-resources) 섹션을 참조 하세요.

## <a name="compute-tiers-size-and-server-types"></a>Compute 계층, 크기 및 서버 유형

계층 및 크기에 따라 계산 리소스를 선택할 수 있습니다. VCores 및 메모리 크기를 결정 합니다. vCores는 기본 하드웨어의 논리적 CPU를 나타냅니다.

사용 가능한 서버 유형의 세부 사양은 다음과 같습니다.

| 컴퓨팅 크기         | vCore 수 | 메모리 크기 (GiB) | 
|----------------------|--------|-------------------|
| **버스터 블**        |        |                   | 
| Standard_B1s         | 1      | 1                 |  
| Standard_B1ms        | 1      | 2                 | 
| Standard_B2s         | 2      | 4                 |  
| **범용**  |        |                   | 
| Standard_D2ds_v4     | 2      | 8                 |  
| Standard_D4ds_v4     | 4      | 16                | 
| Standard_D8ds_v4     | 8      | 32                | 
| Standard_D16ds_v4    | 16     | 64                | 
| Standard_D32ds_v4    | 32     | 128               |  
| Standard_D48ds_v4    | 48     | 192               |  
| Standard_D64ds_v4    | 64     | 256               | 
| **메모리 액세스에 최적화** |        |                   |
| Standard_E2ds_v4     | 2      | 16                |
| Standard_E4ds_v4     | 4      | 32                |
| Standard_E8ds_v4     | 8      | 64                |
| Standard_E16ds_v4    | 16     | 128               |
| Standard_E32ds_v4    | 32     | 256               |
| Standard_E48ds_v4    | 48     | 384               |
| Standard_E64ds_v4    | 64     | 504               |

사용 가능한 계산 시리즈에 대 한 자세한 내용은 Azure VM 설명서 ( [B 시리즈)](../../virtual-machines/sizes-b-series-burstable.md), [범용 (Ddsv4 시리즈)](../../virtual-machines/ddv4-ddsv4-series.md)및 [메모리 최적화 (Edsv4 시리즈)](../../virtual-machines/edv4-edsv4-series.md)를 참조 하세요.

## <a name="storage"></a>Storage

프로 비전 하는 저장소는 유연한 서버에서 사용할 수 있는 저장소 용량의 양입니다. 저장소는 데이터베이스 파일, 임시 파일, 트랜잭션 로그 및 MySQL server 로그에 사용 됩니다. 모든 계산 계층에서 지원 되는 최소 저장소는 5 GiB이 고 최대값은 16 TiB입니다. 저장소는 1 GiB 증분 단위로 크기 조정 되며 서버를 만든 후에 확장할 수 있습니다.

>[!NOTE]
> 스토리지는 스케일 다운이 아닌 스케일 업만 가능합니다.

저장소 용량 한도, 저장소 비율 및 저장소 사용 메트릭을 사용 하 여 Azure Portal (Azure Monitor)에서 저장소 사용량을 모니터링할 수 있습니다. 메트릭에 대 한 자세한 내용은 [모니터링 문서](./concepts-monitoring.md) 를 참조 하세요. 

### <a name="reaching-the-storage-limit"></a>스토리지 제한에 도달

서버에서 사용 되는 저장소가 프로 비전 된 한도에 도달 하는 데 가까이 있으면 서버에서 읽기 전용 모드로 전환 하 여 서버에서 손실 되는 쓰기를 보호 합니다. 100 GiB 프로 비전 된 저장소 보다 작은 서버는 사용 가능한 저장소가 프로 비전 된 저장소 크기의 5% 미만인 경우 읽기 전용으로 표시 됩니다. 100 개 이상의 GiB 프로 비전 된 저장소가 있는 서버는 사용 가능한 저장소가 5 GiB 미만인 경우에만 읽기 전용으로 표시 됩니다.

예를 들어 110 GiB의 저장소를 프로 비전 하 고 실제 사용률이 105 GiB를 초과 하는 경우 서버는 읽기 전용으로 표시 됩니다. 또는 저장소 GiB 5 개를 프로 비전 한 경우 사용 가능한 저장소가 256 미만에 도달 하면 서버는 읽기 전용으로 표시 됩니다.

서비스가 서버를 읽기 전용으로 만들려고 하는 동안 모든 새 쓰기 트랜잭션 요청은 차단되고 기존 활성 트랜잭션은 계속 실행됩니다. 서버가 읽기 전용으로 설정되면 모든 후속 쓰기 작업 및 트랜잭션 커밋은 실패합니다. 읽기 쿼리는 중단 없이 계속 작동합니다. 

서버를 읽기 전용 모드로 설정 하려면 서버에서 프로 비전 된 저장소를 늘려야 합니다. Azure Portal 또는 Azure CLI를 사용 하 여이 작업을 수행할 수 있습니다. 서버를 늘리면 쓰기 트랜잭션을 다시 허용할 준비가 된 것입니다.

서버 스토리지가 임계값에 근접하는 경우 읽기 전용 상태로 들어가는 것을 방지할 수 있도록 알림 경고를 설정하는 것이 좋습니다. 사용 가능한 메트릭에 대 한 자세한 내용은 [모니터링 문서](./concepts-monitoring.md) 를 참조 하세요. 

이렇게 하는 것이 좋습니다. <!--turn on storage auto-grow or to--> 서버 저장소가 임계값에 도달 하는 경우 알리도록 경고를 설정 하 여 읽기 전용 상태가 되지 않도록 합니다. 자세한 내용은 경고를 [설정 하는 방법](how-to-alert-on-metric.md)에 대 한 설명서를 참조 하십시오.

### <a name="storage-auto-grow"></a>저장소 자동 증가

저장소 자동 증가는 Azure Database for MySQL 유연한 서버에서 아직 사용할 수 없습니다.

## <a name="iops"></a>IOPS
모든 계산 크기에서 최소 유효 IOPS는 100이 고, 최대 유효 IOPS는 다음 특성에 의해 결정 됩니다. 
- Compute: 최대 유효 IOPS는 선택한 계산 크기의 사용 가능한 최대 IOPS로 제한 될 수 있습니다.
- 저장소: 모든 계산 계층에서 프로 비전 된 저장소 크기를 3:1 비율로 하는 IOPS 규모입니다.

프로 비전 된 저장소를 늘리거나 더 큰 계산 크기로 이동 하 여 사용할 수 있는 유효한 IOPS의 크기를 조정할 수 있습니다 (IOPS가 계산에 의해 제한 되는 경우). 미리 보기에서 지원 되는 최대 유효 IOPS는 2만 IOPS입니다.

계산과 저장소의 조합을 사용 하 여 계산 크기별 최대 유효 IOPS에 대 한 자세한 내용은 아래에 나와 있습니다. 

| 컴퓨팅 크기         | 최대 유효 IOPS  | 
|----------------------|---------------------|
| **버스터 블**        |                     |
| Standard_B1s         | 320                 |
| Standard_B1ms        | 640                 |
| Standard_B2s         | 1280                | 
| **범용**  |                     |
| Standard_D2ds_v4     | 3200                |
| Standard_D4ds_v4     | 6400                |
| Standard_D8ds_v4     | 12800               |
| Standard_D16ds_v4    | 20000               |
| Standard_D32ds_v4    | 20000               |
| Standard_D48ds_v4    | 20000               | 
| Standard_D64ds_v4    | 20000               | 
| **메모리 최적화** |                     | 
| Standard_E2ds_v4     | 3200                | 
| Standard_E4ds_v4     | 6400                | 
| Standard_ E8ds_v4    | 12800               | 
| Standard_ E16ds_v4   | 20000               | 
| Standard_E32ds_v4    | 20000               | 
| Standard_E48ds_v4    | 20000               | 
| Standard_E64ds_v4    | 20000               |  

최대 유효 IOPS는 compute 크기 당 사용 가능한 최대 IOPS에 따라 달라 집니다. 아래의 수식을 참조 하 고 [B 시리즈](../../virtual-machines/sizes-b-series-burstable.md), [Ddsv4 시리즈](../../virtual-machines/ddv4-ddsv4-series.md)및 [Edsv4 시리즈](../../virtual-machines/edv4-edsv4-series.md) 설명서에서 *최대 캐시 되지 않은 디스크 처리량: IOPS/MBps* 를 참조 하세요.

**최대 유효 IOPS** = 최소 ( *"최대 캐시 되지 않은 디스크 처리량: IOPS/MBps"* 계산 크기, GiB * 3에 프로 비전 된 저장소)

[IO 백분율](./concepts-monitoring.md) 메트릭을 사용 하 여 Azure Portal (Azure Monitor)에서 i/o 사용량을 모니터링할 수 있습니다. 더 많은 IOPS가 필요한 경우 계산 크기나 프로 비전 된 저장소로 제한 되는지 이해 해야 합니다. 적절 하 게 프로 비전 된 서버의 계산 또는 저장소 크기를 조정 합니다.

## <a name="backup"></a>Backup

서비스에서 서버 백업을 자동으로 수행합니다. 1 ~ 35 일 범위에서 보존 기간을 선택할 수 있습니다. [백업 및 복원 개념 문서](concepts-backup-restore.md)에서 백업에 대해 자세히 알아보세요.

## <a name="scale-resources"></a>리소스 스케일링

서버를 만든 후에는 계산 계층, 계산 크기 (vCores 및 메모리), 저장소 크기 및 백업 보존 기간을 독립적으로 변경할 수 있습니다. 계산 크기를 확장 하거나 축소할 수 있습니다. 백업 보존 기간은 1 일에서 35 일로 확장 하거나 축소할 수 있습니다. 스토리지 크기는 늘릴 수 있습니다. 리소스의 크기 조정은 포털 또는 Azure CLI을 통해 수행할 수 있습니다.

> [!NOTE]
> 스토리지 크기는 늘릴 수 있습니다. 증가 한 후에는 더 작은 저장소 크기로 다시 이동할 수 없습니다.

계산 계층 또는 계산 크기를 변경 하면 새 서버 유형을 적용 하기 위해 서버가 다시 시작 됩니다. 시스템이 새 서버로 전환되면 잠시 동안 새 연결을 설정할 수 없으며, 커밋되지 않은 모든 트랜잭션이 롤백됩니다. 이 창은 다르지만 대부분의 경우는 60-120 초 사이입니다. 

저장소 크기 조정 및 백업 보존 기간 변경은 온라인 작업 이며 서버를 다시 시작 하지 않아도 됩니다.

## <a name="pricing"></a>가격 책정

최신 가격 책정 정보는 서비스 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/MySQL/)를 참조하세요. 원하는 구성에 대 한 비용을 확인 하기 위해 [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer/flexibleServers) 는 선택한 옵션에 따라 **계산 + 저장소** 탭의 월별 비용을 보여 줍니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상 가격을 구할 수 있습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트에서 **항목 추가** 를 선택 하 고, **데이터베이스** 범주를 확장 하 고, **Azure Database for MySQL** , **유연한 서버** 를 배포 유형으로 선택 하 여 옵션을 사용자 지정 합니다.

서버 비용을 최적화 하려면 다음 팁을 고려 하세요.

- 계산이 미달 사용 되는 경우 계산 계층 또는 계산 크기 (vCores)를 축소 합니다.
- 워크 로드에 범용 및 메모리 액세스에 최적화 된 계층의 전체 계산 용량이 지속적으로 필요 하지 않은 경우에는 분산이 안정적인 계산 계층으로 전환 하는 것이 좋습니다.
- 사용 하지 않을 때 서버를 중지 합니다.
- 백업 보존 기간을 더 길게 백업 하지 않아도 되는 경우 백업 보존 기간을 줄입니다.

## <a name="next-steps"></a>다음 단계

- [포털에서 MySQL 서버를 만드는 방법](quickstart-create-server-portal.md)을 알아봅니다.
- [서비스 제한 사항](concepts-limitations.md)에 대해 알아봅니다.