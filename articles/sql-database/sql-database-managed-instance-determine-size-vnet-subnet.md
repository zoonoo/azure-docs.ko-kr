---
title: Azure SQL Database Managed Instance VNet/서브넷 크기 결정 | Microsoft Docs
description: 이 항목에서는 Azure SQL Database Managed Instance가 배포될 서브넷의 크기를 계산하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 02/22/2019
ms.openlocfilehash: 2a10876bc3c9558de29caf9fee2ae0b06ee87f28
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358925"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance의 VNet 서브넷 크기 결정

Azure SQL Database Managed Instance는 Azure [VNet(가상 네트워크)](../virtual-network/virtual-networks-overview.md) 내에 배포해야 합니다.

VNet의 서브넷에 배포할 수 있는 Managed Instance 수는 서브넷의 크기(서브넷 범위)에 따라 달라집니다.

Managed Instance를 만들 때 Azure에서는 프로비전하는 동안 선택한 계층에 따라 가상 머신의 수를 할당합니다. 이러한 가상 머신이 서브넷에 연결되어 있기 때문에 IP 주소가 필요합니다. 일반 작업 및 서비스 유지 관리 중에 고가용성을 보장하기 위해 Azure에서는 가상 머신을 추가로 할당할 수 있습니다. 결과적으로 서브넷의 필수 IP 주소 수는 해당 서브넷의 Managed Instance 수보다 많습니다.

기본적으로 Managed Instance는 서브넷에서 최소 16개의 IP 주소가 필요하고 최대 256개의 IP 주소를 사용할 수 있습니다. 결과적으로 서브넷 IP 범위를 정의할 때/28 사이의/24 서브넷 마스크를 사용할 수 있습니다. / 28 (네트워크 당 14 호스트)의 네트워크 마스크 비트는 하나의 일반 목적 또는 비즈니스에 중요 한 배포에 대 한 적절 한 크기입니다. / 27 (네트워크 당 30 호스트)의 마스크 비트를 동일한 VNet 내에 여러 관리 되는 인스턴스 배포에 적합합니다. / 26 (62 호스트) 및/24 (254 명의 호스트)의 마스크 비트 설정을 추가 관리 되는 인스턴스를 지원 하도록 VNet을 추가로 확장할 수 있습니다.

> [!IMPORTANT]
> 서브넷 크기를 16 IP 주소를 사용 하 여 최소한의 추가 관리 되는 인스턴스 규모 확장에 대 한 제한 된 가능성이 있는 경우 접두사 /27 이하가 지정된 서브넷을 선택하는 것이 좋습니다.

## <a name="determine-subnet-size"></a>서브넷 크기 결정

서브넷 내에서 관리되는 여러 인스턴스를 배포하고 서브넷 크기를 최적화해야 하는 경우 다음과 같은 매개 변수를 사용하여 계산을 만듭니다.

- Azure는 고유한 요구 사항으로 인해 서브넷에서 5개의 IP 주소를 사용합니다.
- 범용 인스턴스마다 두 개의 주소가 필요합니다.
- 각 중요 비즈니스용 인스턴스에는 4개의 주소가 필요합니다.

**예제**: 세 개의 범용 및 두 개의 중요 비즈니스용 Managed Instance를 포함하도록 계획합니다. 즉, 5 + 3 * 2 + 2 * 4 = 19개 IP 주소가 필요합니다. IP 범위가 2의 거듭제곱으로 정의되므로 32개(2^5)의 IP 주소라는 IP 범위가 필요합니다. 따라서 /27 서브넷 마스크를 포함하는 서브넷을 예약해야 합니다.

> [!IMPORTANT]
> 위에 표시된 계산은 추가적인 개선의 등장으로 사용하지 않게 됩니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- [Managed Instance의 연결 아키텍처](sql-database-managed-instance-connectivity-architecture.md)에 대해 자세히 알아봅니다.
- [Managed Instance를 배포할 VNet을 만드는](sql-database-managed-instance-create-vnet-subnet.md) 방법을 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](sql-database-managed-instance-custom-dns.md)을 참조하세요.
