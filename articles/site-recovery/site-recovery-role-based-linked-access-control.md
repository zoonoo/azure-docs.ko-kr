---
title: "역할 기반 액세스 제어를 사용하여 Azure Site Recovery 관리 | Microsoft Docs"
description: "이 문서에서는 RBAC(역할 기반 액세스 제어)를 적용 및 사용하여 Azure Site Recovery 배포를 관리하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 08a4d0fa673a37c61e57daed66ab6768e0276ca8
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017


---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>역할 기반 액세스 제어를 사용하여 Azure Site Recovery 관리

Azure RBAC(역할 기반 액세스 제어)를 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하여 팀 내 책임을 구분하고, 특정 작업을 수행하는 데 필요한 사용자에게 특정 액세스 권한만 부여할 수 있습니다.

Azure는 리소스 관리 작업을 제어하는 다양한 기본 제공 역할을 제공합니다. [Azure RBAC 기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)에 대해 알아보기

더 많은 제어를 위해 사용자 고유의 역할을 정의하려는 경우 Azure의 [사용자 지정 역할 작성](../active-directory/role-based-access-control-custom-roles.md) 방법을 참조하세요.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>새 가상 컴퓨터에 대한 복제를 사용하도록 설정하는 데 필요한 사용 권한
Azure Site Recovery를 사용하여 새 가상 컴퓨터를 Azure에 복제할 때 연결된 사용자의 액세스 수준이 유효한지 확인하여 사용자에게 Site Recovery에 제공되는 Azure 리소스를 사용하는 데 필요한 권한이 있는지 검토됩니다.

새 가상 컴퓨터에 대한 복제를 사용하도록 설정하려면 사용자에게 다음 권한이 있어야 합니다.
* 선택한 리소스 그룹에서 가상 컴퓨터를 만들 수 있는 권한
* 선택한 가상 네트워크에서 가상 컴퓨터를 만들 수 있는 권한
* 선택한 저장소 계정에 대한 쓰기 권한

사용자는 새 가상 컴퓨터의 복제를 완료하려면 다음 권한이 필요합니다.

> [!IMPORTANT]
>리소스 배포에 사용되는 배포 모델(Resource Manager/클래식)마다 적절한 권한이 추가되는지 확인합니다.

| **리소스 종류** | **배포 모델** | **사용 권한** |
| --- | --- | --- |
| Compute | 리소스 관리자 | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | 클래식 | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| 네트워크 | 리소스 관리자 | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | 클래식 | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| 저장소 | 리소스 관리자 | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | 클래식 | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| 리소스 그룹 | 리소스 관리자 | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Resource Manager 및 클래식 배포 모델에 대해 각각 '가상 컴퓨터 참여자' 및 '클래식 가상 컴퓨터 참여자' [기본 제공 역할](../active-directory/role-based-access-built-in-roles.md)을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [역할 기반 액세스 제어](../active-directory/role-based-access-control-configure.md): Azure 포털에서 RBAC를 통해 시작합니다.
* 다음을 사용하여 액세스를 관리하는 방법에 대해 알아봅니다.
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [역할 기반 액세스 제어 문제 해결](../active-directory/role-based-access-control-troubleshooting.md): 일반적인 문제를 수정하기 위한 제안 사항을 봅니다.

