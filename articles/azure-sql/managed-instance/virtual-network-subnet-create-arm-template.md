---
title: 가상 네트워크 만들기
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance 배포를 지원 하도록 구성 된 가상 네트워크를 만드는 방법을 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: d6458794b8d3c5ba68109dbc90a075d6a2a577b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711326"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 가상 네트워크 만들기
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance를 배포할 수 있는 유효한 가상 네트워크 및 서브넷을 만드는 방법을 설명 합니다.

Azure SQL Managed Instance는 Azure [virtual network](../../virtual-network/virtual-networks-overview.md)내에 배포 해야 합니다. 이 배포를 사용하면 다음과 같은 시나리오를 수행할 수 있습니다.

- 개인 IP 주소 보호
- 온-프레미스 네트워크에서 직접 SQL Managed Instance에 연결
- 연결 된 서버 또는 다른 온-프레미스 데이터 저장소에 SQL Managed Instance 연결
- SQL Managed Instance를 Azure 리소스에 연결  

> [!NOTE]
> 첫 번째 인스턴스를 배포 하기 전에 [SQL Managed Instance의 서브넷 크기를 결정](vnet-subnet-determine-size.md) 해야 합니다. 리소스를 추가한 후에는 서브넷 크기를 조정할 수 없습니다.
>
> 기존 가상 네트워크를 사용 하려는 경우 SQL Managed Instance에 맞게 해당 네트워크 구성을 수정 해야 합니다. 자세한 내용은 [SQL Managed Instance에 대 한 기존 가상 네트워크 수정](vnet-existing-add-subnet.md)을 참조 하세요.
>
> 관리 되는 인스턴스를 만든 후에는 관리 되는 인스턴스 또는 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.  관리 되는 인스턴스를 다른 서브넷으로 이동 하는 것도 지원 되지 않습니다.
>

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들고 구성하는 가장 쉬운 방법은 Azure Resource Manager 배포 템플릿을 사용하는 것입니다.

1. Azure Portal에 로그인합니다.

2. **Azure에 배포** 단추를 선택합니다.

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   이 단추를 클릭 하면 SQL Managed Instance를 배포할 수 있는 네트워크 환경을 구성 하는 데 사용할 수 있는 양식이 열립니다.

   > [!Note]
   > 이 Azure Resource Manager 템플릿은 두 개의 서브넷이 있는 가상 네트워크를 배포합니다. **Managedinstances**라는 하나의 서브넷은 SQL Managed Instance 예약 되며 미리 구성 된 경로 테이블을 포함 합니다. **기본값**이라고 하는 다른 서브넷은 SQL Managed Instance (예: Azure Virtual Machines)에 액세스 해야 하는 다른 리소스에 사용 됩니다.

3. 네트워크 환경을 구성합니다. 다음 양식에서 네트워크 환경의 매개 변수를 구성할 수 있습니다.

   ![Azure 네트워크를 구성하기 위한 Resource Manager 템플릿](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   가상 네트워크 및 서브넷의 이름을 변경하고, 네트워킹 리소스에 연결된 IP 범위를 조정할 수 있습니다. **구매** 단추를 선택하면 이 양식이 생성되어 환경을 구성합니다. 두 개의 서브넷 필요하지 않은 경우에는 기본 서브넷을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [SQL Managed Instance 이란?](sql-managed-instance-paas-overview.md)을 참조 하세요.
- [SQL Managed Instance의 연결 아키텍처](connectivity-architecture-overview.md)에 대해 알아봅니다.
- [SQL Managed Instance에 대 한 기존 가상 네트워크를 수정](vnet-existing-add-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크를 만들고, 관리 되는 인스턴스를 만들고, 데이터베이스 백업에서 데이터베이스를 복원 하는 방법을 보여 주는 자습서는 [관리 되는 인스턴스 만들기](instance-create-quickstart.md)를 참조 하세요.
- DNS 문제는 [사용자 지정 Dns 구성](custom-dns-configure.md)을 참조 하세요.
