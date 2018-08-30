---
title: 관리 서비스 ID를 지원하는 Azure 서비스
description: 관리 서비스 ID 및 Azure AD 인증을 지원하는 서비스 목록
services: active-directory
author: daveba
ms.author: daveba
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: active-directory
ms.component: msi
manager: mtillman
ms.openlocfilehash: 04247792f8467ecf51a074d1e03bbd6e7c553a02
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247258"
---
# <a name="services-that-support-managed-service-identity"></a>관리 서비스 ID를 지원하는 서비스 

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드에 자격 증명이 없어도 Azure AD 인증을 지원하는 모든 서비스를 인증할 수 있습니다. 현재 Azure 전반에 걸쳐 관리 서비스 ID와 Azure AD 인증을 통합하는 과정이 진행되고 있습니다. 업데이트를 자주 확인해 주세요.

## <a name="azure-services-that-support-managed-service-identity"></a>관리 서비스 ID를 지원하는 Azure 서비스

관리 서비스 ID를 지원하는 Azure 서비스는 다음과 같습니다.

| 서비스 | 시스템 할당 상태 | 사용자 할당 상태| 구성 | 토큰 가져오기 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | 미리 보기 | 미리 보기 | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)<br>[REST](qs-configure-rest-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Virtual Machine Scale Sets | 미리 보기 | 미리 보기 | [Azure Portal](qs-configure-portal-windows-vmss.md)<br>[PowerShell](qs-configure-powershell-windows-vmss.md)<br>[Azure CLI](qs-configure-cli-windows-vmss.md)<br>[Azure 리소스 관리자 템플릿](qs-configure-template-windows-vmss.md)<br>[REST](qs-configure-rest-vmss.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell)
| Azure App Service | 사용 가능 | 사용할 수 없음 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager 템플릿](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell)  |
| Azure 기능 | 사용 가능 | 사용할 수 없음 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure CLI](/azure/app-service/app-service-managed-service-identity#using-the-azure-cli)<br>[Azure PowerShell](/azure/app-service/app-service-managed-service-identity#using-azure-powershell)<br>[Azure Resource Manager 템플릿](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol)<br>[.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[JavaScript](/azure/app-service/app-service-managed-service-identity#token-js)<br>[PowerShell](/azure/app-service/app-service-managed-service-identity#token-powershell) |
| Azure Data Factory V2 | 미리 보기 | 사용할 수 없음 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |
| Azure API Management | 사용 가능 | 사용할 수 없음 | [Azure Resource Manager 템플릿](/azure/api-management/api-management-howto-use-managed-service-identity) | 


## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, 관리 서비스 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

| 서비스 | 리소스 ID | 상태 | Date | 액세스 권한 할당 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure 리소스 관리자 | https://management.azure.com/ | 사용 가능 | 2017년 9월 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | 사용 가능 | 2017년 9월 | |
| Azure Data Lake | https://datalake.azure.net/ | 사용 가능 | 2017년 9월 | |
| Azure SQL | https://database.windows.net/ | 사용 가능 | 2017년 10월 | |
| Azure Event Hubs | https://eventhubs.azure.net | 사용 가능 | 2017년 12월 | |
| Azure Service Bus | https://servicebus.azure.net | 사용 가능 | 2017년 12월 | |
| Azure Storage | https://azure.microsoft.com/services/storage/ | 미리 보기 | 2018년 5월 | |
