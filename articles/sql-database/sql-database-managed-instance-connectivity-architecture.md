---
title: Azure SQL Database Managed Instance 연결 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database Managed Instance 통신 개요를 제공하고, 다양한 구성 요소가 트래픽을 Managed Instance로 전송하기 위해 작동하는 방식과 연결 아키텍처에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 08/16/2018
ms.openlocfilehash: 312425d3ea02d15a992b9a694f09cb2be73b6221
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161593"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Azure SQL Database Managed Instance 연결 아키텍처 

이 문서에서는 Azure SQL Database Managed Instance 통신 개요를 제공하고, 다양한 구성 요소가 트래픽을 Managed Instance로 전송하기 위해 작동하는 방식과 연결 아키텍처에 대해 설명합니다.  

## <a name="communication-overview"></a>통신 개요 

다음 다이어그램은 Managed Instance가 제대로 작동하기 위해 연결되어야 하는 리소스 뿐만 아니라 Managed Instance에 연결되는 엔터티를 보여 줍니다. 

![연결 아키텍처 엔터티](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

다이어그램 맨 아래에 표시되는 통신은 데이터 소스로서 Managed Instance에 연결되는 고객 응용 프로그램 및 도구를 나타냅니다.  

Managed Instance는 PaaS(Platform-As-A-Services)로 제공되므로 Microsoft는 원격 분석 데이트 스트림을 기준으로 자동화 에이전트(관리, 배포 및 유지 관리)를 사용하여 이 서비스를 관리합니다. Managed Instance 관리는 전적으로 Microsoft의 책임이므로 고객은 RDP를 통해 관리되는 인스턴스 가상 클러스터 컴퓨터에 액세스할 수 없습니다. 

최종 사용자 또는 응용 프로그램이 시작하는 일부 SQL Server 작업을 위해서는 Managed Instance가 플랫폼과 상호 작용해야 할 수 있습니다. 하나는 포털, PowerShell 및 Azure CLI를 통해 노출되는 리소스인 Managed Instance 데이터베이스를 만드는 경우입니다. 

Managed Instance는 적절한 작동을 위해 다른 Azure 서비스에 의존하며(예: 백업을 위해 Azure Storage, 원격 분석을 위해 Azure Service Bus, 인증을 위해 Azure AD, TDE를 위해 Azure Key Vault 등) 그에 따라 해당 서비스에 대한 연결을 시작합니다. 

위에 언급된 모든 통신은 암호화되고 인증서를 사용하여 서명됩니다. 통신 당사자가 신뢰될 수 있도록 하기 위해 Managed Instance는 인증 기관에 연락하여 이러한 인증서를 지속적으로 확인합니다. 인증서가 해지되거나 Managed Instance에서 해당 인증서를 확인할 수 없으면 데이터 보호를 위해 연결을 닫습니다. 

## <a name="high-level-connectivity-architecture"></a>고급 연결 아키텍처 

높은 수준에서 보면 Managed Instance는 고객 가상 네트워크 서브넷 내에서 실행되고 가상 클러스터를 형성하는 격리된 전용 가상 머신 집합에서 호스트되는 서비스 구성 요소 집합입니다. 

여러 Managed Instance가 단일 가상 클러스터에 호스트될 수 있습니다. 클러스터는 고객이 서브넷에서 프로비전된 인스턴스 수를 변경하면 필요에 따라 자동으로 확장 또는 축소됩니다. 

고객 응용 프로그램은 개인 IP 주소를 갖는 엔드포인트를 사용하여 가상 네트워크 또는 피어링된 가상 네트워크나 VPN/Express Route 연결 네트워크 내에서 실행되는 경우에만 Managed Instance에 연결하고 데이터베이스를 쿼리 및 업데이트할 수 있습니다.  

![연결 아키텍처 다이어그램](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft 관리 및 배포 서비스는 Managed Instance과 Microsoft 서비스 간의 연결이 공용 IP 주소를 갖는 엔드포인트를 통과하도록 가상 네트워크 외부에서 실행됩니다. Managed Instance가 아웃바운드 연결을 만들 때, NAT(Network Address Translation)로 인해 수신 끝에서는 연결이 이 공용 IP에서 시작되는 것처럼 보입니다. 

관리 트래픽은 고객 가상 네트워크를 통해 흐릅니다. 즉, 가상 네트워크 인프라의 요소가 영향을 미치며, 관리 트래픽을 손상시켜 인스턴스를 오류 상태로 만들고 사용할 수 없게 할 수 있습니다. 

> [!IMPORTANT]
> Microsoft는 고객 환경 및 서비스 가용성을 향상시키기 위해 Managed Instance 작동에 영향을 줄 수 있는 Azure Virtual Network 인프라 요소에 네트워크 의도 정책을 적용합니다. 이것은 네트워크 구성 요소를 방지하고 정상적인 Managed Instance 작동을 보장하는 것을 주 목적으로 하는 플랫폼 메커니즘으로, 네트워크 요구 사항을 최종 사용자에게 투명하게 전달합니다. Managed Instance 삭제 시, 네트워크 의도 정책도 제거됩니다. 

## <a name="virtual-cluster-connectivity-architecture"></a>가상 클러스터 연결 아키텍처 

Managed Instance 연결 아키텍처에 대해 좀 더 자세히 살펴보겠습니다. 다음 다이어그램은 가상 클러스터의 개념적 레이아웃을 보여 줍니다. 

![연결 아키텍처 다이어그램 가상 클러스터](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

클라이언트는 <mi_name>.<clusterid>.database.windows.net 형식을 갖는 호스트 이름을 사용하여 Managed Instance에 연결합니다. 이 호스트 이름은 공용 DNS 영역에 등록되고 공개적으로 확인 가능하더라도 개인 IP 주소로 확인됩니다. 

이 개인 IP 주소는 트래픽을 Managed Instance GW(게이트웨이)로 전송하는 Managed Instance ILB(내부 Load Balancer)에 속합니다. 여러 Managed Instance가 동일한 클러스터 내에서 실행될 수 있으므로 GW는 Managed Instance 호스트 이름을 사용하여 트래픽을 올바른 SQL 엔진 서비스로 리디렉션합니다. 

관리 및 배포 서비스는 외부 Load Balancer에 매핑되는 공용 엔드포인트를 사용하여 Managed Instance에 연결됩니다. 트래픽은 Managed Instance 관리 구성 요소가 배타적으로 사용하는 미리 정의된 포트 집합에서 수신될 때만 노드로 라우팅됩니다. 관리 구성 요소와 관리 평면 간의 모든 통신은 인증서로 상호 인증됩니다. 

## <a name="next-steps"></a>다음 단계 

- 개요는  [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요. 
- VNet 구성에 대한 자세한 내용은  [Managed Instance VNet 구성](sql-database-managed-instance-vnet-configuration.md)을 참조하세요. 
- 빠른 시작에 대해서는 Managed Instance 생성 방법을 참조하세요. 
  - [Azure Portal](sql-database-managed-instance-get-started.md)에서 
  - [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) 사용 
  - [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/) 사용 
  - [Azure Resource Manager 템플릿(SSMS가 포함된 점프박스)](https://portal.azure.com/) 

