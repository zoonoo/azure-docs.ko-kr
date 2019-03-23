---
title: Azure blob 및 Azure Active Directory를 사용 하 여 큐에 대 한 액세스 인증 | Microsoft Docs
description: Azure blob 및 Azure Active Directory를 사용 하 여 큐에 대 한 액세스를 인증 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ff543b7275ab05a83b1be1d156cbc6059a3b5430
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369900"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory"></a>Azure blob 및 Azure Active Directory를 사용 하 여 큐에 대 한 액세스를 인증 합니다.

Azure Storage는 Blob 및 큐 서비스에 대해 Azure AD(Active Directory)를 사용하는 인증 및 권한 부여를 지원합니다. Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 사용자에 대한 액세스 권한을 부여할 수 있습니다. 

Azure AD 자격 증명을 통한 사용자 또는 애플리케이션 권한 부여는 다른 인증 수단보다 보안 수준이 높고 사용이 간편합니다. 애플리케이션에서 공유 키 인증을 계속 사용할 수 있는 동안 Azure AD를 사용하면 코드에서 계정 액세스 키를 저장하지 않아도 됩니다. SAS(공유 액세스 서명)를 계속 사용하여 저장소 계정의 리소스에 세분화된 액세스 권한을 부여할 수도 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지하는 방법을 걱정할 필요 없이 유사한 기능을 제공합니다. Microsoft에서는 가능하다면 Azure Storage 애플리케이션에 Azure AD 인증을 사용하는 것을 권장합니다.

인증 및 권한 부여 Azure AD 자격 증명을 사용 하 여 모든 범용 v2, 범용 v1 및 Blob 저장소 계정은 모든 공용 지역에서 제공 됩니다. 저장소 계정에만 Azure AD 권한 부여는 Azure Resource Manager 배포 모델 지원으로 만들어집니다.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="overview-of-azure-ad-for-blobs-and-queues"></a>Blob 및 큐에 대 한 Azure AD의 개요

Azure Storage에서 Azure AD 통합을 사용하는 첫 번째 단계는 스토리지 데이터의 RBAC 역할을 Azure리소스에 대한 서비스 주체(사용자, 그룹 또는 응용 프로그램 서비스 주체) 또는 관리 ID에 할당하는 것입니다. RBAC 역할에는 컨테이너 및 큐에 대한 사용 권한의 공통 집합이 포함됩니다. Azure Storage에 대 한 RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 참조 하세요 [RBAC 사용 하 여 저장소 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.

Azure AD를 사용하여 애플리케이션에서 저장소 리소스에 대한 액세스 권한을 부여하려면 코드에서 OAuth 2.0 액세스 토큰을 요청해야 합니다. 액세스 토큰을 요청 하 고 사용 하 여 Azure Storage에 요청 권한을 부여 하는 방법에 알아보려면 참조 [Azure Storage 응용 프로그램에서 Azure AD 사용 하 여 인증](storage-auth-aad-app.md)합니다. 관리 되는 id를 사용 하는 경우 참조 [blob 및 Azure 사용 하 여 큐에 대 한 인증 액세스 identities를 관리 되는 Azure 리소스에 대 한](storage-auth-aad-msi.md)합니다.

이제 Azure CLI 및 PowerShell에서는 Azure AD ID를 사용하여 로그인하도록 지원합니다. Azure AD id에 로그인 하면 세션 해당 id에서 실행 됩니다. 자세한 내용은 참조 하세요 [CLI 또는 PowerShell을 사용 하 여 Azure Storage에 액세스 하려면 Azure AD id를 사용 하 여](storage-auth-aad-script.md)입니다.

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 및 큐의 RBAC 역할

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 컨테이너 또는 큐에 액세스하는 데 사용되는 사용 권한의 공통 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의합니다. 

RBAC 역할에는 Azure AD 보안 주체에 할당 된 Azure 부여 해당 보안 주체에 대 한 해당 리소스에 액세스 합니다. 액세스 권한은 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. 사용자, 그룹, 응용 프로그램 서비스 주체를 Azure AD 보안 주체 수 또는 [Azure 리소스에 대 한 id 관리](../../active-directory/managed-identities-azure-resources/overview.md)합니다.

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Azure portal에서 기본 제공 역할을 할당 하는 방법에 알아보려면 참조 [Azure 컨테이너 및 Azure portal에서 RBAC 사용 하 여 큐 액세스 권한을 부여할](storage-auth-aad-rbac.md)합니다.

### <a name="access-permissions-granted-by-rbac-roles"></a>RBAC 역할에 의해 부여 된 권한 

각 Azure Storage 작업을 호출하는 데 필요한 사용 권한에 대한 자세한 내용은 [REST 작업 호출을 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 컨테이너 및 Azure portal에서 RBAC 사용 하 여 큐 액세스 권한 부여](storage-auth-aad-rbac.md)
- [Blob 및 큐 액세스를 위해 애플리케이션에서 Azure Active Directory를 사용하여 인증](storage-auth-aad-app.md)
- [Azure 리소스에 대 한 관리 되는 id 사용 하 여 큐 및 blob에 대 한 액세스 인증](storage-auth-aad-msi.md)
- [Azure AD id를 사용 하 여 CLI 또는 PowerShell을 사용 하 여 Azure Storage 액세스](storage-auth-aad-script.md)