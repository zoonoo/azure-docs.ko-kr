---
title: MySQL용 Azure Database의 가격 책정 계층 | Microsoft Docs
description: MySQL용 Azure Database의 가격 책정 계층
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: ae7e57e9b40f5194c15525a48843060bbccaa956
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678354"
---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>MySQL용 Azure Database 옵션 및 성능: 각 가격 책정 계층에서 사용할 수 있는 항목 이해
MySQL 서버용 Azure Database를 만들 때 해당 서버에 할당되는 리소스를 구성하는 세 가지 주요 선택 사항을 결정합니다. 이 선택 사항은 서버의 성능과 규모에 영향을 미칩니다.
- 가격 책정 계층 
- Compute 단위
- 저장소(GB)

각 가격 책정 계층에는 워크로드 요구 사항에 따라 선택할 수 있는 다양한 성능 수준(Compute 단위)이 있습니다. 더 높은 성능 수준은 더 높은 처리량을 제공하도록 설계된 서버에 추가 리소스를 제공합니다. 실제로 응용 프로그램 가동 중지 시간 없이 가격 책정 계층 내에서 서버의 성능 수준을 변경할 수 있습니다.

> [!IMPORTANT]
> 서비스가 공개 미리 보기 상태인 동안은 SLA(서비스 수준 약정)가 보장되지 않습니다.

MySQL 서버용 Azure Database 내에서 하나 이상의 데이터베이스를 포함할 수 있습니다. 서버당 단일 데이터베이스를 만들어 모든 리소스를 활용하도록 하거나 여러 데이터베이스를 만들어 리소스를 공유하도록 할 수 있습니다. 

## <a name="choose-a-pricing-tier"></a>가격 책정 계층 선택
미리 보기 상태에서 MySQL용 Azure Database는 두 가지 가격 책정 계층(기본 및 표준)을 제공합니다. 프리미엄 계층은 아직 사용할 수 없지만 곧 제공됩니다. 

다음 표에서 다양한 응용 프로그램 워크로드에 가장 적합한 가격 책정 계층의 예제를 제공합니다.

| 가격 책정 계층 | 대상 워크로드 |
| :----------- | :----------------|
| Basic | IOPS 보장 없이 확장 가능한 Compute 및 저장 단위를 필요로 하는 작은 작업에 가장 적합합니다. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 응용 프로그램에 사용되는 서버가 이에 해당합니다. |
| Standard | 처리량이 많은 IOPS 보장이 필요한 클라우드 응용 프로그램을 위해 준비된 옵션입니다. 예제에는 웹 또는 분석 응용 프로그램이 포함됩니다. |
| Premium | 트랜잭션 및 IO의 대기 시간이 짧아야 하는 워크로드에 가장 적합합니다. 많은 동시 사용자에 대해 최상의 지원을 제공합니다. 업무에 중요한 응용 프로그램을 지원하는 데이터베이스에 적용할 수 있습니다.<br />프리미엄 가격 책정 계층은 미리 보기에서 사용할 수 없습니다. |

가격 책정 계층을 결정하려면 먼저 워크로드에 IOPS 보장이 필요한지를 결정합니다. 필요한 경우 표준 가격 책정 계층을 사용합니다.

| **가격 책정 계층 기능** | **Basic** | **Standard** |
| :------------------------ | :-------- | :----------- |
| 최대 Compute 단위 | 100 | 800 | 
| 최대 총 저장소 | 1TB | 1TB | 
| Storage IOPS 보장 | 해당 없음 | 예 | 
| 최대 저장소 IOPS | 해당 없음 | 3,000 | 
| 데이터베이스 백업 보존 기간 | 7 일 | 35일 | 

미리 보기 기간에는 서버를 만들면 가격 책정 계층을 변경할 수 없습니다. 나중에 한 가격 책정 계층에서 다른 계층으로 서버를 업그레이드하거나 다운그레이드할 수 있을 것입니다.

## <a name="understand-the-price"></a>가격 이해
[Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) 내에서 Azure Database for MySQL을 만드는 경우 **가격 책정 계층** 페이지를 선택하면 월간 비용이 선택한 옵션에 따라 표시됩니다. Azure 구독이 없는 경우 Azure 가격 책정 계산기를 사용하여 예상된 가격을 알아봅니다. [Azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/) 웹 사이트를 방문하여 **항목 추가**를 클릭하고, **데이터베이스** 범주를 확장하고, **MySQL용 Azure Database**를 선택하여 옵션을 사용자 지정합니다.

## <a name="choose-a-performance-level-compute-units"></a>성능 수준(Compute 단위) 선택
MySQL 서버용 Azure Database의 가격 책정 계층을 결정한 경우 필요한 Compute 단위 수를 선택하여 성능 수준을 결정할 수 있습니다. 웹 또는 분석 워크로드를 위해 더 높은 사용자 동시성을 요구하는 응용 프로그램에 대한 좋은 시작점은 200 또는 400 Compute 단위이며 필요에 따라 증분 방식으로 조정합니다. 

Compute 단위는 MySQL 서버용 단일 Azure Database에서 사용할 수 있도록 보장되는 CPU 처리량을 측정하는 단위입니다. Compute 단위는 CPU 및 메모리 리소스를 혼합해서 측정합니다.  자세한 내용은 [Compute 단위 설명](concepts-compute-unit-and-storage.md)을 참조하세요.

### <a name="basic-pricing-tier-performance-levels"></a>기본 가격 책정 계층 성능 수준:

| **성능 수준** | **50** | **100** |
| :-------------------- | :----- | :------ |
| 최대 Compute 단위 | 50 | 100 |
| 포함된 저장소 크기 | 50GB | 50GB |
| 최대 서버 저장소 크기\* | 1TB | 1TB |

### <a name="standard-pricing-tier-performance-levels"></a>표준 가격 책정 계층 성능 수준:

| **성능 수준** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| 최대 Compute 단위 | 100 | 200 | 400 | 800 |
| 포함된 저장소 크기 및 프로비전된 IOPS | 125GB,<br/> 375 IOPS | 125GB,<br/> 375 IOPS | 125GB,<br/> 375 IOPS | 125GB,<br/> 375 IOPS |
| 최대 서버 저장소 크기\* | 1TB | 1TB | 1TB | 1TB |
| 프로비전된 최대 서버 IOPS | 3,000 IOPS | 3,000 IOPS | 3,000 IOPS | 3,000 IOPS |
| GB당 프로비전된 최대 서버 IOPS | GB당 고정된 3 IOPS | GB당 고정된 3 IOPS | GB당 고정된 3 IOPS | GB당 고정된 3 IOPS |

\* 최대 서버 저장소 크기는 서버에 대해 프로비전된 최대 저장소 크기를 나타냅니다.

## <a name="storage"></a>Storage 
저장소 구성은 MySQL 서버용 Azure Database에 사용할 수 있는 저장소 용량을 정의합니다. 서비스에서 사용되는 저장소에는 데이터베이스 파일, 트랜잭션 로그 및 MySQL 서버 로그가 포함됩니다. 저장소 구성 선택 시 성능 요구 사항(IOPS) 및 데이터베이스를 호스트하는 데 필요한 저장소의 크기를 고려합니다.

일부 저장소 용량은 위의 표에서 "포함된 저장소 크기"에 나와 있는 대로 각 가격 책정 계층에서 최소 크기로 포함됩니다. 서버를 만들 때 최대 허용 저장소까지 125GB 단위로 저장소 용량을 추가할 수 있습니다. 추가 저장소 용량은 Compute 단위 구성과는 별도로 구성할 수 있습니다. 선택한 저장소의 크기에 따라 가격이 변경됩니다.

각 성능 수준에서 IOPS 구성은 가격 책정 계층 및 선택한 저장소 크기와 관련이 있습니다. 기본 계층에서는 IOPS 보장을 제공하지 않습니다. 표준 가격 책정 계층에서 IOPS는 고정된 3:1 비율로 최대 저장소 크기에 비례하여 크기를 조정합니다. 125GB의 포함된 저장소는 최대 IO 크기가 각각 256KB인 375의 프로비전된 IOPS를 보장합니다. 최대 1TB까지 저장소를 추가하여 프로비전된 3000 IOPS를 보장할 수 있습니다.

Azure Portal에서 메트릭 그래프를 모니터링하거나 Azure CLI 명령을 작성하여 저장소 사용률 및 IOPS를 측정합니다. 모니터링할 관련 메트릭은 저장소 용량 한도, 저장소 비율, 사용된 저장소 및 IO 비율입니다.

>[!IMPORTANT]
> 미리 보기 상태에서 서버를 만들 때 저장소 크기를 선택합니다. 기존 서버의 저장소 크기 변경은 아직 지원되지 않습니다. 

## <a name="scaling-a-server-up-or-down"></a>서버 확장 또는 축소
MySQL용 Azure Database를 만들 때 처음 가격 책정 계층 및 성능 수준을 선택합니다. 나중에 Compute 단위를 동일한 가격 책정 계층의 범위 내에서 동적으로 확장 또는 축소할 수 있습니다. Azure Portal에서, 서버의 가격 책정 계층 페이지에서 Compute 단위를 밀거나 예제 [Azure CLI를 사용하여 Azure Database for MySQL 서버 모니터링 및 확장](scripts/sample-scale-server.md) 예제를 따라 스크립팅합니다.

Compute 단위 크기 조정은 선택한 최대 저장소 크기와는 별개로 수행됩니다.

백그라운드에서 서버의 성능 수준이 변경되면 새 성능 수준에서 원본 서버의 복제본이 생성되고 나서 연결이 복제된 서버로 전환됩니다. 이 프로세스 중에는 데이터가 손실되지 않습니다. 복제본으로 전환할 경우 잠깐 동안 시스템이 서버의 새 복제본으로 전환되고 데이터베이스에 대한 연결이 비활성화되므로 처리 중인 일부 트랜잭션이 롤백될 수 있습니다. 하지만 평균 4초 이내 또 사례의 99% 이상에서 30초 미만으로 이 창이 달라집니다. 현재 연결에서 비행의 트랜잭션 다수를 사용할 수 없는 경우 이 창을 더 이상 유지할 수 없습니다.

전체 크기 조정 프로세스 기간은 변경 전후 서버의 크기 및 가격 책정 계층에 따라 달라집니다. 예를 들어 표준 가격 책정 계층 내에서 Compute 단위를 변경하는 서버는 몇 분 안에 완료해야 합니다. 서버의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

## <a name="next-steps"></a>다음 단계
- Compute 단위에 대한 자세한 내용은 [Compute 단위 설명](concepts-compute-unit-and-storage.md)을 참조하세요.
- [Azure CLI를 사용하여 MySQL 서버용 Azure Database 모니터링 및 확장](scripts/sample-scale-server.md) 방법을 알아봅니다.
