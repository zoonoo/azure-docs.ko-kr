---
title: Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스
description: Azure 리소스 및 Azure AD 인증에 대해 관리 ID를 지원하는 서비스 목록
services: active-directory
author: daveba
ms.author: daveba
ms.date: 10/23/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 25da856d8bd72f4e74718cf32f62bbb0a3eb5b88
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984692"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 서비스

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드에 자격 증명이 없어도 Azure AD 인증을 지원하는 모든 서비스를 인증할 수 있습니다. 현재 Azure 전반에 걸쳐 Azure 리소스와 Azure AD 인증에 대한 관리 ID를 통합하는 과정이 진행되고 있습니다. 업데이트를 자주 확인해 주세요.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스

다음 Azure 서비스는 Azure 리소스에 대한 관리 ID를 지원합니다.

| 서비스 | 시스템 할당 상태 | 사용자 할당 상태| 구성 | 토큰 가져오기 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | 사용 가능 | 미리 보기 | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)<br>[REST (영문)](qs-configure-rest-vm.md) | [REST (영문)](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | 사용 가능 | 미리 보기 | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Azure 리소스 관리자 템플릿](qs-configure-template-windows-vmss.md)<br>[REST (영문)](qs-configure-rest-vmss.md) | [REST (영문)](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | Windows: 사용 가능함 <br> Linux: 서비스 예정 | 사용할 수 없음 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager 템플릿](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST (영문)](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure 기능 | 사용 가능 | 사용할 수 없음 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager 템플릿](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST (영문)](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Logic Apps | 사용 가능 | 사용할 수 없음 | [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)<br>[Azure Resource Manager 템플릿](/azure/app-service/app-service-managed-service-identity#deployment-template) |  |
| Azure Data Factory V2 | 사용 가능 | 사용할 수 없음 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST (영문)](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API Management | 사용 가능 | 사용할 수 없음 | [Azure Resource Manager 템플릿](/azure/api-management/api-management-howto-use-managed-service-identity) |
| Azure Container Instances | Linux: 미리 보기<br>Windows: 사용할 수 없음 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)<br>[Azure Resource Manager 템플릿](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)<br>[YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file) |  |


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, Azure 리소스에 대해 관리 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

| 서비스 | 리소스 ID | 상태 | Date | 액세스 권한 할당 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure 리소스 관리자 | `https://management.azure.com/` | 사용 가능 | 2017년 9월 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | `https://vault.azure.net` | 사용 가능 | 2017년 9월 | |
| Azure Data Lake | `https://datalake.azure.net/` | 사용 가능 | 2017년 9월 | |
| Azure SQL | `https://database.windows.net/` | 사용 가능 | 2017년 10월 | |
| Azure Event Hubs | `https://eventhubs.azure.net` | 미리 보기 | 2017년 12월 | |
| Azure Service Bus | `https://servicebus.azure.net` | 미리 보기 | 2017년 12월 | |
| Azure Storage | `https://storage.azure.com/` | 미리 보기 | 2018년 5월 | |