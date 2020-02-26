---
title: Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여
titleSuffix: Azure Storage
description: Azure Active Directory를 사용 하 여 Azure blob 및 큐에 대 한 액세스 권한을 부여 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 2/23/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b8a42723a9b56665160e660c0ea1451253c3d185
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588980"
---
# <a name="authorize-access-to-blobs-and-queues-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여

Azure Storage에서는 Azure Active Directory (Azure AD)를 사용 하 여 Blob 및 큐 저장소에 대 한 요청에 권한을 부여할 수 있습니다. Azure AD에서는 RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 보안 주체는 OAuth 2.0 토큰을 반환 하기 위해 Azure AD에서 인증 됩니다. 그런 다음 토큰을 사용 하 여 Blob 또는 큐 저장소에 대 한 요청을 인증할 수 있습니다.

Azure AD를 사용 하 여 Azure Storage에 대 한 요청을 승인 하면 공유 키 권한 부여를 통해 뛰어난 보안과 사용 편의성을 제공 합니다. 가능 하면 blob 및 큐 응용 프로그램에 Azure AD 권한 부여를 사용 하 여 공유 키에 내재 된 잠재적 보안 취약성을 최소화 하는 것이 좋습니다.

Azure AD를 사용한 권한 부여는 모든 공용 지역 및 국가 클라우드의 모든 범용 및 Blob 저장소 계정에 사용할 수 있습니다. Azure Resource Manager 배포 모델로 만든 저장소 계정만 Azure AD 권한 부여를 지원 합니다.

또한 Blob storage는 Azure AD 자격 증명으로 서명 된 SAS (공유 액세스 서명) 만들기를 추가로 지원 합니다. 자세한 내용은 [공유 액세스 서명을 사용 하 여 데이터에 제한 된 액세스 권한 부여](storage-sas-overview.md)를 참조 하세요.

Azure Files는 도메인에 가입 된 Vm에 대해서만 AD (미리 보기) 또는 SMB를 통한 Azure AD DS (GA) 권한 부여를 지원 합니다. Azure Files SMB를 통한 AD (미리 보기) 또는 Azure AD DS (GA)를 사용 하는 방법에 대 한 자세한 내용은 [smb 액세스를 위한 Azure Files id 기반 인증 지원 개요](../files/storage-files-active-directory-overview.md)를 참조 하세요.

Azure AD를 사용한 권한 부여는 Azure Table storage에 대해 지원 되지 않습니다. 공유 키를 사용 하 여 테이블 저장소에 대 한 요청에 권한을 부여 합니다.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 및 큐에 대 한 Azure AD 개요

보안 주체 (사용자, 그룹 또는 응용 프로그램)가 blob 또는 큐 리소스에 대 한 액세스를 시도 하는 경우에는 익명 액세스에 사용할 수 있는 blob이 아닌 한 요청에 권한이 부여 되어야 합니다. Azure AD를 사용 하 여 리소스에 대 한 액세스는 2 단계 프로세스입니다. 첫째, 보안 주체의 id가 인증 되 고 OAuth 2.0 토큰이 반환 됩니다. 그런 다음 토큰이 Blob 또는 큐 서비스에 대 한 요청의 일부로 전달 되 고 서비스에서 지정 된 리소스에 대 한 액세스 권한을 부여 하는 데 사용 됩니다.

인증 단계에서는 응용 프로그램이 런타임에 OAuth 2.0 액세스 토큰을 요청 해야 합니다. 응용 프로그램이 azure VM, 가상 머신 확장 집합 또는 Azure Functions 앱과 같은 Azure 엔터티 내에서 실행 되는 경우 [관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 blob 또는 큐에 액세스할 수 있습니다. 관리 id를 사용 하 여 Azure Blob 또는 큐 서비스에 대 한 요청을 인증 하는 방법을 알아보려면 [Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad-msi.md)를 참조 하세요.

권한 부여 단계를 수행 하려면 하나 이상의 RBAC 역할을 보안 주체에 할당 해야 합니다. Azure Storage는 blob 및 큐 데이터에 대 한 일반적인 사용 권한 집합을 포함 하는 RBAC 역할을 제공 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 부여 되는 사용 권한이 결정 됩니다. Azure Storage에 대 한 RBAC 역할 할당에 대해 자세히 알아보려면 [rbac를 사용 하 여 저장소 데이터에 대 한 액세스 권한 관리](storage-auth-aad-rbac.md)를 참조 하세요.

Azure Blob 또는 큐 서비스에 요청 하는 네이티브 응용 프로그램 및 웹 응용 프로그램은 Azure AD를 사용 하 여 액세스 권한을 부여할 수도 있습니다. 액세스 토큰을 요청 하 고이를 사용 하 여 blob 또는 큐 데이터에 대 한 요청을 인증 하는 방법을 알아보려면 [Azure Storage 응용 프로그램에서 AZURE AD를 사용 하 여 Azure Storage에 대 한 액세스 권한 부여](storage-auth-aad-app.md)를 참조 하세요.

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 대 한 RBAC 역할 할당

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 blob 및 큐 데이터에 액세스 하는 데 사용 되는 일반 사용 권한 집합을 포함 하는 기본 제공 RBAC 역할 집합을 정의 합니다. Blob 및 큐 데이터에 액세스 하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 주체에 할당 되 면 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Blob 및 큐에 대 한 기본 제공 RBAC 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

보안 주체에 기본 제공 RBAC 역할을 할당 하는 방법을 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Azure Portal에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [PowerShell에서 RBAC를 사용하여 Azure Blob 및 큐 데이터에 대한 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조하세요. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 [Azure 역할 기반 Access Control에 대 한 사용자 지정 역할 만들기](../../role-based-access-control/custom-roles.md)를 참조 하세요.

### <a name="access-permissions-for-data-operations"></a>데이터 작업에 대 한 액세스 권한

특정 Blob 또는 큐 서비스 작업을 호출 하는 데 필요한 권한에 대 한 자세한 내용은 [blob 및 큐 데이터 작업 호출에 대 한 권한](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)을 참조 하세요.

## <a name="resource-scope"></a>리소스 범위

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD 계정을 사용 하 여 데이터 액세스

사용자의 Azure AD 계정을 사용 하거나 계정 액세스 키 (공유 키 인증)를 사용 하 여 Azure Portal, PowerShell 또는 Azure CLI를 통해 blob 또는 큐 데이터에 액세스할 수 있습니다.

### <a name="data-access-from-the-azure-portal"></a>Azure Portal에서 데이터 액세스

Azure Portal는 azure AD 계정 또는 계정 액세스 키를 사용 하 여 Azure storage 계정의 blob 및 큐 데이터에 액세스할 수 있습니다. Azure Portal에서 사용 하는 권한 부여 체계는 할당 된 RBAC 역할에 따라 다릅니다.

Blob 또는 큐 데이터에 액세스 하려고 하면 Azure Portal는 먼저 **Microsoft. Storage/storageAccounts/listkeys/action**을 사용 하 여 RBAC 역할이 할당 되었는지 여부를 확인 합니다. 이 작업을 사용 하 여 역할을 할당 한 경우 Azure Portal는 공유 키 인증을 통해 blob 및 큐 데이터에 액세스 하기 위해 계정 키를 사용 합니다. 이 작업에 역할을 할당 하지 않은 경우 Azure Portal는 Azure AD 계정을 사용 하 여 데이터에 액세스 하려고 시도 합니다.

Azure AD 계정을 사용 하 여 Azure Portal에서 blob 또는 큐 데이터에 액세스 하려면 blob 및 큐 데이터에 액세스할 수 있는 권한이 필요 하며, Azure Portal 저장소 계정 리소스를 탐색할 수 있는 권한도 필요 합니다. Azure Storage에서 제공 하는 기본 제공 역할은 blob 및 큐 리소스에 대 한 액세스 권한을 부여 하지만 저장소 계정 리소스에 대 한 사용 권한은 부여 하지 않습니다. 이러한 이유로 포털에 대 한 액세스에는 저장소 계정 수준으로 범위가 지정 된 [판독기](../../role-based-access-control/built-in-roles.md#reader) 역할과 같은 Azure Resource Manager 역할의 할당도 필요 합니다. **읽기 권한자** 역할은 가장 제한 된 권한을 부여 하지만 저장소 계정 관리 리소스에 대 한 액세스 권한을 부여 하는 다른 Azure Resource Manager 역할도 허용 됩니다. Azure AD 계정으로 Azure Portal에서 데이터 액세스를 위해 사용자에 게 권한을 할당 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](storage-auth-aad-rbac-portal.md)에 추가를 참조 하세요.

Azure Portal는 컨테이너 또는 큐로 이동할 때 사용 중인 권한 부여 체계를 나타냅니다. 포털의 데이터 액세스에 대 한 자세한 내용은 Azure Portal를 [사용 하 여 blob 또는 큐 데이터에 액세스를](storage-access-blobs-queues-portal.md)참조 하세요.

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell 또는 Azure CLI에서 데이터 액세스

Azure CLI 및 PowerShell은 Azure AD 자격 증명으로 로그인을 지원 합니다. 로그인 하면 해당 자격 증명으로 세션이 실행 됩니다. 자세한 내용은 [AZURE AD 자격 증명을 사용 하 여 Azure CLI 또는 PowerShell 명령을 실행 하 여 blob 또는 큐 데이터에 액세스](authorize-active-directory-powershell.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad-msi.md)
- [Blob 및 큐에 액세스 하기 위해 응용 프로그램에서 Azure Active Directory 권한을 부여 합니다.](storage-auth-aad-app.md)
- [Azure Active Directory 기반 access control에 대 한 Azure Storage 지원 출시](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
