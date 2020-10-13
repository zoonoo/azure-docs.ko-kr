---
title: 필수 서브넷 크기 & 범위 확인
titleSuffix: Azure SQL Managed Instance
description: 이 항목에서는 Azure SQL Managed Instance를 배포할 서브넷의 크기를 계산 하는 방법에 대해 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617641"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대 한 필수 서브넷 크기 & 범위 확인
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 Azure [VNet (가상 네트워크)](../../virtual-network/virtual-networks-overview.md)내에 배포 되어야 합니다.

VNet의 서브넷에 배포할 수 있는 관리 되는 인스턴스 수는 서브넷의 크기 (서브넷 범위)에 따라 달라 집니다.

관리 되는 인스턴스를 만들 때 Azure는 프로 비전 중에 선택한 계층에 따라 여러 가상 머신을 할당 합니다. 이러한 가상 머신이 서브넷에 연결되어 있기 때문에 IP 주소가 필요합니다. 일반 작업 및 서비스 유지 관리 중에 고가용성을 보장하기 위해 Azure에서는 가상 머신을 추가로 할당할 수 있습니다. 따라서 서브넷의 필수 IP 주소 수가 해당 서브넷에 있는 관리 되는 인스턴스의 수보다 큽니다.

기본적으로 Managed Instance의 서브넷에는 최소 32개의 IP 주소가 필요합니다. 따라서 서브넷 IP 범위를 정의할 때 최소 서브넷 마스크 /27을 사용할 수 있습니다. Managed Instance 배포에 대한 서브넷 크기를 신중하게 계획하는 것이 좋습니다. 계획 중에 고려해 야 하는 입력은 다음과 같습니다.

- 다음 인스턴스 매개 변수를 포함 하는 관리 되는 인스턴스 수:
  - 서비스 계층
  - 하드웨어 생성
  - vCores 수
- 확장/축소 또는 서비스 계층 변경 계획

> [!IMPORTANT]
> IP 주소가 16 개인 서브넷 크기 (서브넷 마스크/28)는 관리 되는 인스턴스를 배포 하는 것을 허용 하지만 평가에 사용 되는 단일 인스턴스를 배포 하거나 크기 조정 작업을 수행 하지 않는 개발/테스트 시나리오에만 사용 해야 합니다.

## <a name="determine-subnet-size"></a>서브넷 크기 결정

이후 인스턴스 배포 및 크기 조정 요구 사항에 따라 서브넷 크기를 조정 합니다. 다음 매개 변수는 계산을 형성 하는 데 도움이 될 수 있습니다.

- Azure는 고유한 요구 사항으로 인해 서브넷에서 5개의 IP 주소를 사용합니다.
- 각 가상 클러스터는 추가 주소 수를 할당 합니다. 
- 각 관리 되는 인스턴스는 가격 책정 계층 및 하드웨어 생성에 따라 달라 지는 주소 수를 사용 합니다.

> [!IMPORTANT]
> 서브넷에 리소스가 있으면 서브넷 주소 범위를 변경할 수 없습니다. 또한 한 서브넷에서 다른 서브넷으로 관리 되는 인스턴스를 이동할 수 없습니다. 가능 하면 나중에 문제를 방지 하기 위해 더 큰 서브넷을 사용 하는 것이 좋습니다.

GP = 범용 BC = 업무상 중요 합니다. VC = 가상 클러스터

| **하드웨어 gen** | **가격 책정 계층** | **Azure 사용량** | **VC 사용** | **인스턴스 사용** | **개수*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| 5세대 | GP | 5 | 6 | 3 | 14 |
| 5세대 | BC | 5 | 6 | 5 | 16 |

  \* 열 합계는 한 인스턴스가 서브넷에 배포 될 때 수행할 수 있는 주소 수를 표시 합니다. 서브넷의 각 추가 인스턴스는 인스턴스 사용 열로 표시 되는 주소 수를 추가 합니다. Azure 사용 열로 표시 된 주소는 여러 가상 클러스터에서 공유 되는 반면, VC 사용 열로 표시 된 주소는 해당 가상 클러스터에 배치 된 인스턴스 간에 공유 됩니다.

업데이트 작업에는 일반적으로 가상 클러스터 크기 조정이 필요 합니다. 업데이트 작업을 수행 하려면 가상 클러스터를 만들어야 하는 경우도 있습니다. 자세한 내용은 [관리 작업 확인 문서](sql-managed-instance-paas-overview.md#management-operations)를 참조 하세요. 가상 클러스터를 만들 경우 필요한 추가 주소 수는 해당 가상 클러스터 (인스턴스 사용 열)에 배치 된 인스턴스에 필요한 주소와 함께 VC 사용 열에서 나타내는 주소 수와 같습니다.

**예 1**: 범용 관리 되는 인스턴스 하나 (Gen4 하드웨어)와 하나의 업무상 중요 한 관리 되는 인스턴스 (Gen5 하드웨어)를 사용할 계획입니다. 즉, 배포할 수 있으려면 최소 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 개의 IP 주소가 필요 합니다. IP 범위가 2의 거듭제곱으로 정의 되 면 서브넷에는이 배포에 대해 최소 IP 범위 32 (2 ^ 5)가 필요 합니다.<br><br>
위에서 설명한 것 처럼 업데이트 작업을 수행 하려면 가상 클러스터를 만들어야 합니다. 따라서 예를 들어 가상 클러스터를 만들어야 하는 Gen5 업무상 중요 한 관리 되는 인스턴스를 업데이트 하는 경우 6 + 5 = 11 개의 IP 주소를 추가로 사용할 수 있어야 합니다. 32 주소 중 22 개를 이미 사용 하 고 있으므로이 작업에 사용할 수 있는 주소가 없습니다. 따라서/26 (64 주소)의 서브넷 마스크를 사용 하 여 서브넷을 예약 해야 합니다.

**예 2**: 동일한 서브넷에 배치 된 세 가지 범용 (Gen5 하드웨어)과 2 개의 업무상 중요 한 관리 되는 인스턴스 (Gen5 하드웨어)를 사용할 계획입니다. 즉, 5 + 6 + 3 * 3 + 2 * 5 = 30 개의 IP 주소가 필요 합니다. 따라서 서브넷 마스크/26을 사용 하 여 서브넷을 예약 해야 합니다. /27의 서브넷 마스크를 선택 하면 나머지 주소 수가 2 (32 – 30)가 되 고,이로 인해 인스턴스 크기 조정을 수행 하는 서브넷에 추가 주소가 필요 하기 때문에 모든 인스턴스에 대 한 업데이트 작업이 수행 되지 않습니다.

**예 3**: 특정 범용 관리 되는 인스턴스 (Gen5 하드웨어)를 포함 하 고 시간에서 vcores 업데이트 작업을 수행 하도록 계획 합니다. 즉, 5 + 6 + 1 * 3 + 3 = 17 개의 IP 주소가 필요 합니다. IP 범위가 2의 거듭제곱으로 정의되므로 32개(2^5)의 IP 주소라는 IP 범위가 필요합니다. 따라서 /27 서브넷 마스크를 포함하는 서브넷을 예약해야 합니다.

### <a name="address-requirements-for-update-scenarios"></a>업데이트 시나리오에 대 한 주소 요구 사항

크기 조정 작업 중에는 가격 책정 계층 및 하드웨어 생성에 따라 추가 IP 용량이 일시적으로 필요 합니다.

| **하드웨어 gen** | **가격 책정 계층** | **시나리오** | **추가 주소*** |
| --- | --- | --- | --- |
| Gen4 | GP 또는 BC | VCores 크기 조정 | 5 |
| Gen4 | GP 또는 BC | 저장소 크기 조정 | 5 |
| Gen4 | GP 또는 BC | GP에서 BC 또는 BC에서 GP로 전환 | 5 |
| Gen4 | GP | Gen5 *로 전환 | 9 |
| Gen4 | BC | Gen5 *로 전환 | 11 |
| 5세대 | GP | VCores 크기 조정 | 3 |
| 5세대 | GP | 저장소 크기 조정 | 0 |
| 5세대 | GP | BC로 전환 | 5 |
| 5세대 | BC | VCores 크기 조정 | 5 |
| 5세대 | BC | 저장소 크기 조정 | 5 |
| 5세대 | BC | GP로 전환 | 3 |

  \* Gen4 하드웨어가 단계적으로 진행 되 고 있으며 새 배포에 더 이상 사용할 수 없습니다. Gen4에서 Gen5로 하드웨어 생성을 업데이트 하 여 Gen5 하드웨어 생성과 관련 된 기능을 활용 합니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [AZURE SQL Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- [SQL Managed Instance의 연결 아키텍처](connectivity-architecture-overview.md)에 대해 자세히 알아보세요.
- [SQL Managed Instance를 배포할 VNet을 만드는](virtual-network-subnet-create-arm-template.md)방법을 참조 하세요.
- DNS 문제는 [사용자 지정 Dns 구성](custom-dns-configure.md)을 참조 하세요.
