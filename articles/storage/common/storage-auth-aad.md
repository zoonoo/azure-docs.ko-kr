---
title: Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure AD(Azure Active Directory)를 사용하여 Azure Blob 및 큐에 대한 액세스 권한을 부여합니다. 액세스 권한에 대한 Azure 역할을 할당합니다. Azure AD 계정으로 데이터에 액세스합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: f61e9c86a69db88ed2d673c0af7dc17995f97115
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901925"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여

Azure Storage는 Azure AD(Azure Active Directory)를 사용하여 Blob 및 큐 스토리지에 대한 요청을 승인하도록 지원합니다. Azure AD를 사용하면 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 주체일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에 의해 인증되어 OAuth 2.0 토큰을 반환합니다. 그런 다음, 토큰을 사용하여 Blob 또는 Queue Storage에 대한 요청에 권한을 부여할 수 있습니다.

Azure AD를 사용하여 Azure Storage에 대한 요청에 권한을 부여하면 공유 키 권한 부여보다 뛰어난 보안과 사용 편의성이 구현됩니다. 공유 키에 내재된 잠재적인 보안 취약성을 최소화하기 위해 가능한 경우 Blob 및 큐 애플리케이션에서 Azure AD 권한 부여를 사용하는 것이 좋습니다.

Azure AD를 통한 권한 부여는 모든 퍼블릭 지역 및 국가별 클라우드의 모든 범용 및 Blob Storage 계정에 사용할 수 있습니다. Azure Resource Manager 배포 모델로 만든 스토리지 계정만 Azure AD 권한 부여를 지원합니다.

또한 Blob Storage는 Azure AD 자격 증명으로 서명된 SAS(공유 액세스 서명) 만들기를 지원합니다. 자세한 내용은 [공유 액세스 서명을 사용하여 데이터에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)를 참조하세요.

Azure Files는 도메인에 가입된 VM에 대해서만 SMB를 통해 AD(미리 보기) 또는 Azure AD DS(GA)를 사용하는 권한 부여를 지원합니다. Azure Files용 SMB를 통한 AD(미리 보기) 또는 Azure AD DS(GA) 사용에 대한 자세한 내용은 [SMB 액세스에 대한 Azure Files ID 기반 인증 지원 개요](../files/storage-files-active-directory-overview.md)를 참조하세요.

Azure Table Storage에는 Azure AD를 통해 권한 부여가 지원되지 않습니다. 공유 키를 사용하여 Table Storage에 대한 요청에 권한을 부여합니다.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 및 큐에 대한 Azure AD 개요

보안 주체(사용자, 그룹 또는 애플리케이션)가 Blob 또는 큐 리소스에 액세스하려고 할 때 익명 액세스에 Blob을 사용할 수 있는 경우가 아니면 요청에 권한을 부여해야 합니다. Azure AD의 경우 리소스에 대한 액세스는 2단계 프로세스입니다. 먼저, 보안 주체의 ID가 인증되고 OAuth 2.0 토큰이 반환됩니다. 다음으로 토큰이 Blob 또는 큐 서비스에 대한 요청의 일부로 전달되고 서비스에서 지정된 리소스에 대한 액세스 권한을 부여하는 데 사용됩니다.

인증 단계를 수행하려면 런타임 시 애플리케이션 요청에 OAuth 2.0 액세스 토큰이 있어야 합니다. 애플리케이션이 Azure VM, 가상 머신 확장 집합 또는 Azure Function 앱과 같은 Azure 엔터티 내에서 실행되는 경우 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 Blob 또는 큐에 액세스할 수 있습니다. 관리 ID에서 Azure Blob 또는 큐 서비스에 대해 수행한 요청에 권한을 부여하는 방법을 알아보려면 [Azure 리소스에 대한 Azure Active Directory 및 관리 ID를 사용하여 Blob 및 큐에 대한 액세스 권한 부여](storage-auth-aad-msi.md)를 참조하세요.

권한 부여 단계에서는 보안 주체에 하나 이상의 Azure 역할을 할당해야 합니다. Azure Storage는 Blob 및 큐 데이터에 대한 일반적인 사용 권한 집합을 포함하는 Azure 역할을 제공합니다. 보안 주체에 할당된 역할에 따라 보안 주체가 가지는 권한이 결정됩니다. Azure Storage의 Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)을 참조하세요.

Azure Blob 또는 큐 서비스에 대해 요청을 수행하는 네이티브 애플리케이션 및 웹 애플리케이션은 Azure AD를 사용하여 액세스에 권한을 부여할 수도 있습니다. 액세스 토큰을 요청하고 이를 사용하여 Blob 또는 큐 데이터에 대한 요청에 권한을 부여하는 방법을 알아보려면 [Azure Storage 애플리케이션에서 Azure AD를 사용하여 Azure Storage에 대한 액세스 권한 부여](storage-auth-aad-app.md)를 참조하세요.

## <a name="assign-azure-roles-for-access-rights"></a>액세스 권한을 위한 Azure 역할 할당

Azure AD(Azure Active Directory)는 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure 스토리지는 Blob 및 큐 데이터에 액세스하는 데 사용되는 공통 권한 집합을 포함하는 Azure 기본 제공 역할 집합을 정의합니다. Blob 및 큐 데이터에 액세스하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

Azure AD 보안 주체에 Azure 역할을 할당하는 경우 Azure는 해당 보안 주체의 해당 리소스에 대한 액세스 권한을 부여합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 사용자 또는 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

### <a name="azure-built-in-roles-for-blobs-and-queues"></a>Blob 및 큐를 위한 Azure 기본 제공 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

보안 주체에 Azure 기본 제공 역할을 할당하는 방법을 알아보려면 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md)을 참조하세요.

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하세요. Azure 사용자 지정 역할 만들기에 대한 자세한 내용은 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 참조하세요.

### <a name="access-permissions-for-data-operations"></a>데이터 작업에 대한 액세스 권한

특정 Blob 또는 큐 서비스 작업을 호출하는 데 필요한 권한에 대한 자세한 내용은 [Blob 및 큐 데이터 작업을 호출하기 위한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조하세요.

## <a name="resource-scope"></a>리소스 범위

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD 계정으로 데이터에 액세스

Azure Portal, PowerShell 또는 Azure CLI를 통해 Blob 또는 큐 데이터에 액세스하려면 사용자의 Azure AD 계정을 사용하거나 계정 액세스 키(공유 키 권한 부여)를 사용하여 권한을 부여할 수 있습니다.

### <a name="data-access-from-the-azure-portal"></a>Azure Portal에서 데이터 액세스

Azure Portal에서는 Azure AD 계정 또는 계정 액세스 키를 사용하여 Azure Storage 계정의 Blob 및 큐 데이터에 액세스할 수 있습니다. Azure Portal에서 사용하는 권한 부여 체계는 할당된 Azure 역할에 따라 달라집니다.

Blob 또는 큐 데이터에 액세스하려고 하면 Azure Portal은 먼저 **Microsoft.Storage/storageAccounts/listkeys/action** 을 사용하여 Azure 역할이 할당되었는지 여부를 확인합니다. 이 작업을 사용하여 역할을 할당한 경우 Azure Portal은 공유 키 인증을 통해 Blob 및 큐 데이터에 액세스하기 위해 계정 키를 사용 합니다. 이 작업에 역할을 할당하지 않은 경우 Azure Portal은 Azure AD 계정을 사용하여 데이터에 액세스하려고 합니다.

Azure AD 계정을 사용하여 Azure Portal에서 Blob 또는 큐 데이터에 액세스하려면 Blob 및 큐 데이터에 액세스할 수 있는 권한이 필요하며, Azure Portal의 스토리지 계정 리소스를 탐색할 수 있는 권한도 필요합니다. Azure Storage에서 제공하는 기본 제공 역할은 Blob 및 큐 리소스에 대한 액세스 권한을 부여하지만 스토리지 계정 리소스에 대한 사용 권한은 부여하지 않습니다. 이러한 이유로 포털에 액세스하려면 스토리지 계정 수준 이상으로 범위가 지정된 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할과 같은 Azure Resource Manager 역할도 할당해야 합니다. **Reader** 역할은 가장 제한된 사용 권한을 부여하지만, 스토리지 계정 관리 리소스에 대한 액세스 권한을 부여하는 다른 Azure Resource Manager 역할도 허용됩니다. Azure AD 계정을 사용하여 Azure Portal에서 데이터 액세스 권한을 사용자에게 할당하는 방법에 대한 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](../blobs/assign-azure-role-data-access.md) 또는 [큐 데이터에 액세스하기 위한 Azure 역할 할당](../queues/assign-azure-role-data-access.md)을 참조하세요.

Azure Portal는 컨테이너 또는 큐로 이동할 때 사용 중인 권한 부여 체계를 나타냅니다. 포털의 데이터 액세스에 대한 자세한 내용은 [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택](../blobs/authorize-data-operations-portal.md) 및 [Azure Portal에서 큐 데이터에 대한 액세스 권한을 부여하는 방법 선택](../queues/authorize-data-operations-portal.md)을 참조하세요.

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell 또는 Azure CLI에서 데이터 액세스

이제 Azure CLI 및 PowerShell에서는 Azure AD 자격 증명을 사용하여 로그인하도록 지원합니다. 로그인하면 세션이 해당 자격 증명으로 실행됩니다. 자세한 내용은 [Azure AD 자격 증명으로 Azure CLI 또는 PowerShell 명령을 실행하여 Blob 또는 큐 데이터에 액세스](../blobs/authorize-data-operations-powershell.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스의 Azure Active Directory 및 관리 ID를 사용하여 Blob 및 큐에 대한 액세스 권한 부여](storage-auth-aad-msi.md)
- [Blob 및 큐 액세스를 위해 애플리케이션에서 Azure Active Directory를 사용하여 권한 부여](storage-auth-aad-app.md)
- [Azure Active Directory 기반 액세스 제어에 대한 Azure Storage 지원 일반 제공](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)