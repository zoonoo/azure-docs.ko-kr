---
title: Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스를 승인합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255367"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여

Azure 저장소는 Azure Active Directory(Azure AD)를 사용하여 Blob 및 Queue 저장소에 대한 요청을 승인합니다. Azure AD를 사용하면 역할 기반 액세스 제어(RBAC)를 사용하여 사용자, 그룹 또는 응용 프로그램 서비스 주체일 수 있는 보안 주체에 대한 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에서 인증되어 OAuth 2.0 토큰을 반환합니다. 그런 다음 토큰을 사용하여 Blob 또는 Queue 저장소에 대한 요청을 승인할 수 있습니다.

Azure AD를 사용하여 Azure 저장소에 대한 요청을 승인하면 공유 키 권한 부여를 통해 뛰어난 보안과 사용 편의성을 제공합니다. 공유 키에 내재된 잠재적인 보안 취약점을 최소화하기 위해 가능하면 Blob 및 큐 응용 프로그램과 함께 Azure AD 권한 부여를 사용하는 것이 좋습니다.

Azure AD를 사용하면 모든 공용 지역 및 국가 클라우드의 모든 범용 및 Blob 저장소 계정에 대해 권한 부여를 사용할 수 있습니다. Azure 리소스 관리자 배포 모델로 만든 저장소 계정만 Azure AD 권한 부여를 지원합니다.

Blob 저장소는 또한 Azure AD 자격 증명으로 서명된 SAS(공유 액세스 서명) 만들기를 지원합니다. 자세한 내용은 [공유 액세스 서명이 있는 데이터에 대한 제한된 액세스 권한 부여를](storage-sas-overview.md)참조하십시오.

Azure Files는 도메인 에 가입한 VM에 대해서만 SMB를 통해 AD(미리 보기) 또는 Azure AD DS(GA)를 통해 권한 부여를 지원합니다. Azure 파일에 대한 SMB를 통해 AD(미리 보기) 또는 Azure AD DS(Azure AD DS)를 사용하는 방법에 대해 알아보려면 [SMB 액세스에 대한 Azure 파일 ID 기반 인증 지원 개요를](../files/storage-files-active-directory-overview.md)참조하십시오.

Azure AD를 사용할 수 있는 권한 부여는 Azure 테이블 저장소에 대해 지원되지 않습니다. 공유 키를 사용하여 테이블 저장소에 대한 요청을 승인합니다.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 및 큐에 대한 Azure AD 개요

보안 주체(사용자, 그룹 또는 응용 프로그램)가 Blob 또는 큐 리소스에 액세스하려고 시도하는 경우 익명 액세스에 사용할 수 있는 Blob이 아니면 요청이 승인되어야 합니다. Azure AD를 사용하면 리소스에 대한 액세스는 2단계 프로세스입니다. 첫째, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 다음으로 토큰은 Blob 또는 Queue 서비스에 대한 요청의 일부로 전달되고 서비스에서 지정된 리소스에 대한 액세스를 승인하는 데 사용됩니다.

인증 단계에서는 응용 프로그램이 런타임에 OAuth 2.0 액세스 토큰을 요청해야 합니다. 응용 프로그램이 Azure VM, 가상 시스템 규모 집합 또는 Azure Functions 앱과 같은 Azure 엔터티 내에서 실행 중인 경우 [관리되는 ID를](../../active-directory/managed-identities-azure-resources/overview.md) 사용하여 Blob 또는 큐에 액세스할 수 있습니다. 관리되는 ID로 만든 요청을 Azure Blob 또는 Queue 서비스에 승인하는 방법을 알아보려면 [Azure Active Directory 및 Azure 리소스에 대한 관리되는 ID를 사용하여 Blob 및 큐에 대한 액세스 권한 부여를](storage-auth-aad-msi.md)참조하세요.

권한 부여 단계에서는 하나 이상의 RBAC 역할을 보안 주체에 할당해야 합니다. Azure Storage는 Blob 및 큐 데이터에 대한 일반적인 사용 권한 집합을 포함하는 RBAC 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가질 수 있는 권한이 결정됩니다. Azure 저장소에 대한 RBAC 역할 할당에 대해 자세히 알아보려면 [RBAC를 사용하여 저장소 데이터에](storage-auth-aad-rbac.md)대한 액세스 권한 관리를 참조하십시오.

Azure Blob 또는 Queue 서비스에 대한 요청을 하는 네이티브 응용 프로그램 및 웹 응용 프로그램도 Azure AD를 사용하여 액세스 권한을 부여할 수 있습니다. 액세스 토큰을 요청하고 Blob 또는 큐 데이터에 대한 요청을 승인하는 방법을 알아보려면 [Azure Storage 응용 프로그램에서 Azure AD를 사용하여 Azure 저장소에](storage-auth-aad-app.md)대한 액세스 권한 부여를 참조하세요.

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 RBAC 역할 할당

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 Blob 및 큐 데이터에 액세스하는 데 사용되는 일반적인 사용 권한 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의합니다. Blob 및 큐 데이터에 대한 액세스에 대한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 보안 주체에 할당되면 Azure는 해당 보안 보안 주체에 대한 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 Azure 리소스에 대한 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [관리되는 ID일](../../active-directory/managed-identities-azure-resources/overview.md)수 있습니다.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Blob 및 큐에 대한 기본 제공 RBAC 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

보안 주체에 기본 제공 RBAC 역할을 할당하는 방법을 알아보려면 다음 문서 중 하나를 참조하십시오.

- [Azure Portal에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [PowerShell에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하세요. 사용자 지정 RBAC 역할 만들기에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어에 대한 사용자 지정 역할 만들기를](../../role-based-access-control/custom-roles.md)참조하십시오.

### <a name="access-permissions-for-data-operations"></a>데이터 작업에 대한 권한 액세스 권한

특정 Blob 또는 Queue 서비스 작업을 호출하는 데 필요한 사용 권한에 대한 자세한 내용은 [Blob 및 큐 데이터 작업을 호출할 수 있는 권한을](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)참조하세요.

## <a name="resource-scope"></a>리소스 범위

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD 계정으로 데이터에 액세스

Azure 포털, PowerShell 또는 Azure CLI를 통해 Blob 또는 큐 데이터에 대한 액세스는 사용자의 Azure AD 계정을 사용하거나 계정 액세스 키(공유 키 권한 부여)를 사용하여 권한을 부여할 수 있습니다.

### <a name="data-access-from-the-azure-portal"></a>Azure 포털의 데이터 액세스

Azure 포털은 Azure AD 계정 또는 계정 액세스 키를 사용하여 Azure 저장소 계정의 Blob 및 큐 데이터에 액세스할 수 있습니다. Azure 포털에서 사용하는 권한 부여 체계는 사용자에게 할당된 RBAC 역할에 따라 다릅니다.

Blob 또는 큐 데이터에 액세스하려고 하면 Azure 포털이 먼저 **Microsoft.Storage/StorageAccounts/listkeys/action을**사용하여 RBAC 역할이 할당되었는지 여부를 확인합니다. 이 작업을 사용하여 역할이 할당된 경우 Azure Portal은 공유 키 권한 부여를 통해 Blob 및 큐 데이터에 액세스하기 위해 계정 키를 사용합니다. 이 작업을 사용하여 역할이 할당되지 않은 경우 Azure Portal은 Azure AD 계정을 사용하여 데이터에 액세스하려고 시도합니다.

Azure AD 계정을 사용하여 Azure 포털의 Blob 또는 큐 데이터에 액세스하려면 Blob 및 큐 데이터에 액세스할 수 있는 권한이 필요하며 Azure 포털의 저장소 계정 리소스를 탐색할 수 있는 권한도 필요합니다. Azure Storage에서 제공하는 기본 제공 역할은 Blob 및 큐 리소스에 대한 액세스 권한을 부여하지만 저장소 계정 리소스에 대한 사용 권한을 부여하지는 않습니다. 이러한 이유로 포털에 액세스하려면 저장소 계정 수준 이상으로 범위가 지정되는 [Reader](../../role-based-access-control/built-in-roles.md#reader) 역할과 같은 Azure Resource Manager 역할도 할당해야 합니다. **Reader** 역할은 가장 제한된 권한을 부여하지만 저장소 계정 관리 리소스에 대한 액세스를 부여하는 다른 Azure Resource Manager 역할도 허용됩니다. Azure AD 계정이 있는 Azure 포털의 데이터 액세스에 대한 권한을 사용자에게 할당하는 방법에 대한 자세한 내용은 Azure 포털에서 [RBAC를 사용하여 Azure Blob 및 큐 데이터에](storage-auth-aad-rbac-portal.md)대한 권한 부여 를 참조하세요.

Azure 포털은 컨테이너 또는 큐로 이동할 때 사용 중인 권한 부여 스키마를 나타냅니다. 포털의 데이터 액세스에 대한 자세한 내용은 [Azure 포털 사용을 사용하여 Blob 또는 큐 데이터에 액세스합니다.](storage-access-blobs-queues-portal.md)

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell 또는 Azure CLI의 데이터 액세스

Azure CLI 및 PowerShell은 Azure AD 자격 증명으로 로그인을 지원합니다. 로그인하면 해당 자격 증명에서 세션이 실행됩니다. 자세한 내용은 [Azure AD 자격 증명을 사용하여 Azure CLI 또는 PowerShell 명령 실행을 참조하여 Blob 또는 큐 데이터에 액세스합니다.](authorize-active-directory-powershell.md)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 및 Azure 리소스에 대한 관리ID를 사용하여 Blob 및 큐에 대한 액세스 권한을 부여합니다.](storage-auth-aad-msi.md)
- [Blob 및 큐에 액세스할 수 있도록 응용 프로그램에서 Azure Active Directory로 인증](storage-auth-aad-app.md)
- [Azure Active Directory 기반 액세스 제어에 대한 Azure 저장소 지원은 일반적으로 사용 가능](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
