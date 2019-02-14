---
title: Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스
description: Azure 리소스 및 Azure AD 인증에 대해 관리 ID를 지원하는 서비스 목록
services: active-directory
author: priyamohanram
ms.author: priyamo
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa25d2e0fe4700eab69d383d1ec8b57fca86bd4
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201553"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 서비스

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드에 자격 증명이 없어도 Azure AD 인증을 지원하는 모든 서비스를 인증할 수 있습니다. 현재 Azure 전반에 걸쳐 Azure 리소스와 Azure AD 인증에 대한 관리 ID를 통합하는 과정이 진행되고 있습니다. 업데이트를 자주 확인해 주세요.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스

다음 Azure 서비스는 Azure 리소스에 대한 관리 ID를 지원합니다.

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

|관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 미리 보기 | 미리 보기 | 미리 보기 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 미리 보기 | 미리 보기 | 미리 보기

사용 가능한 지역에서 Azure Virtual Machines용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 미리 보기 | 미리 보기 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 미리 보기 | 미리 보기

사용 가능한 지역에서 Azure Virtual Machines Scale Sets용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

|관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| 사용자 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음

사용 가능한 지역에서 Azure App Service용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 템플릿](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-functions"></a>Azure 기능

관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| 사용자 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음

사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager 템플릿](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용 가능 | 사용 가능 | 사용 가능 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음

사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Azure Resource Manager 템플릿](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음

사용 가능한 지역에서 Azure Data Factory V2용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)
- [REST (영문)](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | 사용 가능 | 사용 가능 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음

사용 가능한 지역에서 Azure API Management용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Resource Manager 템플릿](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

관리 ID 유형 |  모든 일반 공급<br>전 세계 Azure 지역 | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| 시스템 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음

사용 가능한 지역에서 Azure Container Instances용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager 템플릿](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, Azure 리소스에 대해 관리 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

| 서비스 | 리소스 ID | 상태 | 액세스 권한 할당 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure 리소스 관리자 | `https://management.azure.com/` | 사용 가능 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) <br>[Azure Resource Manager 템플릿](../../role-based-access-control/role-assignments-template.md) |
| Azure Key Vault | `https://vault.azure.net` | 사용 가능 |  
| Azure Data Lake | `https://datalake.azure.net/` | 사용 가능 |
| Azure SQL | `https://database.windows.net/` | 사용 가능 |
| Azure Event Hubs | `https://eventhubs.azure.net` | 미리 보기 |
| Azure Service Bus | `https://servicebus.azure.net` | 미리 보기 |
| Azure Storage | `https://storage.azure.com/` | 미리 보기 |
