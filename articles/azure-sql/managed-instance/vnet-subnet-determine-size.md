---
title: 필요한 서브넷 크기 및 범위 확인
titleSuffix: Azure SQL Managed Instance
description: 이 토픽에서는 Azure SQL 관리형 인스턴스가 배포될 서브넷의 크기를 계산하는 방법을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 18592f282cb2f06b5a305f2186aa6285bc50fcf3
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689344"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대한 필수 서브넷 크기 및 범위 확인
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 Azure [VNet(가상 네트워크)](../../virtual-network/virtual-networks-overview.md) 내에 배포해야 합니다.

VNet의 서브넷에 배포할 수 있는 관리형 인스턴스 수는 서브넷의 크기(서브넷 범위)에 따라 달라집니다.

관리형 인스턴스를 만들 때 Azure에서는 프로비저닝하는 동안 선택한 계층에 따라 가상 머신의 수를 할당합니다. 이러한 가상 머신이 서브넷에 연결되어 있기 때문에 IP 주소가 필요합니다. 일반 작업 및 서비스 유지 관리 중에 고가용성을 보장하기 위해 Azure에서는 가상 머신을 추가로 할당할 수 있습니다. 결과적으로 서브넷의 필수 IP 주소 수는 해당 서브넷의 관리형 인스턴스 수보다 많습니다.

기본적으로 Managed Instance의 서브넷에는 최소 32개의 IP 주소가 필요합니다. 따라서 서브넷 IP 범위를 정의할 때 최소 서브넷 마스크 /27을 사용할 수 있습니다. Managed Instance 배포에 대한 서브넷 크기를 신중하게 계획하는 것이 좋습니다. 계획할 때 고려해야 할 입력 사항은 다음과 같습니다.

- 다음 인스턴스 매개 변수를 포함하여 관리형 인스턴스의 수
  - 서비스 계층
  - 하드웨어 세대
  - vCore 수
- 스케일 업/다운 또는 서비스 계층 변경 계획

> [!IMPORTANT]
> IP 주소가 16개인 서브넷 크기(서브넷 마스크/28)에서는 관리형 인스턴스를 배포할 수 있지만 이러한 서브넷 크기는 평가 또는 개발/테스트 시나리오(스케일링 작업이 수행되지 않음)에 사용되는 단일 인스턴스를 배포하는 데에만 사용해야 합니다.

## <a name="determine-subnet-size"></a>서브넷 크기 결정

향후 인스턴스 배포 및 스케일링 필요에 따라 서브넷 크기를 조정합니다. 다음 매개 변수는 계산식을 만드는 데 도움이 될 수 있습니다.

- Azure는 고유한 요구 사항으로 인해 서브넷에서 5개의 IP 주소를 사용합니다.
- 각 가상 클러스터는 주소 수를 추가로 할당합니다. 
- 각 관리형 인스턴스는 가격 책정 계층 및 하드웨어 생성에 따라 주소 수를 사용합니다.

> [!IMPORTANT]
> 서브넷에 리소스가 있으면 서브넷 주소 범위를 변경할 수 없습니다. 또한 Managed Instance를 한 서브넷에서 다른 서브넷으로 이동할 수도 없습니다. 가능하면 나중에 문제가 발생하지 않도록 더 큰 서브넷을 사용하는 것이 좋습니다.

GP = 범용; BC = 중요 비즈니스용; VC = 가상 클러스터

| **하드웨어 생성** | **가격 책정 계층** | **Azure 사용량** | **VC 사용량** | **인스턴스 사용량** | **총계*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| 5세대 | GP | 5 | 6 | 3 | 14 |
| 5세대 | BC | 5 | 6 | 5 | 16 |

  \* 열 합계는 인스턴스 한 개가 서브넷에 배포될 때 취할 수 있는 주소 수를 표시합니다. 서브넷의 각 추가 인스턴스는 인스턴스 사용량 열로 표시되는 주소 수를 추가합니다. Azure 사용량 열로 표시되는 주소는 여러 가상 클러스터에서 공유되지만 VC 사용량 열로 표시되는 주소는 해당 가상 클러스터에 배치된 인스턴스에서 공유됩니다.

일반적으로 업데이트 작업에는 가상 클러스터 크기 조정이 필요합니다. 업데이트 작업을 수행하려면 가상 클러스터를 만들어야 하는 경우도 있습니다. 자세한 내용은 [관리 작업 문서](sql-managed-instance-paas-overview.md#management-operations)를 참조하세요. 가상 클러스터를 만들 경우 필요한 추가 주소 수는 VC 사용량 열로 표시되는 주소 수에 해당 가상 클러스터(인스턴스 사용량 열)에 배치된 인스턴스에 필요한 주소 수를 합한 수와 같습니다.

**예 1**: 범용 관리형 인스턴스(Gen4 하드웨어) 한 개와 중요 비즈니스용 관리형 인스턴스(Gen5 하드웨어)를 사용할 계획입니다. 이는 배포하는 데 필요한 IP 주소 수가 최소 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22개임을 의미합니다. IP 범위가 2의 제곱으로 정의되므로 이 배포를 수행하려면 서브넷에 최소 32(2^5)의 IP 범위가 필요합니다.<br><br>
위에서 언급한 것처럼 업데이트 작업을 수행하려면 가상 클러스터를 만들어야 하는 경우가 있습니다. 이는 예를 들어 가상 클러스터를 만들어야 하는 Gen5 중요 비즈니스용 관리형 인스턴스로 업데이트하려면 6 + 5 = 11개의 IP 주소를 추가로 사용할 수 있어야 함을 의미합니다. This means that, as an example, in case of an update to the Gen5 business critical managed instance that requires a virtual cluster creation, you will need to have additional 6 + 5 = 11 IP addresses available. 32개의 주소 중 이미 22개를 사용 중이므로 이 작업에 사용할 수 있는 주소가 없습니다. 따라서 /26(64개 주소) 서브넷 마스크를 포함하는 서브넷을 예약해야 합니다.

**예 2**: 동일한 서브넷에 범용 관리형 인스턴스(Gen5 하드웨어) 3개와 중요 비즈니스용 관리형 인스턴스(Gen5 하드웨어) 2개를 배치할 계획입니다. 이는 5 + 6 + 3 * 3 + 2 * 5 = 30개의 IP 주소가 필요함을 의미합니다. 따라서 /26 서브넷 마스크를 포함하는 서브넷을 예약해야 합니다. /27 서브넷 마스크를 선택하면 나머지 주소 수가 2개(32 – 30)가 되고 이렇게 되면 인스턴스 스케일링을 위한 추가 주소 수가 서브넷에 필요하므로 모든 인스턴스에 업데이트 작업이 수행되지 않습니다.

**예 3**: 범용 관리형 인스턴스(Gen5 하드웨어) 1개를 사용하고 가끔 vCore 업데이트 작업을 수행할 계획입니다. 이는 5 + 6 + 1 * 3 + 3 = 17개의 IP 주소가 필요함을 의미합니다. IP 범위가 2의 거듭제곱으로 정의되므로 32개(2^5)의 IP 주소라는 IP 범위가 필요합니다. 따라서 /27 서브넷 마스크를 포함하는 서브넷을 예약해야 합니다.

### <a name="address-requirements-for-update-scenarios"></a>업데이트 시나리오에 대한 주소 요구 사항

스케일링 작업 중 가격 책정 계층 및 하드웨어 생성에 따라 일시적으로 추가 IP 용량이 필요합니다.

| **하드웨어 생성** | **가격 책정 계층** | **시나리오** | **추가 주소*** |
| --- | --- | --- | --- |
| Gen4 | GP 또는 BC | vCore 스케일링 | 5 |
| Gen4 | GP 또는 BC | 스토리지 스케일링: | 5 |
| Gen4 | GP 또는 BC | GP에서 BC로 또는 BC에서 GP로 전환 | 5 |
| Gen4 | GP | Gen5*로 전환 | 9 |
| Gen4 | BC | Gen5*로 전환 | 11 |
| 5세대 | GP | vCore 스케일링 | 3 |
| 5세대 | GP | 스토리지 스케일링: | 0 |
| 5세대 | GP | BC로 전환 | 5 |
| 5세대 | BC | vCore 스케일링 | 5 |
| 5세대 | BC | 스토리지 스케일링: | 5 |
| 5세대 | BC | GP로 전환 | 3 |

  \* Gen4 하드웨어는 단계적으로 중단되며 더이상 새로운 배포에 사용할 수 없습니다. Gen4에서 Gen5로 하드웨어 생성을 업데이트하면 Gen5 하드웨어 생성과 관련된 기능을 활용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- [SQL 관리형 인스턴스의 연결 아키텍처](connectivity-architecture-overview.md)에 대해 자세히 알아보세요.
- [SQL 관리형 인스턴스를 배포할 VNet을 만드는](virtual-network-subnet-create-arm-template.md) 방법에 대해 알아보세요.
- DNS 문제는 [사용자 지정 DNS 구성](custom-dns-configure.md)을 참조하세요.
