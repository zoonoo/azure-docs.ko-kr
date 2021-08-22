---
title: 가상 네트워크 만들기
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance 배포를 지원하도록 구성된 가상 네트워크를 만드는 방법을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 3993194b484e2d6d2956f9d995a9b977fcf5a6e4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529136"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 가상 네트워크 만들기
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance를 배포할 수 있는 유효한 가상 네트워크 및 서브넷을 만드는 방법을 설명합니다.

Azure SQL Managed Instance는 Azure [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 내에 배포해야 합니다. 이 배포를 사용하면 다음과 같은 시나리오를 수행할 수 있습니다.

- 개인 IP 주소 보호
- 온-프레미스 네트워크에서 직접 SQL Managed Instance에 연결
- SQL Managed Instance를 연결된 서버 또는 다른 온-프레미스 데이터 저장소에 연결
- SQL Managed Instance를 Azure 리소스에 연결  

> [!NOTE]
> 첫 번째 인스턴스를 배포하기 전에 [SQL Managed Instance에 대한 서브넷의 크기를 결정](vnet-subnet-determine-size.md)해야 합니다. 리소스를 추가한 후에는 서브넷 크기를 조정할 수 없습니다.
>
> 기존 가상 네트워크를 사용하려는 경우 SQL Managed Instance를 수용하도록 해당 네트워크 구성을 수정해야 합니다. 자세한 내용은 [SQL Managed Instance의 기존 가상 네트워크 수정](vnet-existing-add-subnet.md)을 참조하세요.
>
> 관리형 인스턴스를 만든 후에 관리형 인스턴스 또는 가상 네트워크를 다른 리소스 그룹이나 구독으로 이동하는 기능은 지원되지 않습니다.  Managed Instance를 다른 서브넷으로 이동하는 기능도 지원되지 않습니다.
>

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들고 구성하는 가장 쉬운 방법은 Azure Resource Manager 배포 템플릿을 사용하는 것입니다.

1. Azure Portal에 로그인합니다.

2. **Azure에 배포** 단추를 선택합니다.

   [!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.sql%2Fsql-managed-instance-azure-environment%2Fazuredeploy.json)

   이 단추를 사용하면 네트워크 환경을 구성하는 데 사용할 수 있는 양식이 열리는데, 여기서 SQL Managed Instance를 배포할 수 있습니다.

   > [!Note]
   > 이 Azure Resource Manager 템플릿은 두 개의 서브넷이 있는 가상 네트워크를 배포합니다. **ManagedInstances** 라는 하나의 서브넷이 SQL Managed Instance용으로 예약되고 미리 구성된 경로 테이블을 포함합니다. **Default** 라는 다른 서브넷은 SQL Managed Instance에 액세스해야 하는 다른 리소스(예: Azure Virtual Machines)에 사용됩니다.

3. 네트워크 환경을 구성합니다. 다음 양식에서 네트워크 환경의 매개 변수를 구성할 수 있습니다.

   ![Azure 네트워크를 구성하기 위한 Resource Manager 템플릿](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   가상 네트워크 및 서브넷의 이름을 변경하고, 네트워킹 리소스에 연결된 IP 범위를 조정할 수 있습니다. **구매** 단추를 선택하면 이 양식이 생성되어 환경을 구성합니다. 두 개의 서브넷 필요하지 않은 경우에는 기본 서브넷을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 개요는 [SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- [SQL Managed Instance의 연결 아키텍처](connectivity-architecture-overview.md)에 대해 알아봅니다.
- [SQL Managed Instance의 기존 가상 네트워크를 수정](vnet-existing-add-subnet.md)하는 방법을 알아봅니다.
- 가상 네트워크와 관리형 인스턴스를 만들고 데이터베이스 백업에서 데이터베이스를 복원하는 방법을 보여 주는 자습서는 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- DNS 문제는 [사용자 지정 DNS 구성](custom-dns-configure.md)을 참조하세요.
