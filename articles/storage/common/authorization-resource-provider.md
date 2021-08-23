---
title: Azure Storage 리소스 공급자를 사용하여 관리 리소스 액세스
description: Azure Storage 리소스 공급자는 Azure Storage용 관리 리소스에 대한 액세스를 제공하는 서비스입니다. Azure Storage 리소스 공급자를 사용하여 스토리지 계정, 프라이빗 엔드포인트, 계정 액세스 키와 같은 리소스를 생성, 업데이트, 관리, 삭제할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789678"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Azure Storage 리소스 공급자를 사용하여 관리 리소스 액세스

Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure Storage 리소스 공급자는 Azure Resource Manager를 기반으로 하며 Azure Storage용 관리 리소스에 대한 액세스를 제공하는 서비스입니다. Azure Storage 리소스 공급자를 사용하여 스토리지 계정, 프라이빗 엔드포인트, 계정 액세스 키와 같은 리소스를 생성, 업데이트, 관리, 삭제할 수 있습니다. Azure Resource Manager에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.

Azure Storage 리소스 공급자를 사용하여 스토리지 계정 생성 또는 삭제, 구독의 스토리지 계정 목록 가져오기 등의 작업을 수행할 수 있습니다. Azure Storage 리소스 공급자에 대한 요청에 권한을 부여하려면 Azure AD(Azure Active Directory)를 사용합니다. 이 문서에서는 관리 리소스에 권한을 할당하는 방법을 설명하고 Azure Storage 리소스 공급자에 대한 요청을 만드는 방법을 보여 주는 예제 링크를 제공합니다.

## <a name="management-resources-versus-data-resources"></a>관리 리소스와 데이터 리소스 비교

Microsoft는 Azure Storage 리소스로 작업하기 위한 두 가지 REST API를 제공합니다. 두 API는 Azure Storage에 대해 수행할 수 있는 모든 작업의 기초를 형성합니다. Azure Storage REST API를 사용하면 Blob, 큐, 파일, 테이블 데이터를 포함하여 스토리지 계정의 데이터로 작업할 수 있습니다. Azure Storage 리소스 공급자 REST API를 사용하면 스토리지 계정 및 관련 리소스로 작업할 수 있습니다.

Blob 데이터를 읽거나 쓰는 요청에는 관리 작업을 수행하는 요청과는 다른 권한이 필요합니다. Azure RBAC를 통해 두 리소스 종류에 대한 권한을 모두 세밀하게 제어할 수 있습니다. 보안 주체에 Azure 역할을 할당하는 경우 보안 주체에 부여되는 권한을 이해해야 합니다. 각 Azure 기본 제공 역할과 연결된 작업을 설명하는 자세한 참조를 보려면 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

Azure Storage는 Azure AD를 사용하여 Blob 및 큐 스토리지에 대한 요청에 권한을 부여할 수 있도록 지원합니다. Blob과 큐 데이터 작업을 위한 Azure 역할에 대한 자세한 내용은 [Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여](storage-auth-aad.md)를 참조하세요.

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 관리 권한 할당

모든 Azure 구독에는 사용자, 그룹, 애플리케이션을 관리하는 Azure Active Directory가 연결되어 있습니다. [Microsoft ID 플랫폼](../../active-directory/develop/index.yml) 컨텍스트에서는 사용자, 그룹 또는 애플리케이션을 보안 주체라고도 합니다. Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Active Directory에서 정의된 보안 주체에 구독의 리소스에 대한 액세스 권한을 부여할 수 있습니다.

보안 주체에 Azure 역할을 할당하는 경우 역할을 통해 부여된 권한이 적용되는 범위도 지정합니다. 관리 작업의 경우 구독, 리소스 그룹 또는 스토리지 계정 수준에서 역할을 할당할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Azure CLI 도구](/cli/azure/install-classic-cli), [PowerShell](/powershell/azure/) 또는 [Azure Storage 리소스 공급자 REST API](/rest/api/storagerp)를 사용하여 보안 주체에 Azure 역할을 할당할 수 있습니다.

자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)과 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.

### <a name="built-in-roles-for-management-operations"></a>관리 작업을 위한 기본 제공 역할

Azure는 관리 작업을 호출할 수 있는 권한을 부여하는 기본 제공 역할을 제공합니다. Azure Storage는 특히 Azure Storage 리소스 공급자에서 사용하기 위한 기본 제공 역할도 제공합니다.

스토리지 관리 작업을 호출할 수 있는 권한을 부여하는 기본 제공 역할에는 다음 표에 설명된 역할이 포함됩니다.

|    Azure 역할    |    Description    |    계정 키에 대한 액세스 권한 포함 여부    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **소유자** | 모든 스토리지 리소스와 리소스에 대한 액세스를 관리할 수 있습니다.  | 예, 스토리지 계정 키를 확인하고 다시 생성할 수 있는 권한을 제공합니다. |
| **기여자**  | 모든 스토리지 리소스를 관리할 수 있지만 리소스에 대한 액세스는 관리할 수 없습니다. | 예, 스토리지 계정 키를 확인하고 다시 생성할 수 있는 권한을 제공합니다. |
| **판독기** | 스토리지 계정에 대한 정보를 볼 수 있지만 계정 키는 볼 수 없습니다. | 아니요. |
| **Storage 계정 기여자** | 스토리지 계정을 관리하고, 구독의 리소스 그룹 및 리소스에 대한 정보를 가져오고, 구독 리소스 그룹 배포를 만들고 관리할 수 있습니다. | 예, 스토리지 계정 키를 확인하고 다시 생성할 수 있는 권한을 제공합니다. |
| **사용자 액세스 관리자** | 스토리지 계정에 대한 액세스를 관리할 수 있습니다.   | 예, 보안 주체가 자신과 다른 사용자에게 모든 권한을 할당할 수 있도록 허용합니다. |
| **Virtual Machine 참가자** | 가상 머신을 관리할 수 있지만 가상 머신이 연결된 스토리지 계정은 관리할 수 없습니다.   | 예, 스토리지 계정 키를 확인하고 다시 생성할 수 있는 권한을 제공합니다. |

표의 세 번째 열은 기본 제공 역할이 **Microsoft.Storage/storageAccounts/listkeys/action** 을 지원하는지 여부를 나타냅니다. 이 작업은 스토리지 계정 키를 읽고 다시 생성할 수 있는 권한을 부여합니다. Azure Storage 관리 리소스에 대한 액세스 권한에 데이터 액세스 권한도 포함되는 것은 아닙니다. 그러나 사용자에게 계정 키에 대한 액세스 권한이 있는 경우 계정 키를 사용하여 공유 키 권한 부여를 통해 Azure Storage 데이터에 액세스할 수 있습니다.

### <a name="custom-roles-for-management-operations"></a>관리 작업을 위한 사용자 지정 역할

Azure는 관리 리소스에 액세스하기 위한 Azure 사용자 지정 역할 정의도 지원합니다. 사용자 지정 역할에 대한 자세한 내용은 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요.

## <a name="code-samples"></a>코드 샘플

Azure Storage 관리 라이브러리의 관리 작업에 권한을 부여하고 호출하는 방법을 보여 주는 코드 예제는 다음 샘플을 참조하세요.

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.JS](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager와 클래식 배포 비교

Resource Manager와 클래식 배포 모델은 Azure 솔루션을 배포하고 관리하는 별개의 두 가지 방식을 나타냅니다. 새 스토리지 계정을 만드는 경우 Azure Resource Manager 배포 모델을 사용하는 것이 좋습니다. 또한 가능한 경우 Resource Manager 모델을 사용하여 기존 클래식 스토리지 계정을 다시 만드는 것이 좋습니다. 클래식 배포 모델을 사용하여 스토리지 계정을 만들 수도 있지만 클래식 모델은 유연성이 떨어지고 결국 사용이 중단될 것입니다.

Azure 배포 모델에 대한 자세한 내용은 [Resource Manager 및 클래식 배포](../../azure-resource-manager/management/deployment-models.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)
- [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)
- [Azure Storage 리소스 공급자의 스케일링 수준 목표](scalability-targets-resource-provider.md)