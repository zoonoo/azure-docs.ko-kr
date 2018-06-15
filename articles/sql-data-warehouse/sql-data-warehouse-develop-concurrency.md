---
title: SQL Data Warehouse의 동시성 및 워크로드 관리 | Microsoft Docs
description: 솔루션 개발을 위한 Azure SQL Data Warehouse의 동시성 및 워크로드 관리를 이해합니다.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: ''
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
ms.locfileid: "29959509"
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>SQL Data Warehouse의 동시성 및 워크로드 관리
예측 가능한 성능을 광범위하게 제공하려는 경우 Microsoft Azure SQL Data Warehouse를 사용하면 동시성 수준 및 리소스 할당(예: 메모리, CPU 우선 순위 지정)을 제어할 수 있습니다. 이 문서에서는 동시성 및 워크로드 관리 개념을 소개하며, 두 기능 모두 구현된 방법 및 데이터 웨어하우스에서 이들을 제어할 수 있는 방법을 설명합니다. SQL Data Warehouse 워크로드 관리는 다중 사용자 환경을 지원합니다. 다중 테넌트 워크로드용은 아닙니다.

## <a name="concurrency-limits"></a>동시성 한도
SQL Data Warehouse를 사용하여 최대 1,024개의 동시 연결을 할 수 있습니다. 1,024개의 모든 연결을 통해 동시에 쿼리를 제출할 수 있습니다. 그러나 SQL Data Warehouse는 처리량을 최적화하기 위해 각 쿼리가 최소한의 메모리를 부여받도록 일부 쿼리를 대기시킬 수 있습니다. 쿼리 실행 시 큐에 대기하기가 발생합니다. SQL Data Warehouse는 동시성 한도에 이른 경우 쿼리를 큐에 대기하도록 하여 활성 쿼리가 긴급하게 필요한 메모리 리소스에 액세스할 수 있게 하여 총 처리량을 늘릴 수 있습니다.  

동시성 한도는 *동시 쿼리 수* 및 *동시성 슬롯 수*라는 두 개념에 의해 제어됩니다. 쿼리가 실행하려면 쿼리 동시성 한도 및 동시성 슬롯 할당 내에서 실행해야 합니다.

* 동시 쿼리 수란 동시에 실행하는 쿼리 수입니다. SQL Data Warehouse는 더 큰 DWU 크기에서 최대 32개의 동시 쿼리를 지원합니다.
* 동시성 슬롯은 DWU를 기준으로 할당됩니다. 100개의 DWU마다 4개의 동시성 슬롯을 제공합니다. 예를 들어 DW100은 4개의 동시성 슬롯을 할당하고, DW1000은 40개를 할당합니다. 각 쿼리는 쿼리의 [리소스 클래스](#resource-classes) 에 따라 하나 이상의 동시성 슬롯을 사용합니다. smallrc 리소스 클래스에서 실행되는 쿼리는 하나의 동시성 슬롯을 사용합니다. 상위 리소스 클래스에서 실행되는 쿼리는 더 많은 동시성 슬롯을 사용합니다.

다음 테이블에는 다양한 DWU 크기의 동시 쿼리와 동시성 슬롯의 한도가 나와 있습니다.

### <a name="concurrency-limits"></a>동시성 한도
| DWU | 최대 동시 쿼리 수 | 할당된 동시성 슬롯 수 |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

이러한 임계값 중 하나가 충족되면 새 쿼리가 큐에 추가되고 선입선출 방식으로 실행됩니다.  쿼리가 완료되고 쿼리 및 슬롯의 수가 한도 밑으로 떨어지면 큐에 저장된 쿼리가 릴리스됩니다. 

> [!NOTE]  
> *Select* 쿼리 또는 카탈로그 뷰는 어떠한 동시성 한도에 의해서도 제어되지 않습니다. 시스템에서 실행하는 쿼리 수에 관계없이 시스템을 모니터링할 수 있습니다.
> 
> 

## <a name="resource-classes"></a>리소스 클래스
리소스 클래스는 쿼리에 지정된 메모리 할당 및 CPU 사이클을 제어하는 데 도움을 줍니다. 2가지 유형의 리소스 클래스를 데이터베이스 역할 형태로 사용자에게 할당할 수 있습니다. 2가지 유형의 리소스 클래스는 다음과 같습니다.
1. 동적 리소스 클래스(**smallrc, mediumrc, largerc, xlargerc**)는 현재 DWU에 따라 가변 크기의 메모리를 할당합니다. 즉, 더 큰 DWU로 확장할 경우 쿼리도 자동으로 더 많은 메모리를 얻게 됩니다. 
2. 정적 리소스 클래스(**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**)는 현재 DWU에 관계없이 동일한 양의 메모리를 할당합니다(DWU 자체에 충분한 메모리가 있다고 가정할 경우). 즉, 더 큰 DWU에서는 각 리소스 클래스에서 동시에 더 많은 쿼리를 실행할 수 있습니다.

**smallrc** 및 **staticrc10**의 사용자에게는 더 적은 양의 메모리가 주어져 더 높은 동시성을 활용할 수 있습니다. 반면, **xlargerc** 또는 **staticrc80**에 할당된 사용자에게는 더 많은 양의 메모리가 주어져 동시에 실행할 수 있는 쿼리가 매우 적습니다.

기본적으로 각 사용자는 작은 리소스 클래스인 **smallrc**의 멤버입니다. `sp_addrolemember` 절차는 리소스 클래스를 늘리는 데 사용되고 `sp_droprolemember` 절차는 리소스 클래스를 줄이는 데 사용됩니다. 예를 들어 다음 명령은 loaduser의 리소스 클래스를 **largerc**로 늘립니다.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>리소스 클래스를 적용하지 않는 쿼리

더 큰 메모리를 할당해도 별로 도움이 되지 않는 일부 쿼리 형식이 있습니다. 시스템은 리소스 클래스 할당을 무시하고, 대신 이러한 쿼리를 항상 작은 리소스 클래스에서 실행합니다. 이러한 쿼리가 항상 작은 리소스 클래스에서 실행되는 경우 동시성 슬롯이 압박 하에서 실행될 수 있으며 필요한 것보다 더 많은 수의 슬롯을 사용하지 않게 됩니다. 자세한 내용은 [리소스 클래스 예외](#query-exceptions-to-concurrency-limits) 를 참조하세요.

## <a name="details-on-resource-class-assignment"></a>리소스 클래스 할당에 대한 세부 정보


리소스 클래스에 대한 추가적인 몇 가지 자세한 정보.

* *역할 변경* 권한이 필요합니다.
* 하나 이상의 더 높은 리소스 클래스에 사용자를 추가할 수 있지만 동적 리소스 클래스는 정적 리소스 클래스보다 우선합니다. 즉, 사용자가 **mediumrc**(동적) 및 **staticrc80**(정적) 둘 다에 할당되는 경우, 적용되는 리소스 클래스는 **mediumrc**입니다.
 * 사용자가 특정 리소스 클래스 형식의 둘 이상의 리소스 클래스(둘 이상의 동적 리소스 클래스 또는 둘 이상의 정적 리소스 클래스)에 할당되는 경우, 최상위 리소스 클래스가 적용됩니다. 즉, 사용자가 mediumrc와 largerc 둘 다에 할당되면 더 높은 리소스 클래스인 largerc가 적용됩니다. 사용자가 **staticrc20** 및 **statirc80** 둘 다에 할당되면 **staticrc80**이 적용됩니다.
* 시스템 관리 사용자의 리소스 클래스는 변경할 수 없습니다.

자세한 예제는 [사용자 리소스 클래스 변경 예제](#changing-user-resource-class-example)를 참조하세요.

## <a name="memory-allocation"></a>메모리 할당
사용자의 리소스 클래스를 늘리는 데 따른 장단점이 있습니다. 사용자에 대한 리소스 클래스를 늘리면 쿼리가 더 많은 메모리에 액세스할 수 있게 되고 이렇게 되면 쿼리가 더 빨리 실행됩니다.  하지만 리소스 클래스가 높아질수록 동시에 실행될 수 있는 쿼리의 수도 줄어듭니다. 즉, 단일 쿼리에 많은 양의 메모리를 할당하거나 동시 실행을 위해 역시 메모리 할당이 필요한 다른 쿼리를 허용하는 방식 중에서 하나를 고려해야 합니다. 한 명의 사용자가 쿼리에 대한 메모리 할당을 높게 받는 경우 다른 사용자들은 쿼리를 실행할 목적으로 동일한 메모리에 액세스할 수 없습니다.

다음 표는 DWU 및 리소스 클래스에 의해 각 배포에 할당된 메모리를 매핑합니다.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>동적 리소스 클래스에 대한 배포별 메모리 할당(MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

다음 표는 DWU 및 정적 리소스 클래스별로 각 배포에 할당된 메모리를 매핑합니다. 리소스 클래스가 높을수록 글로벌 DWU 제한에 맞춰 메모리가 줄어드는 점에 유의합니다.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>정적 리소스 클래스에 대한 배포별 메모리 할당(MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

이전 테이블에서, **xlargerc** 리소스 클래스의 DW2000에서 실행되는 쿼리는 배포된 60개의 각 데이터베이스 내에서 6,400MB의 메모리에 액세스하게 됩니다.  SQL 데이터 웨어하우스에는 배포가 60개 있습니다. 따라서, 주어진 리소스 클래스 내의 쿼리에 대한 총 메모리 할당을 계산하려면, 위의 값에 60을 곱해야 합니다.

### <a name="memory-allocations-system-wide-gb"></a>시스템 전체 메모리 할당(GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

시스템 전체 메모리 할당 테이블에서 보면 xlargerc 리소스 클래스의 DW2000에서 실행되는 쿼리에는 전체 SQL Data Warehouse에 대해 총 375GB(6,400MB * 60개 배포/1,024, 이 결과를 GB로 변환)의 메모리가 할당됩니다.

동일한 계산이 정적 리소스 클래스에도 적용됩니다.
 
## <a name="concurrency-slot-consumption"></a>동시성 슬롯 사용량  
SQL Data Warehouse는 더 많은 리소스 클래스에서 실행되는 쿼리에 더 많은 메모리를 부여합니다. 메모리는 고정된 리소스입니다.  따라서 쿼리당 할당되는 메모리가 많을수록 동시에 실행할 수 있는 쿼리의 수가 줄어듭니다. 다음 테이블에서는 앞에서 설명한 모든 개념을 단일 보기로 다시 제공합니다. 이 보기에는 DWU에서 사용 가능한 동시성 슬롯의 수와 각 리소스 클래스가 사용하는 슬롯 수가 나와 있습니다.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>동적 리소스 클래스에 대한 동시성 슬롯의 할당 및 사용량  
| DWU | 최대 동시 쿼리 수 | 할당된 동시성 슬롯 수 | smallrc에서 사용되는 슬롯 | mediumrc에서 사용되는 슬롯 | largerc에서 사용되는 슬롯 | xlargerc에서 사용되는 슬롯 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>정적 리소스 클래스에 대한 동시성 슬롯의 할당 및 사용량  
| DWU | 최대 동시 쿼리 수 | 할당된 동시성 슬롯 수 |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

이러한 테이블에서 볼 수 있는 것처럼 DW1000으로 실행되는 SQL Data Warehouse는 최대 32개의 동시 쿼리 및 총 40개의 동시성 슬롯을 할당합니다. 모든 사용자가 smallrc에서 실행하는 경우 각 쿼리가 동시성 슬롯 1개를 소비하므로 32개의 동시성 쿼리가 허용됩니다. DW1000의 모든 사용자가 mediumrc에서 실행하는 경우, 각 쿼리에는 총 메모리 할당량 47GB에서 배포당 800MB가 할당되고 동시성은 5명의 사용자에게 제한됩니다(40개 동시성 슬롯/mediumrc 사용자당 8개 슬롯).

## <a name="selecting-proper-resource-class"></a>적절한 리소스 클래스 선택  
사용자의 리소스 클래스를 변경하기보다는 사용자를 리소스 클래스에 영구적으로 할당하는 것이 좋습니다. 예를 들어 클러스터형 Columnstore 테이블에 대한 로드 시에는 리소스를 많이 할당할수록 품질이 보다 뛰어난 인덱스가 작성됩니다. 로드가 더 높은 메모리에 액세스할 수 있도록 하려면 특별히 데이타 로드에 대한 사용자를 만들고 이 사용자를 더 높은 리소스 클래스에 영구적으로 할당합니다.
여기에서 준수할 수 있는 몇 가지 모범 사례가 있습니다. 위에서 언급한 것처럼 SQL DW에서는 두 가지 유형의 리소스 클래스 형식, 즉 정적 리소스 클래스 및 동적 리소스 클래스를 지원합니다.
### <a name="loading-best-practices"></a>로드 모범 사례
1.  보통 크기의 데이터가 로드될 것으로 예상되면 정적 리소스 클래스를 사용하는 것이 좋습니다. 나중에 계산 능력의 향상을 위해 확장할 경우 로드 사용자는 더 많은 메모리를 소비하지 않으므로 데이터 웨어하우스는 기본적으로 더 많은 동시 쿼리를 실행할 수 있게 됩니다.
2.  경우에 따라 더 큰 로드가 예상될 경우 동적 리소스 클래스를 사용하는 것이 좋습니다. 나중에 계산 능력의 향상을 위해 확장할 경우 로드 사용자는 기본적으로 더 많은 메모리를 얻게 되므로 로드를 더 빠르게 진행할 수 있게 됩니다.

로드를 효율적으로 처리하는 데 필요한 메모리는 로드된 테이블의 특성과 처리된 데이터의 양에 따라 다릅니다. 예를 들어 CCI 테이블로 데이터를 로드하려면 CCI 행 그룹이 최적성에 도달할 수 있도록 하는 데 메모리가 필요합니다. 자세한 내용은 Columnstore 인덱스 - 데이터 로드 지침을 참조하세요.

모범 사례로, 로드 시 200MB 이상의 메모리를 사용하는 것이 좋습니다.

### <a name="querying-best-practices"></a>쿼리 모범 사례
쿼리마다 복잡성에 따라 다른 요구 사항을 갖습니다. 쿼리당 메모리를 늘리거나 동시성을 늘리는 방법은 모두 쿼리 요구에 따라 전체 처리량을 늘릴 수 있는 효과적인 방법입니다.
1.  주기적으로 복잡한 쿼리가 예상되고(예: 매일 및 매주 보고서 생성) 동시성을 활용할 필요가 없는 경우 동적 리소스 클래스를 사용하는 것이 좋습니다. 시스템에 처리할 데이터가 더 많은 경우 데이터 웨어하우스를 확장하면 쿼리를 실행하는 사용자에게 자동으로 추가 메모리가 제공됩니다.
2.  가변 또는 일별 동시성 패턴이 예상되는 경우(예를 들어 광범위하게 액세스할 수 있는 웹 UI를 통해 데이터베이스를 쿼리하는 경우) 정적 리소스 클래스를 사용하는 것이 좋습니다. 나중에 데이터 웨어하우스를 확장할 때 정적 리소스 클래스와 연결된 사용자는 자동으로 더 많은 동시 쿼리를 실행할 수 있게 됩니다.

쿼리 요구에 따라 적절한 메모리 권한을 선택하는 작업은 쿼리되는 데이터의 양, 테이블 스키마의 특성, 다양한 조인, 선택 및 그룹 조건자 등에 따라 좌우되므로 복잡할 수 있습니다. 일반적인 관점에서 볼 때 더 많은 메모리를 할당하면 쿼리를 더 빠르게 완료할 수 있지만 전체적인 동시성은 떨어집니다. 동시성이 문제가 되지 않을 경우 메모리를 과도하게 할당해도 괜찮습니다. 처리량을 최적으로 조정하려면 다양한 리소스 클래스를 서로 시도해 보는 것이 좋습니다.

다음 저장 프로시저를 사용하여 지정된 SLO에서 리소스 클래스별 메모리 부여 및 동시성을 확인하고 지정된 리소스 클래스의 분할되지 않은 CCI 테이블에 대해 메모리 집약적 CCI 작업을 수행하기 위한 가장 적절한 리소스 클래스를 확인할 수 있습니다.

#### <a name="description"></a>설명:  
이 저장 프로시저의 용도는 다음과 같습니다.  
1. 사용자가 지정된 SLO에서 리소스 클래스별로 메모리 부여 및 동시성을 파악하도록 도와줍니다. 사용자는 아래 예제에 나와 있는 것처럼 이를 위해 스키마 및 테이블 이름 둘 다에 대해 NULL을 제공해야 합니다.  
2. 사용자가 지정된 리소스 클래스에서 분할되지 않은 CCI 테이블에 대해 메모리 집약적 CCI 작업(로드, 테이블 복사, 인덱스 다시 작성 등)을 수행하기 위해 가장 적합한 리소스 클래스를 찾아내도록 도와줍니다. 저장 프로시저는 테이블 스키마를 사용하여 필요한 메모리 부여량을 확인합니다.

#### <a name="dependencies--restrictions"></a>종속성 및 제한 사항:
- 이 저장 프로시저는 분할된 cci 테이블의 메모리 요구 사항을 계산하도록 디자인되지 않았습니다.    
- 이 저장 프로시저는 CTAS/INSERT-SELECT의 SELECT 부분에 대한 메모리 요구 사항을 고려하지 않으며 간단한 SELECT로 간주합니다.
- 이 저장 프로시저는 이 저장 프로시저가 만들어진 세션에서 사용할 수 있도록 임시 테이블을 사용합니다.    
- 이 저장 프로시저는 현재 제공 환경(예: 하드웨어 구성, DMS 구성)에 의존하므로 제공된 구성이 변경될 경우 이 저장 프로시저가 제대로 작동하지 않습니다.  
- 이 저장 프로시저는 기존의 제공된 동시성 제한에 의존하므로 이러한 동시성 제한이 변경되면 이 저장 프로시저가 제대로 작동하지 않습니다.  
- 이 저장 프로시저는 기존에 제공된 리소스 클래스에 의존하므로 이러한 리소스 클래스가 변경되면 이 저장 프로시저가 제대로 작동하지 않습니다.  

>  [!NOTE]  
>  제공된 매개 변수로 저장 프로시저를 실행한 후에 출력이 표시되지 않으면 다음 2가지 경우로 가정할 수 있습니다. <br />1. DW 매개 변수 중 하나에 잘못된 SLO 값이 포함되어 있습니다. <br />2. 또는 테이블 이름이 제공된 경우 CCI 작업에 대해 일치하는 리소스 클래스가 없습니다. <br />예를 들어 DW100에서 부여 가능한 최대 메모리는 400MB이고, 테이블 스키마 크기가 400MB의 요구 사항을 충족할 크기여야 합니다.
      
#### <a name="usage-example"></a>사용법 예제:
구문  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: NULL 매개 변수를 제공하여 DW DB에서 현재 DWU를 추출하거나 지원되는 모든 DWU를 'DW100' 형식으로 제공
2. @SCHEMA_NAME: 테이블의 스키마 이름 제공
3. @TABLE_NAME: 관심 있는 테이블 이름 제공

이 저장 프로시저 실행 예제:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

위의 예제에서 사용된 Table1은 아래와 같이 만들 수 있습니다.  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>저장 프로시저 정의는 다음과 같습니다.
```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(   @DWU VARCHAR(7),
      @SCHEMA_NAME VARCHAR(128),
       @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temptable (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>쿼리 중요도
SQL Data Warehouse는 워크로드 그룹을 사용하여 리소스 클래스를 구현합니다. 다양한 DWU 크기의 리소스 클래스 동작을 제어하는 총 8개의 워크로드 그룹이 있습니다. DWU의 경우 SQL Data Warehouse는 8개의 워크로드 그룹 중 4개만 사용합니다. 각 워크로드 그룹은 4가지 리소스 클래스 smallrc, mediumrc, largerc 또는 xlargerc 중 하나에 할당되기 때문에 이렇게 하는 것이 합리적입니다. 이들 워크로드 그룹의 일부가 더 높은 *중요도*로 설정된다는 점 때문에 워크로드 그룹을 이해하는 것이 중요합니다. 중요도가 CPU 예약에 사용됩니다. 높은 중요도를 갖고 실행되는 쿼리는 중간 중요도의 쿼리보다 3배 더 많은 CPU 사이클을 받게 될 것입니다. 따라서 동시성 슬롯 매핑은 또한 CPU 우선 순위를 결정합니다. 쿼리가 16개 이상의 슬롯을 사용할 경우 중요도 높음으로 실행됩니다.

다음 테이블에서는 각 워크로드 그룹에 대한 중요도 매핑을 보여 줍니다.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>동시성 슬롯 및 중요도에 대한 워크로드 그룹 매핑
| 워크로드 그룹 | 동시성 슬롯 매핑 | MB/배포 | 중요도 매핑 |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |중간 |
| SloDWGroupC01 |2 |200 |중간 |
| SloDWGroupC02 |4 |400 |중간 |
| SloDWGroupC03 |8 |800 |중간 |
| SloDWGroupC04 |16 |1,600 |높음 |
| SloDWGroupC05 |32 |3,200 |높음 |
| SloDWGroupC06 |64 |6,400 |높음 |
| SloDWGroupC07 |128 |12,800 |높음 |

**동시성 슬롯의 할당 및 사용량** 차트에서는 DW500이 smallrc, mediumrc, largerc 및 xlargerc 각각에 대해 1, 4, 8 또는 16의 동시성 슬롯을 사용한다는 확인할 수 있습니다. 위의 차트에서 이러한 값을 확인하여 각 리소스 클래스에 대한 중요도를 알 수 있습니다.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500의 리소스 클래스와 중요도 관계
| 리소스 클래스 | 워크로드 그룹 | 사용된 동시성 슬롯 수 | MB/배포 | 중요도 |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |중간 |
| mediumrc |SloDWGroupC02 |4 |400 |중간 |
| largerc |SloDWGroupC03 |8 |800 |중간 |
| xlargerc |SloDWGroupC04 |16 |1,600 |높음 |
| staticrc10 |SloDWGroupC00 |1 |100 |중간 |
| staticrc20 |SloDWGroupC01 |2 |200 |중간 |
| staticrc30 |SloDWGroupC02 |4 |400 |중간 |
| staticrc40 |SloDWGroupC03 |8 |800 |중간 |
| staticrc50 |SloDWGroupC03 |16 |1,600 |높음 |
| staticrc60 |SloDWGroupC03 |16 |1,600 |높음 |
| staticrc70 |SloDWGroupC03 |16 |1,600 |높음 |
| staticrc80 |SloDWGroupC03 |16 |1,600 |높음 |

다음 DMV 쿼리는 리소스 관리자의 관점에서 메모리 리소스 할당의 차이점을 자세히 살펴보거나, 또는 문제를 해결할 때 워크로드 그룹의 활성 및 사용 기록을 분석하는 데도 사용할 수 있습니다.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>동시성 한도를 적용하는 쿼리
대부분의 쿼리는 리소스 클래스에 의해 제어됩니다. 이러한 쿼리는 동시 쿼리 및 동시성 슬롯 임계값 둘 다를 벗어나지 않아야 합니다. 사용자는 동시성 슬롯 모델에서 쿼리를 제외하도록 선택할 수 없습니다.

다시 한 번 강조하지만 다음 문은 리소스 클래스를 인식합니다.

* INSERT-SELECT
* UPDATE
* 삭제
* SELECT(사용자 테이블을 쿼리하는 경우)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* 데이터 로드
* DMS(데이터 이동 서비스)에서 수행하는 데이터 이동 작업

## <a name="query-exceptions-to-concurrency-limits"></a>동시성 제한에 대한 쿼리 예외
일부 쿼리는 사용자가 할당된 리소스 클래스를 인식하지 않습니다. 동시성 제한에 대한 이러한 예외는 특정 명령에 필요한 메모리 리소스가 부족할 때(종종 명령이 메타데이터 작업이므로) 나타납니다. 이러한 예외는 필요하지 않은 더 큰 양의 메모리가 쿼리에 할당되는 것을 방지하기 위한 것입니다. 이러한 경우에는 사용자에게 할당된 실제 리소스 클래스와 상관없이 기본 또는 작은 리소스 클래스(smallrc)가 항상 사용됩니다. 예를 들어, `CREATE LOGIN` 은 항상 smallrc에서 실행됩니다. 이 작업을 수행하는 데 필요한 리소스가 매우 부족하고 따라서 동시성 슬롯 모델에 쿼리를 포함하는 것이 적합하지 않습니다.  이러한 쿼리는 또한 32명의 사용자 동시성 제한으로 제한되지 않으며, 이러한 쿼리를 개수 제한 없이 1,024개의 세션 제한까지 실행할 수 있습니다.

다음 문은 리소스 클래스를 인식하지 않습니다.

* CREATE 또는 DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT 또는 MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE 또는 DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER 또는 DROP USER
* CREATE, ALTER 또는 DROP PROCEDURE
* CREATE 또는 DROP VIEW
* INSERT VALUES
* SELECT(시스템 뷰와 DMV에서)
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> 사용자 리소스 클래스 변경 예제
1. **로그인 만들기:** SQL Data Warehouse 데이터베이스를 호스트하는 SQL Server에서 **마스터** 데이터베이스에 대한 연결을 열고 다음 명령을 실행합니다.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Azure SQL Data Warehouse 사용자에 대해 마스터 데이터베이스에 사용자를 만드는 것이 좋습니다. 마스터에서 사용자를 만들면 데이터베이스 이름을 지정하지 않아도 사용자가 SSMS 등의 도구를 사용하여 로그인할 수 있습니다.  또한 개체 탐색기를 사용하여 SQL server의 모든 데이터베이스를 볼 수 있습니다.  사용자를 만들고 관리하는 방법에 대한 자세한 내용은 [SQL Data Warehouse에서 데이터베이스 보호][Secure a database in SQL Data Warehouse]를 참조하세요.
   > 
   > 
2. **SQL Data Warehouse 사용자 만들기:** **SQL Data Warehouse** 데이터베이스에 대한 연결을 열고 다음 명령을 실행합니다.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **사용 권한 부여:** 다음 예제에서는 **SQL Data Warehouse** 데이터베이스에 `CONTROL`을 부여합니다. 데이터베이스 수준에서 `CONTROL`은 SQL Server의 db_owner와 동일합니다.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **리소스 클래스 증가:** 다음 쿼리를 사용하여 더 높은 워크로드 관리 역할에 사용자를 추가합니다.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **리소스 클래스 감소:** 다음 쿼리를 사용하여 워크로드 관리 역할에서 사용자를 제거합니다.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > smallrc에서 사용자를 제거하는 것은 불가능합니다.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>큐에 대기 중인 쿼리 검색 및 다른 DMV
`sys.dm_pdw_exec_requests` DMV는 동시성 큐에 대기 중인 쿼리를 식별하는 데 사용할 수 있습니다. 동시성 슬롯을 대기하는 쿼리는 **일시 중단**상태가 됩니다.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

워크로드 관리 역할은 `sys.database_principals`를 사용하여 확인할 수 있습니다.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

다음 쿼리는 각 사용자에게 어떤 역할이 할당되었는지 보여줍니다.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse에 다음과 같은 대기 형식이 있습니다.

* **LocalQueriesConcurrencyResourceType**: 동시성 슬롯 프레임워크 외부에 존재하는 쿼리. `SELECT @@VERSION` 과 같은 DMV 쿼리 및 시스템 함수는 로컬 쿼리의 예입니다.
* **UserConcurrencyResourceType**: 동시성 슬롯 프레임워크 내에 존재하는 쿼리. 최종 사용자 테이블에 대한 쿼리는 이 리소스 형식을 사용하는 예를 나타냅니다.
* **DmsConcurrencyResourceType**: 데이터 이동 작업으로 초래된 대기
* **BackupConcurrencyResourceType**: 이 대기는 데이터베이스가 백업 중임을 나타냅니다. 이 리소스 유형에 대한 최대값은 1입니다. 여러 백업을 동시에 요청한 경우 다른 백업 요청은 큐에 저장됩니다.

`sys.dm_pdw_waits` DMV는 요청이 대기 중인 리소스를 알아내는 데 사용할 수 있습니다.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

`sys.dm_pdw_resource_waits` DMV는 지정된 쿼리에 사용되는 리소스 대기만 표시합니다. 리소스 대기 시간은 기본 SQL Server가 CPU에 대해 쿼리를 예약하는 데 소요되는 대기 시간을 나타내는 것과 달리 리소스가 제공될 때까지 대기하는 시간만 측정합니다.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

`sys.dm_pdw_wait_stats` DMV는 기록 추세 분석에 사용할 수 있습니다.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>다음 단계
데이터베이스 사용자 및 보안을 관리하는 방법에 대한 자세한 내용은 [SQL Data Warehouse에서 데이터베이스 보호][Secure a database in SQL Data Warehouse]를 참조하세요. 더 큰 리소스 클래스가 클러스터된 columnstore 인덱스 품질을 향상할 방법에 대한 자세한 내용은 [인덱스를 다시 빌드하여 세그먼트 품질 개선]을 참조하세요.

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[인덱스를 다시 빌드하여 세그먼트 품질 개선]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
