---
title: Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 인증(미리 보기) | Microsoft Docs
description: Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 인증(미리 보기)
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dcf7d237c8cfbf52a804e428d84fff0bb328c7c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012111"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 인증(미리 보기)

Azure Storage는 Blob 및 큐 서비스에 대해 Azure AD(Active Directory)를 사용하는 인증 및 권한 부여를 지원합니다. Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 애플리케이션 서비스 사용자에 대한 액세스 권한을 부여할 수 있습니다. 

Azure AD 자격 증명을 통한 사용자 또는 애플리케이션 권한 부여는 다른 인증 수단보다 보안 수준이 높고 사용이 간편합니다. 애플리케이션에서 공유 키 인증을 계속 사용할 수 있는 동안 Azure AD를 사용하면 코드에서 계정 액세스 키를 저장하지 않아도 됩니다. SAS(공유 액세스 서명)를 계속 사용하여 저장소 계정의 리소스에 세분화된 액세스 권한을 부여할 수도 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지하는 방법을 걱정할 필요 없이 유사한 기능을 제공합니다. Microsoft에서는 가능하다면 Azure Storage 응용 프로그램에 Azure AD 인증을 사용하는 것을 권장합니다.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>미리 보기 정보

미리 보기에 대한 다음 사항을 주의하세요.

- Azure AD 통합은 미리 보기 상태인 Blob 및 큐 서비스에서만 지원됩니다.
- Azure AD 통합은 모든 공용 지역에서 GPv1, GPv2 및 Blob Storage 계정에 지원됩니다. 
- Resource Manager 배포 모델을 사용하여 만든 저장소 계정만이 지원되지 않습니다. 
- Azure 저장소 분석 로깅의 호출자 ID 정보에 대한 지원이 곧 제공됩니다.
- 표준 저장소 계정의 리소스에 대한 액세스 권한의 Azure AD 권한 부여는 현재 지원됩니다. Premium Storage 계정의 페이지 Blob에 대한 액세스 권한의 권한 부여는 곧 지원됩니다.
- Azure Storage는 기본 제공 및 사용자 지정 RBAC 역할을 지원합니다. 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐에 이르는 범위의 역할을 할당할 수 있습니다.
- 현재 Azure AD 통합을 지원하는 Azure Storage 클라이언트 라이브러리는 다음과 같습니다.
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [Blob, 큐 및 파일](https://github.com/Azure/azure-storage-python)
    - [Node.JS](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>저장소에 대 한 Azure AD의 개요

Azure Storage에서 Azure AD 통합을 사용하는 첫 번째 단계는 스토리지 데이터의 RBAC 역할을 Azure리소스에 대한 서비스 주체(사용자, 그룹 또는 응용 프로그램 서비스 주체) 또는 관리 ID에 할당하는 것입니다. RBAC 역할에는 컨테이너 및 큐에 대한 사용 권한의 공통 집합이 포함됩니다. Azure Storage에 대 한 RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 참조 하세요 [RBAC (미리 보기)를 사용 하 여 저장소 데이터에 대 한 관리 액세스 권한을](storage-auth-aad-rbac.md)합니다.

Azure AD를 사용하여 애플리케이션에서 저장소 리소스에 대한 액세스 권한을 부여하려면 코드에서 OAuth 2.0 액세스 토큰을 요청해야 합니다. 액세스 토큰을 요청하고 사용하여 Azure Storage에 대한 요청에 권한을 부여하는 방법을 알아보려면 [Azure Storage 애플리케이션에서 Azure AD를 사용하여 인증(미리 보기)](storage-auth-aad-app.md)을 참조하세요. 관리 ID를 사용 중인 경우 [Azure 리소스의 Azure 관리 ID를 사용하여 Blob 및 큐에 대한 액세스 인증(미리 보기)](storage-auth-aad-msi.md)을 참조하세요.

이제 Azure CLI 및 PowerShell에서는 Azure AD ID를 사용하여 로그인하도록 지원합니다. Azure AD ID를 사용하여 로그인하면 세션이 해당 ID에서 실행됩니다. 자세히 알아보려면 [Azure AD ID를 사용하여 CLI 또는 PowerShell을 통해 Azure Storage에 액세스(미리 보기)](storage-auth-aad-script.md)를 참조하세요.

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 및 큐의 RBAC 역할

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 컨테이너 또는 큐에 액세스하는 데 사용되는 사용 권한의 공통 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의합니다. 

RBAC 역할에는 Azure AD 보안 주체에 할당 된 Azure 부여 해당 보안 주체에 대 한 해당 리소스에 액세스 합니다. 액세스 권한은 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. 사용자, 그룹, 응용 프로그램 서비스 주체를 Azure AD 보안 주체 수 또는 [Azure 리소스에 대 한 id 관리](../../active-directory/managed-identities-azure-resources/overview.md)합니다. 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

Azure portal에서 기본 제공 역할을 할당 하는 방법에 알아보려면 참조 [Azure 컨테이너 및 Azure portal (미리 보기)에서 RBAC 사용 하 여 큐 액세스 권한을 부여할](storage-auth-aad-rbac.md)합니다.

### <a name="access-permissions-granted-by-rbac-roles"></a>RBAC 역할에 의해 부여 된 권한 

다음 표에서 서로 다른 수준의 범위에 대 한 기본 제공 역할에 의해 부여 된 액세스 권한:

|범위|Blob 데이터 소유자|Blob 데이터 기여자|Blob 데이터 판독기|큐 데이터 기여자|큐 데이터 판독기|
|---|---|---|---|---|---|
|구독 수준|구독의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한 및 POSIX 액세스 제어 관리|구독의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한| 구독의 모든 컨테이너 및 Blob에 대한 읽기 권한|구독의 모든 큐에 대한 읽기/쓰기 권한|구독의 모든 큐에 대한 읽기 권한|
|리소스 그룹 수준|리소스 그룹의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한 및 POSIX 액세스 제어 관리|리소스 그룹의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한|리소스 그룹의 모든 컨테이너 및 Blob에 대한 읽기 권한|리소스 그룹의 모든 큐에 대한 읽기/쓰기 권한|리소스 그룹의 모든 큐에 대한 읽기 권한|
|스토리지 계정 수준|스토리지 계정의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한 및 POSIX 액세스 제어 관리|저장소 계정의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한|저장소 계정의 모든 컨테이너 및 Blob에 대한 읽기 권한|저장소 계정의 모든 큐에 대한 읽기/쓰기 권한|저장소 계정의 모든 큐에 대한 읽기 권한|
|컨테이너/큐 수준|지정한 컨테이너 및 해당 Blob에 대한 읽기/쓰기 권한 및 POSIX 액세스 제어 관리|지정된 컨테이너 및 해당 Blob에 대한 읽기/쓰기 권한|지정된 컨테이너 및 해당 Blob에 대한 읽기 권한|지정된 큐에 대한 읽기/쓰기 권한|지정된 큐에 대한 읽기 권한|

각 Azure Storage 작업을 호출하는 데 필요한 사용 권한에 대한 자세한 내용은 [REST 작업 호출을 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Blob 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물 [Azure Storage에 대한 Azure AD 인증 미리 보기 발표](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)를 참조하세요.
