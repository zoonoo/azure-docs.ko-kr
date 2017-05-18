---
title: "MySQL용 Azure 데이터베이스의 서비스 계층 | Microsoft Docs"
description: "MySQL용 Azure 데이터베이스의 서비스 계층"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b647db3c3a48ac6c151814ee68b3117a92c1d4d8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---
# <a name="azure-database-for-mysql-options-and-performance-understand-whats-available-in-each-service-tier"></a>MySQL용 Azure 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 항목 이해
MySQL용 Azure 데이터베이스는 기본 및 표준 서비스 계층을 제공합니다. 프리미엄 서비스 계층은 아직 사용할 수 없습니다.

각 서비스 계층은 여러 다른 유형의 작업 요구를 처리하기 위한 다양한 성능 수준을 제공합니다. 더 높은 성능 수준은 더욱 높은 처리량을 제공하도록 설계된 추가 리소스를 제공합니다. 응용 프로그램 가동 중지 시간 없이 동적으로 서비스 계층 내의 성능 수준을 변경할 수 있습니다.

나중에 한 서비스 계층에서 다른 서비스 계층으로 업그레이드하거나 다운그레이드할 수 있을 것입니다. 

> [!IMPORTANT]
> 이 서비스는 현재 공개 미리 보기로 제공되므로 SLA(서비스 수준 계약)는 아직 제공되지 않습니다.

특정 성능 수준의 서비스 계층 내에서 전용 리소스를 사용하여 단일 MySQL용 Azure 데이터베이스를 만들 수 있습니다. 그런 후 여러 데이터베이스 간에 리소스가 공유되는 서버 내에서 하나 또는 여러 개의 데이터베이스를 만들 수도 있습니다. 단일 MySQL용 Azure 데이터베이스 서버에 사용할 수 있는 리소스는 Compute 단위 및 저장 단위로 표현됩니다. Compute 단위 및 저장 단위에 대한 자세한 내용은 [Compute 단위 및 저장 단위에 대해 설명](concepts-compute-unit-and-storage.md)을 참조하세요.

## <a name="choosing-a-service-tier"></a>서비스 계층 선택

다음 표에서 다양한 응용 프로그램 워크로드에 가장 적합한 계층의 예제를 제공합니다.

| 서비스 계층 | 대상 워크로드 |
| :----------- | :----------------|
| Basic | IOPS 보장 없이 확장 가능한 Compute 및 저장 단위를 필요로 하는 작은 작업에 가장 적합합니다. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 응용 프로그램에 사용되는 서버가 이에 해당합니다. |
| Standard | 높은 처리량을 위해 더 높은 Compute 단위 및 저장 단위로 별도로 확장하는 기능을 포함하는 IOPS 보증이 필요한 클라우드 응용 프로그램을 위한 준비된 옵션입니다. 예제에는 웹 또는 분석 응용 프로그램이 포함됩니다. |
| Premium | 높은 IO 및 작업 처리량과 함께, 트랜잭션 및 IO에 대해 매우 짧은 대기 시간을 요구하는 작업에 가장 적합합니다. 많은 동시 사용자에 대해 최상의 지원을 제공합니다. 업무에 중요한 응용 프로그램을 지원하는 데이터베이스에 적용됩니다.<br />프리미엄 서비스 계층은 미리 보기에서 사용할 수 없습니다. |
| &nbsp; | &nbsp; |

서비스 계층을 결정하려면 먼저 작업에 IOPS 보장이 필요한지를 결정합니다. 그런 후 필요한 최소 기능을 결정합니다.

| **서비스 계층 기능** | **Basic** | **Standard** | **프리미엄** * |
| :------------------------ | :-------- | :----------- | :------------ |
| 최대 Compute 단위 | 100 | 2000 | 미리 보기에서 사용할 수 없음 |
| 최대 총 저장소 | 1050GB | 10000GB | 미리 보기에서 사용할 수 없음 |
| 저장소 IOPS 보장 | 해당 없음 | 예 | 미리 보기에서 사용할 수 없음 |
| 최대 저장소 IOPS | 해당 없음 | 30,000 | 미리 보기에서 사용할 수 없음 |
| 데이터베이스 백업 보존 기간 | 7 일 | 35일 | 35일 |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; |

> [!NOTE]
> 미리 보기의 표준 서비스 계층은 현재 최대 800 Compute 단위 및 1000GB 저장소를 지원합니다.

최소 서비스 계층을 결정한 경우 MySQL용 Azure 데이터베이스 서버에 대한 성능 수준을 결정할 수 있습니다(Compute 단위). 표준 200 및 400 Compute 단위는 웹 또는 분석 작업을 위해 더 높은 사용자 동시성을 요구하는 응용 프로그램에 대한 좋은 시작점입니다. 

그러나 Compute 단위는 작업의 요구 사항에 따라 저장 단위와는 별도로 확장하거나 축소할 수 있습니다. 작업의 Compute 리소스를 조정해야 하는 경우 Compute 단위를 동적으로 늘리거나 줄일 수 있습니다. 작업에 더 많은 IOPS 또는 저장소가 필요한 경우 저장소도 확장할 수 있습니다.

> [!NOTE]
> 미리 보기에서 기본 및 표준 계층은 현재 저장소의 동적 크기 조정을 지원하지 않습니다. 나중에 이 기능을 추가할 계획입니다.

> [!NOTE]
> 표준 서비스 계층에서 IOPS는 고정된 3:1 비율로 프로비전된 저장소 크기에 비례해서 조정됩니다. 125GB의 포함된 저장소는 최대 IO 크기가 각각 256KB인 375의 프로비전된 IOPS를 보장합니다. 1000GB를 프로비전하는 경우 프로비전된 3,000의 IOPS를 얻게 됩니다. 사용 가능한 프로비전된 IOPS를 완전히 활용하려면 서버 Compute 단위 사용량을 모니터링하고 확장해야 합니다.

## <a name="service-tiers-and-performance-levels"></a>서비스 계층 및 성능 수준

MySQL용 Azure 데이터베이스는 각 서비스 계층 내에서 여러 성능 수준을 제공합니다. 다음 중 하나를 사용하여 작업 요구에 가장 잘 맞는 수준을 선택할 수 있는 유연성이 있습니다.

- [Azure 포털](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

각 MySQL 서버 내에 호스트된 데이터베이스 수에 관계 없이, 데이터베이스는 보장된 리소스 집합을 가져오며 서버의 예상되는 성능 특징은 영향을 받지 않습니다.

기본 서비스 계층:

| **성능 수준** | **50** | **100** |
| :-------------------- | :----- | :------ |
| 최대 Compute 단위 | 50 | 100 |
| 포함된 저장소 크기 | 50GB | 50GB |
| 최대 서버 저장소 크기\* | 1050GB | 1050GB |
| 최대 동시 로그인 | &nbsp; | &nbsp; |
| 최대 연결 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; |

표준 서비스 계층:

| **성능 수준** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| 최대 Compute 단위 | 100 | 200 | 400 | 800 |
| 포함된 저장소 크기 및 프로비전된 IOPS | 125GB, 375 IOPS | &nbsp; | &nbsp; | &nbsp; |
| 최대 서버 저장소 크기\* | 1TB | &nbsp; | &nbsp; | &nbsp; |
| 프로비전된 최대 서버 IOPS | 3000 IOPS | &nbsp; | &nbsp; | &nbsp; |
| GB당 프로비전된 최대 서버 IOPS | GB당 고정된 3 IOPS | &nbsp; | &nbsp; | &nbsp; |
| 최대 동시 로그인 | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| 최대 연결 | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |

\* 최대 서버 저장소 크기는 서버에 대해 프로비전된 최대 저장소 크기를 나타냅니다.

## <a name="scaling-up-or-down-a-single-server"></a>단일 서버 확장 또는 축소

처음에 서비스 계층 및 성능 수준을 선택한 후에 작업 요구에 따라 서버를 동적으로 확장 또는 축소할 수 있습니다. 규모를 확장 또는 축소해야 하는 경우는 Azure Portal 또는 Azure CLI를 사용하여 데이터베이스의 계층을 쉽게 변경할 수 있습니다.

서비스 계층 및/또는 데이터베이스의 성능 수준을 변경하게 되면 새 성능 수준에서 원본 데이터베이스의 복제본을 만들고 연결을 복제본으로 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 하지만 평균 4초 이내 또 사례의 99% 이상에서 30초 미만으로 이 창이 달라집니다. 현재 연결에서 비행의 트랜잭션 다수를 사용할 수 없는 경우 이 창을 더 이상 유지할 수 없습니다.

전체 확장 프로세스 기간은 변경 전후 서버의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 표준 서비스 계층으로/부터 또는 표준 서비스 계층 내에서 Compute 단위를 변경하는 서버는 몇 분 안에 완료해야 합니다. 서버의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

### <a name="documentation-about-the-steps-for-scaling-up-or-down"></a>확장 또는 축소 단계에 대한 설명서

- [Azure Portal을 사용하여 단일 서버 관리](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI를 사용하여 단일 서버 관리](quickstart-create-mysql-server-database-using-azure-cli.md)

### <a name="details-about-scaling-up-or-down"></a>확장 또는 축소에 대한 세부 정보

- 서버를 다운그레이드하려면 서버 저장 단위가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다.
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. 다운그레이드하는 경우 지정 시간으로 복원하는 기능을 잃게 되거나 백업 보존 기간이 단축될 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 MySQL용 Azure 데이터베이스를 백업 및 복원하는 방법](./howto-restore-server-portal.md)을 참조하세요.
- 서버의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.
