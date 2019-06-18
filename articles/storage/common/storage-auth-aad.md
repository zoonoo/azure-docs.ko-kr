---
title: Azure blob 및 Azure Active Directory를 사용 하 여 큐에 대 한 액세스 인증 | Microsoft Docs
description: Azure blob 및 Azure Active Directory를 사용 하 여 큐에 대 한 액세스를 인증 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 66051bd0f8be349f748c72218d538bba273be8f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147263"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Azure blob 및 Azure Active Directory를 사용 하 여 큐에 대 한 액세스를 인증 합니다.

Azure Storage는 Blob 및 큐 서비스에 대해 Azure AD(Active Directory)를 사용하는 인증 및 권한 부여를 지원합니다. Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 사용자에 대한 액세스 권한을 부여할 수 있습니다. 

Azure AD 자격 증명을 통한 사용자 또는 애플리케이션 권한 부여는 다른 인증 수단보다 보안 수준이 높고 사용이 간편합니다. 애플리케이션에서 공유 키 인증을 계속 사용할 수 있는 동안 Azure AD를 사용하면 코드에서 계정 액세스 키를 저장하지 않아도 됩니다. SAS(공유 액세스 서명)를 계속 사용하여 저장소 계정의 리소스에 세분화된 액세스 권한을 부여할 수도 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지하는 방법을 걱정할 필요 없이 유사한 기능을 제공합니다. Microsoft에서는 가능하다면 Azure Storage 애플리케이션에 Azure AD 인증을 사용하는 것을 권장합니다.

인증 및 Azure AD 자격 증명을 사용 하 여 권한 부여를 모두에 사용할 수 있는 범용 및 Blob storage 계정에서 모든 공용 지역 및 국가 클라우드의. 저장소 계정에만 Azure AD 권한 부여는 Azure Resource Manager 배포 모델 지원으로 만들어집니다.

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 및 큐에 대 한 Azure AD의 개요

보안 주체 (사용자, 그룹 또는 응용 프로그램)를 blob 또는 큐 리소스에 액세스 하려고 하는 경우 요청 해야에 권한을 부여할 수는 blob을 익명 액세스에 사용할 수 있는 경우가 아니라면 합니다. Azure AD를 사용 하 여 리소스에 액세스할 수는 2 단계 프로세스입니다. 첫째, 보안 주체의 id가 인증 하 고 OAuth 2.0 토큰을 반환 됩니다. 그런 다음 토큰이 Blob 또는 큐 서비스에는 요청의 일부로 전달 되 고 지정된 된 리소스에 대 한 액세스 권한을 부여 하는 서비스에서 사용 합니다.

인증 단계는 응용 프로그램 런타임 시 OAuth 2.0 액세스 토큰을 요청 해야 합니다. 응용 프로그램을 Azure VM, 가상 머신 확장 집합을 Azure Functions 앱 등 Azure 엔터티 내에서 실행 중인 경우 사용할 수는 [관리 id](../../active-directory/managed-identities-azure-resources/overview.md) 액세스 blob 또는 큐에 있습니다. Azure Blob 또는 큐 서비스에 관리 되는 id로 요청 권한을 부여 하는 방법에 알아보려면 참조 [Azure 리소스에 대 한 blob 및 Azure Active Directory 및 관리 되는 id를 사용 하 여 큐에 대 한 액세스를 인증](storage-auth-aad-msi.md)합니다.

권한 부여 단계 RBAC 역할을 하나 이상의 보안 주체에 할당할 수는 필요 합니다. Azure Storage blob 및 큐 데이터에 대 한 사용 권한의 공통 집합을 포함 하는 RBAC 역할을 제공 합니다. 보안 주체에 할당 된 역할에는 보안 주체가 갖는 사용 권한을 결정 합니다. Azure Storage에 대 한 RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 참조 하세요 [RBAC 사용 하 여 저장소 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.

네이티브 응용 프로그램 및 Azure Blob 또는 큐 서비스에 요청 하는 웹 응용 프로그램은 Azure AD를 사용 하 여 인증할 수도 있습니다. 액세스 토큰을 요청 하 고 blob 또는 큐 데이터에 대 한 요청 권한을 부여 하도록 사용 하는 방법에 알아보려면 참조 [Azure Storage 응용 프로그램에서 Azure AD 사용 하 여 인증](storage-auth-aad-app.md)합니다.

## <a name="assigning-rbac-roles-for-access-rights"></a>액세스 권한에 대 한 RBAC 역할 할당

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage blob 및 큐 데이터에 액세스 하는 데 사용 권한의 공통 집합을 포함 하는 기본 제공 RBAC 역할 집합을 정의 합니다. 또한 blob 및 큐 데이터 액세스에 대 한 사용자 지정 역할을 정의할 수 있습니다.

RBAC 역할에는 Azure AD 보안 주체에 할당 된 Azure 부여 해당 보안 주체에 대 한 해당 리소스에 액세스 합니다. 액세스 권한은 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. 사용자, 그룹, 응용 프로그램 서비스 주체를 Azure AD 보안 주체 수 또는 [Azure 리소스에 대 한 id 관리](../../active-directory/managed-identities-azure-resources/overview.md)합니다.

### <a name="built-in-rbac-roles-for-blobs-and-queues"></a>Blob 및 큐에 대 한 기본 제공 RBAC 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

보안 주체에 기본 제공 RBAC 역할을 할당 하는 방법에 알아보려면 다음 문서 중 하나를 참조 하세요.

- [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI를 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [PowerShell을 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](../../role-based-access-control/role-definitions.md#management-and-data-operations-preview)를 참조하세요. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 [Access Control에 대 한 사용자 지정 역할 만들기](../../role-based-access-control/custom-roles.md)합니다.

### <a name="access-permissions-for-data-operations"></a>데이터 작업에 대 한 액세스 권한

특정 Blob 또는 큐 서비스 작업을 호출 하는 데 필요한 사용 권한에 대 한 자세한 내용은 참조 하세요. [blob 및 큐 데이터 작업을 호출 하는 것에 대 한 권한을](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)합니다.

## <a name="resource-scope"></a>리소스 범위

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="access-data-with-an-azure-ad-account"></a>Azure AD 계정으로 데이터에 액세스

Azure portal, PowerShell 통해 데이터를 blob 또는 큐에 대 한 액세스 또는 사용자의 Azure AD 계정을 사용 하 여 또는 계정 액세스 키 (공유 키 인증)를 사용 하 여, Azure CLI는 권한을 부여할 수 있습니다.

### <a name="data-access-from-the-azure-portal"></a>Azure portal에서 데이터 액세스

Azure portal blob 및 큐 데이터를 Azure storage 계정에 액세스 하 Azure AD 계정 또는 계정 액세스 키를 사용할 수 있습니다. Azure portal을 사용 하는 권한 부여 체계는 사용자에 게 할당 되는 RBAC 역할에 따라 달라 집니다.

Blob 또는 큐 데이터에 액세스 하려고 할 때 Azure 포털을 먼저 여부를 할당 했는지 확인 된 RBAC 역할을 사용 하 여 **Microsoft.Storage/storageAccounts/listkeys/action**합니다. 이 작업을 사용 하 여 역할 할당 된 Azure portal 공유 키 인증을 통해 blob 및 큐 데이터에 액세스 하기 위한 계정 키를 사용 합니다. 이 작업을 사용 하 여 역할 할당 되었습니다 없습니다, Azure portal에 Azure AD 계정을 사용 하 여 데이터에 액세스 하려고 합니다.

Blob 또는 큐에 액세스 하 Azure AD 계정을 사용 하 여 Azure portal에서 데이터를 blob 및 큐 데이터에 액세스 하려면 권한이 필요 하 고 또한 Azure portal에서 저장소 계정 리소스를 탐색 하려면 권한이 필요 합니다. Azure Storage에서 제공 하는 기본 제공 역할에는 blob 및 큐 리소스에 대 한 액세스 권한을 부여 하지만 저장소 계정 리소스에 대 한 권한을 부여 하지 않습니다. 따라서 포털에 액세스 해야 Azure Resource Manager 역할을 할당 같은 합니다 [판독기](../../role-based-access-control/built-in-roles.md#reader) 저장소 계정 또는 그 보다 높은 수준으로 범위가 지정 된 역할을 합니다. 합니다 **판독기** 역할은 가장 제한 된 권한을 부여 하지만 저장소 계정 관리 리소스에 대 한 액세스 권한을 부여 하는 다른 Azure 리소스 관리자 역할에도 적용 됩니다. Azure AD 계정으로 Azure portal에서 데이터 액세스에 대 한 사용자에 게 권한을 할당 하는 방법에 대 한 자세한 내용은 참조 하세요 [Azure blob 및 큐 데이터에 RBAC 사용 하 여 Azure portal에서 액세스 권한을 부여](storage-auth-aad-rbac-portal.md)합니다.

Azure portal에는 권한 부여 체계 나타냅니다 컨테이너 또는 큐로 이동할 때 사용 됩니다. 포털에서 데이터 액세스에 대 한 자세한 내용은 참조 하세요. [blob 또는 큐 데이터에 액세스 하려면 Azure portal을 사용 하 여](storage-access-blobs-queues-portal.md)입니다.

### <a name="data-access-from-powershell-or-azure-cli"></a>PowerShell 또는 Azure CLI에서 데이터 액세스

Azure CLI 및 PowerShell에 Azure AD 자격 증명으로 로그인을 지원 합니다. 로그인 한 후 세션 자격 증명으로 실행 됩니다. 자세한 내용은 참조 하세요 [blob 또는 큐 데이터에 액세스 하려면 Azure AD 자격 증명을 사용 하 여 실행 하는 Azure CLI 또는 PowerShell 명령을](storage-auth-aad-script.md)합니다.

## <a name="azure-ad-authentication-over-smb-for-azure-files"></a>Azure Files에 대 한 SMB 통한 azure AD 인증

Azure Files는 도메인에 가입된 VM(미리 보기)에 대해 SMB를 통한 Azure AD 인증을 지원합니다. SMB를 통한 Azure AD를 사용 하 여 Azure Files에 대 한 자세한 내용은 참조 하세요 [SMB 통한 Azure Files (미리 보기)에 대 한 개요의 Azure Active Directory 인증](../files/storage-files-active-directory-overview.md)합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대 한 blob 및 Azure Active Directory 및 관리 되는 id를 사용 하 여 큐에 대 한 액세스 인증](storage-auth-aad-msi.md)
- [Blob 및 큐 액세스를 위해 애플리케이션에서 Azure Active Directory를 사용하여 인증](storage-auth-aad-app.md)
- [Azure Active Directory에 대 한 azure Storage 지원 기반 access control 일반 공급](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)
