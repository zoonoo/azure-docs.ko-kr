---
title: Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스
description: Azure 리소스 및 Azure AD 인증에 대해 관리 ID를 지원하는 서비스 목록
services: active-directory
author: MarkusVi
ms.author: priyamo
ms.date: 04/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79c3f8ae183c78b19f7f520f8154bf9484a1f795
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60289789"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 서비스

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드에 자격 증명이 없어도 Azure AD 인증을 지원하는 모든 서비스를 인증할 수 있습니다. 현재 Azure 전반에 걸쳐 Azure 리소스와 Azure AD 인증에 대한 관리 ID를 통합하는 과정이 진행되고 있습니다. 업데이트를 자주 확인해 주세요.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스

다음 Azure 서비스는 Azure 리소스에 대한 관리 ID를 지원합니다.

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 미리 보기 | 미리 보기 | 미리 보기 | 
| 사용자 할당 | 미리 보기 | 미리 보기 | 미리 보기 | 미리 보기 |

사용 가능한 지역에서 Azure Virtual Machines용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 미리 보기 | 미리 보기 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 미리 보기 | 미리 보기 |

사용 가능한 지역에서 Azure Virtual Machines Scale Sets용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| 사용자 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure App Service용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 템플릿](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 항목 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 항목 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 하 여 관리 되는 id를 사용 하려면 다음 목록을 참조 하십시오 [Azure 청사진](../../governance/blueprints/overview.md):

- [Azure portal-blueprint 할당](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API-blueprint 할당](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure 기능

관리 ID 유형 |모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| 사용자 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 템플릿](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 항목 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |
| 사용자 할당 항목 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager 템플릿](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Data Factory V2용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST (영문)](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용 가능 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure API Management용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Resource Manager 템플릿](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Container Instances용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager 템플릿](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, Azure 리소스에 대해 관리 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

### <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager로 액세스를 구성 하려면 다음 목록을 참조 하세요.

- [Azure portal 통해 액세스를 할당 합니다.](howto-assign-access-portal.md)
- [Powershell 통해 액세스 할당](howto-assign-access-powershell.md)
- [Azure CLI를 통해 액세스 할당](howto-assign-access-CLI.md)
- [Azure Resource Manager 템플릿을 통해 액세스 할당](../../role-based-access-control/role-assignments-template.md)

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|--------|
| Azure 글로벌 | `https://management.azure.com/`| 사용 가능 |
| Azure Government | `https://management.usgovcloudapi.net/` | 사용 가능 |
| Azure Germany | `https://management.microsoftazure.de/` | 사용 가능 |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | 사용 가능 |

### <a name="azure-key-vault"></a>Azure Key Vault

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|--------|
| Azure 글로벌 | `https://vault.azure.net`| 사용 가능 |
| Azure Government | `https://vault.usgovcloudapi.net` | 사용 가능 |
| Azure Germany |  `https://vault.microsoftazure.de` | 사용 가능 |
| Azure China 21Vianet | `https://vault.azure.cn` | 사용 가능 |

### <a name="azure-data-lake"></a>Azure Data Lake 

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|--------|
| Azure 글로벌 | `https://datalake.azure.net/` | 사용 가능 |
| Azure Government |  | 사용할 수 없음 |
| Azure Germany |   | 사용할 수 없음 |
| Azure China 21Vianet |  | 사용할 수 없음 |

### <a name="azure-sql"></a>Azure SQL 

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|--------|
| Azure 글로벌 | `https://database.windows.net/` | 사용 가능 |
| Azure Government | `https://database.usgovcloudapi.net/` | 사용 가능 |
| Azure Germany | `https://database.cloudapi.de/` | 사용 가능 |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | 사용 가능 |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|--------|
| Azure 글로벌 | `https://eventhubs.azure.net` | 미리 보기 |
| Azure Government |  | 사용할 수 없음 |
| Azure Germany |   | 사용할 수 없음 |
| Azure China 21Vianet |  | 사용할 수 없음 |

### <a name="azure-service-bus"></a>Azure Service Bus

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|--------|
| Azure 글로벌 | `https://servicebus.azure.net`  | 미리 보기 |
| Azure Government |  | 사용할 수 없음 |
| Azure Germany |   | 사용할 수 없음 |
| Azure China 21Vianet |  | 사용할 수 없음 |

### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blob 및 큐

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|--------|
| Azure 글로벌 | `https://storage.azure.com/` | 사용 가능 |
| Azure Government | `https://storage.azure.com/` | 사용 가능 |
| Azure Germany | `https://storage.azure.com/` | 사용 가능 |
| Azure China 21Vianet | `https://storage.azure.com/` | 사용 가능 |
