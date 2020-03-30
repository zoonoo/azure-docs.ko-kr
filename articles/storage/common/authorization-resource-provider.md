---
title: Azure 저장소 리소스 공급자를 사용하여 관리 리소스에 액세스
description: Azure 저장소 리소스 공급자는 Azure 저장소에 대 한 관리 리소스에 대 한 액세스를 제공 하는 서비스입니다. Azure Storage 리소스 공급자를 사용하여 저장소 계정, 개인 끝점 및 계정 액세스 키와 같은 리소스를 생성, 업데이트, 관리 및 삭제할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5d42a6a0567d3949bc4b0fb1947450a9c957f18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972356"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Azure 저장소 리소스 공급자를 사용하여 관리 리소스에 액세스

Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 저장소 리소스 공급자는 Azure 리소스 관리자를 기반으로 하며 Azure 저장소에 대 한 관리 리소스에 대 한 액세스를 제공 하는 서비스입니다. Azure Storage 리소스 공급자를 사용하여 저장소 계정, 개인 끝점 및 계정 액세스 키와 같은 리소스를 생성, 업데이트, 관리 및 삭제할 수 있습니다. Azure 리소스 관리자에 대한 자세한 내용은 [Azure 리소스 관리자 개요를](/azure/azure-resource-manager/resource-group-overview)참조하십시오.

Azure Storage 리소스 공급자를 사용하여 저장소 계정을 만들거나 삭제하거나 구독에서 저장소 계정 목록을 가져오는 등의 작업을 수행할 수 있습니다. Azure 저장소 리소스 공급자에 대한 요청을 승인하려면 Azure Active Directory(Azure AD)를 사용합니다. 이 문서에서는 관리 리소스에 사용 권한을 할당하는 방법과 Azure Storage 리소스 공급자에 대해 요청하는 방법을 보여 주는 예제를 가리킵니다.

## <a name="management-resources-versus-data-resources"></a>관리 리소스 와 데이터 리소스 비교

Microsoft는 Azure 저장소 리소스로 작업하기 위한 두 개의 REST API를 제공합니다. 이러한 API는 Azure Storage에 대해 수행할 수 있는 모든 작업의 기초를 형성합니다. Azure Storage REST API를 사용하면 Blob, 큐, 파일 및 테이블 데이터를 비롯한 저장소 계정의 데이터로 작업할 수 있습니다. Azure 저장소 리소스 공급자 REST API를 사용하면 저장소 계정 및 관련 리소스로 작업할 수 있습니다.

Blob 데이터를 읽거나 쓰는 요청에는 관리 작업을 수행하는 요청과 다른 권한이 필요합니다. RBAC는 두 가지 유형의 리소스에 대한 사용 권한을 세분화하여 제어합니다. 보안 주체에 RBAC 역할을 할당할 때 해당 보안 주체가 부여할 권한을 이해해야 합니다. 각 기본 제공 RBAC 역할과 연관된 작업을 설명하는 자세한 참조는 [Azure 리소스에 대한 기본 제공 역할을](../../role-based-access-control/built-in-roles.md)참조하십시오.

Azure 저장소는 Azure AD를 사용하여 Blob 및 Queue 저장소에 대한 요청을 승인합니다. Blob 및 큐 데이터 작업에 대한 RBAC 역할에 대한 자세한 내용은 Active Directory 를 [사용하여 Blob 및 큐에 대한 액세스 권한 부여를](storage-auth-aad.md)참조하세요.

## <a name="assign-management-permissions-with-role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어)를 통해 관리 권한 할당

모든 Azure 구독에는 사용자, 그룹 및 응용 프로그램을 관리하는 연결된 Azure Active Directory가 있습니다. 사용자, 그룹 또는 응용 프로그램은 [Microsoft ID 플랫폼의](/azure/active-directory/develop/)컨텍스트에서 보안 주체라고도 합니다. 역할 기반 액세스 제어(RBAC)를 사용하여 Active Directory에 정의된 보안 보안 주체에 대한 구독의 리소스에 대한 액세스 권한을 부여할 수 있습니다.

보안 주체에 RBAC 역할을 할당할 때 역할에 의해 부여된 사용 권한이 적용되는 범위도 나타냅니다. 관리 작업의 경우 구독, 리소스 그룹 또는 저장소 계정 수준에서 역할을 할당할 수 있습니다. [Azure 포털,](https://portal.azure.com/) [Azure CLI 도구,](../../cli-install-nodejs.md) [PowerShell](/powershell/azureps-cmdlets-docs)또는 [Azure 저장소 리소스 공급자 REST API를](/rest/api/storagerp)사용하여 보안 주체에 RBAC 역할을 할당할 수 있습니다.

RBAC에 대한 자세한 내용은 Azure 리소스 및 [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 관리자 역할에](../../role-based-access-control/rbac-and-directory-admin-roles.md)대한 역할 기반 액세스 [제어(RBAC)란 무엇입니까?](../../role-based-access-control/overview.md)

### <a name="built-in-roles-for-management-operations"></a>관리 운영을 위한 기본 제공 역할

Azure는 관리 작업을 호출하는 데 권한을 부여하는 기본 제공 역할을 제공합니다. Azure Storage는 Azure Storage 리소스 공급자와 함께 사용하도록 특별히 기본 제공 역할을 제공합니다.

저장소 관리 작업을 호출할 수 있는 권한을 부여하는 기본 제공 역할에는 다음 표에 설명된 역할이 포함됩니다.

|    RBAC 역할    |    설명    |    계정 키에 대한 액세스가 포함되어 있습니까?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **소유자** | 모든 저장소 리소스 및 리소스에 대한 액세스를 관리할 수 있습니다.  | 예. 저장소 계정 키를 보고 다시 생성할 수 있는 권한을 제공합니다. |
| **참가자**  | 모든 저장소 리소스를 관리할 수 있지만 리소스에 할당을 관리할 수는 없습니다. | 예. 저장소 계정 키를 보고 다시 생성할 수 있는 권한을 제공합니다. |
| **읽기 권한자** | 저장소 계정에 대한 정보를 볼 수 있지만 계정 키를 볼 수는 없습니다. | 아니요. |
| **Storage 계정 기여자** | 저장소 계정을 관리하고, 구독의 리소스 그룹 및 리소스에 대한 정보를 얻고, 구독 리소스 그룹 배포를 만들고 관리할 수 있습니다. | 예. 저장소 계정 키를 보고 다시 생성할 수 있는 권한을 제공합니다. |
| **사용자 액세스 관리자** | 저장소 계정에 대한 액세스를 관리할 수 있습니다.   | 예. 보안 주체가 자신과 다른 사람에게 권한을 할당할 수 있도록 허용합니다. |
| **Virtual Machine 참가자** | 가상 컴퓨터를 관리할 수 있지만 가상 시스템이 연결된 저장소 계정은 관리할 수 없습니다.   | 예. 저장소 계정 키를 보고 다시 생성할 수 있는 권한을 제공합니다. |

테이블의 세 번째 열은 기본 제공 역할이 **Microsoft.Storage/StorageAccounts/list키/작업을**지원하는지 여부를 나타냅니다. 이 작업은 저장소 계정 키를 읽고 다시 생성할 수 있는 권한을 부여합니다. Azure 저장소 관리 리소스에 액세스할 수 있는 권한에는 데이터에 액세스할 수 있는 권한도 포함되지 않습니다. 그러나 사용자가 계정 키에 액세스할 수 있는 경우 공유 키 권한 부여를 통해 계정 키를 사용하여 Azure Storage 데이터에 액세스할 수 있습니다.

### <a name="custom-roles-for-management-operations"></a>관리 운영을 위한 사용자 지정 역할

Azure는 관리 리소스에 대한 액세스를 위해 사용자 지정 RBAC 역할 정의도 지원합니다. 사용자 지정 역할에 대한 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할을](../../role-based-access-control/custom-roles.md)참조합니다.

## <a name="code-samples"></a>코드 샘플

Azure Storage 관리 라이브러리에서 관리 작업을 승인하고 호출하는 방법을 보여 주는 코드 예제는 다음 샘플을 참조하세요.

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure 리소스 관리자 대 클래식 배포

Resource Manager와 클래식 배포 모델은 Azure 솔루션을 배포하고 관리하는 별개의 두 가지 방식을 나타냅니다. 새 저장소 계정을 만들 때 Azure 리소스 관리자 배포 모델을 사용하는 것이 좋습니다. 가능하면 리소스 관리자 모델을 사용하여 기존 클래식 저장소 계정을 다시 만드는 것이 좋습니다. 클래식 배포 모델을 사용하여 저장소 계정을 만들 수 있지만 클래식 모델은 유연성이 낮아지므로 결국 더 이상 사용되지 않습니다.

Azure 배포 모델에 대한 자세한 내용은 [리소스 관리자 및 클래식 배포를](../../azure-resource-manager/management/deployment-models.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스 관리자 개요](/azure/azure-resource-manager/resource-group-overview)
- [Azure 리소스에 대한 RBAC(역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)
- [Azure 저장소 리소스 공급자에 대 한 확장성 대상](scalability-targets-resource-provider.md)
