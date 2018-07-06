---
title: RBAC를 사용하여 Azure Storage 컨테이너 및 큐에 대한 액세스 권한 관리(미리 보기) | Microsoft Docs
description: RBAC(역할 기반 액세스 제어)를 사용하여 사용자, 그룹, 응용 프로그램 서비스 사용자 또는 관리 서비스 ID에 Azure Storage 데이터에 대한 액세스 권한을 할당합니다. Azure Storage는 컨테이너 및 큐에 대한 액세스 권한에 기본 제공 및 사용자 지정 역할을 지원합니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/29/2018
ms.author: tamram
ms.openlocfilehash: 241808e0a7bde1d2c53cd0af1de677275c169214
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082233"
---
# <a name="manage-access-rights-to-azure-storage-data-with-rbac-preview"></a>RBAC를 사용하여 Azure Storage 데이터에 대한 액세스 권한 관리(미리 보기)

Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](https://docs.microsoft.com/azure/role-based-access-control/overview)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Storage는 컨테이너 또는 큐에 액세스하는 데 사용되는 사용 권한의 공통 집합을 포함하는 기본 제공 RBAC 역할 집합을 정의합니다. RBAC 역할이 Azure AD ID에 할당되면 ID에는 지정된 범위에 따라 해당 리소스에 대한 액세스 권한이 부여됩니다. 액세스 권한은 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure Portal, Azure 명령줄 도구 또는 Azure Management API를 사용하여 Azure Storage 리소스에 액세스 권한을 할당할 수 있습니다. 

Azure AD ID는 사용자, 그룹 또는 응용 프로그램 서비스 주체일 수도 있고, *관리 서비스 ID*일 수도 있습니다. 보안 주체는 사용자, 그룹 또는 응용 프로그램 서비스 사용자일 수 있습니다. [관리 서비스 ID](../../active-directory/managed-service-identity/overview.md)는 Azure 가상 머신, 기능 앱, 가상 머신 확장 집합 및 기타 기능에서 실행 중인 응용 프로그램에서 인증하는 데 사용되는 자동으로 관리되는 ID입니다. Azure AD에서 ID는 [Azure ID 솔루션 이해](https://docs.microsoft.com/en-us/azure/active-directory/understand-azure-identity-solutions)를 참조하세요.

## <a name="rbac-roles-for-azure-storage"></a>Azure Storage에 대한 RBAC 역할

Azure Storage는 기본 제공 및 사용자 지정 RBAC 역할을 지원합니다. Azure Storage는 Azure AD에 사용하기 위해 이러한 기본 제공 RBAC 역할을 제공합니다.

- [Storage Blob 데이터 기여자(미리 보기)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor-preview)
- [Storage Blob 데이터 판독기(미리 보기)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)
- [Storage 큐 데이터 기여자(미리 보기)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)
- [Storage 큐 데이터 판독기(미리 보기)](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-queue-data-reader-preview)

기본 제공 역할을 Azure Storage에 정의하는 방법에 대한 자세한 내용은 [역할 정의 이해](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#management-and-data-operations-preview)를 참조하세요.

또한 컨테이너 및 큐와 함께 사용하기 위해 사용자 지정 역할을 정의할 수 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles.md)을 참조하세요. 

> [!IMPORTANT]
> 이 미리 보기는 프로덕션 이외 용도로 사용해야 합니다. Azure Storage에 대한 Azure AD 통합이 일반 공급 버전으로 선언되어야만 프로덕션 SLA(서비스 수준 계약)를 사용할 수 있습니다. 사용자 시나리오에서 Azure AD 통합이 아직 지원되지 않는 경우, 응용 프로그램에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다. 미리 보기에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증(미리 보기)](storage-auth-aad.md)을 참조하세요.
>
> 미리 보기 동안 RBAC 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.

## <a name="assign-a-role-to-a-security-principal"></a>보안 주체에 역할 할당

Azure ID에 RBAC 역할을 할당하여 저장소 계정의 컨테이너 또는 큐에 사용 권한을 부여합니다. 저장소 계정 또는 특정 컨테이너나 큐에 대한 역할 할당의 범위를 지정할 수 있습니다. 다음 표에서는 범위에 따라 기본 제공 역할에서 부여한 액세스 권한을 요약합니다. 

|                                 |     Blob 데이터 기여자                                                 |     Blob 데이터 판독기                                                |     큐 데이터 기여자                                  |     큐 데이터 판독기                                 |
|---------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------|----------------------------------------------------------|
|    구독으로 범위가 지정됨       |    구독의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한       |    구독의 모든 컨테이너 및 Blob에 대한 읽기 권한       |    구독의 모든 큐에 대한 읽기/쓰기 권한       |    구독의 모든 큐에 대한 읽기 권한         |
|    리소스 그룹으로 범위 지정됨     |    리소스 그룹의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한     |    리소스 그룹의 모든 컨테이너 및 Blob에 대한 읽기 권한     |    리소스 그룹의 모든 큐에 대한 읽기/쓰기 권한     |    리소스 그룹의 모든 큐에 대한 읽기 권한     |
|    저장소 계정으로 범위 지정됨    |    저장소 계정의 모든 컨테이너 및 Blob에 대한 읽기/쓰기 권한    |    저장소 계정의 모든 컨테이너 및 Blob에 대한 읽기 권한    |    저장소 계정의 모든 큐에 대한 읽기/쓰기 권한    |    저장소 계정의 모든 큐에 대한 읽기 권한    |
|    컨테이너/큐로 범위 지정됨    |    지정된 컨테이너 및 해당 Blob에 대한 읽기/쓰기 권한              |    지정된 컨테이너 및 해당 Blob에 대한 읽기 권한              |    지정된 큐에 대한 읽기/쓰기 권한                  |    지정된 큐에 대한 읽기 권한                    |

> [!NOTE]
> Azure Storage 계정 소유자인 경우 자동으로 데이터에 액세스할 수 있는 권한이 할당되지 않습니다. Azure Storage에 RBAC 역할을 직접 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 저장소 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.

각 Azure Storage 작업을 호출하는 데 필요한 사용 권한에 대한 자세한 내용은 [REST 작업 호출을 위한 권한](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)을 참조하세요.

다음 섹션에서는 저장소 계정 또는 개별 컨테이너로 범위가 지정된 역할을 할당하는 방법을 보여줍니다.

### <a name="assign-a-role-scoped-to-the-storage-account-in-the-azure-portal"></a>Azure Portal의 저장소 계정으로 범위가 지정된 역할 할당

Azure Portal의 저장소 계정에 있는 모든 컨테이너 또는 큐에 대한 액세스 권한을 부여하는 기본 제공 역할을 할당하려면:

1. [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동합니다.
2. 저장소 계정을 선택한 다음, **액세스 제어(IAM)** 를 선택하여 계정에 대한 액세스 제어 설정을 표시합니다. **추가** 단추를 클릭하여 새 역할을 추가합니다.

    ![저장소 액세스 제어 설정을 보여주는 스크린샷](media/storage-auth-aad-rbac/portal-access-control.png)

3. **사용 권한 추가** 창에서 Azure AD ID에 할당할 역할을 선택합니다. 그런 다음, 해당 역할을 할당하려는 ID를 찾기 위해 검색합니다. 예를 들어 다음 이미지에서는 사용자에게 할당된 **Storage Blob 데이터 판독기(미리 보기)** 역할을 보여줍니다.

    ![RBAC 역할을 할당하는 방법을 보여주는 스크린샷](media/storage-auth-aad-rbac/add-rbac-role.png)

4. **저장**을 클릭합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 추가된 사용자에게 이제 저장소 계정의 모든 Blob 데이터에 대한 읽기 권한이 있음을 보여줍니다.

    ![역할에 할당된 사용자의 목록을 보여주는 스크린샷](media/storage-auth-aad-rbac/account-scoped-role.png)

### <a name="assign-a-role-scoped-to-a-container-or-queue-in-the-azure-portal"></a>Azure Portal에서 컨테이너 또는 큐로 범위가 지정된 역할 할당

컨테이너 또는 큐로 범위가 지정된 기본 제공 역할을 할당하는 단계는 유사합니다. 여기에 표시된 프로시저는 컨테이너로 범위가 지정된 역할을 할당하지만 큐로 범위가 지정된 역할을 할당하기 위해 동일한 단계를 수행할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 저장소 계정으로 이동하고 계정에 대한 **개요**를 표시합니다.
2. Blob 서비스에서 **Blob 찾아보기**를 선택합니다. 
3. 역할을 할당하려는 컨테이너를 찾아 컨테이너의 설정을 표시합니다. 
4. **액세스 제어(IAM)** 를 선택하여 컨테이너에 대한 액세스 제어 설정을 표시합니다.
5. **사용 권한 추가** 창에서 Azure AD ID에 할당하려는 역할을 선택합니다. 그런 다음, 해당 역할을 할당하려는 ID를 찾기 위해 검색합니다.
6. **저장**을 클릭합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 추가된 사용자에게 이제 *sample-container*라는 컨테이너의 데이터에 대한 읽기 권한이 있음을 보여줍니다.

    ![역할에 할당된 사용자의 목록을 보여주는 스크린샷](media/storage-auth-aad-rbac/container-scoped-role.png)

## <a name="next-steps"></a>다음 단계

- RBAC에 대한 자세한 내용은 [역할 기반 액세스 제어 시작](../../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 RBAC 역할 할당을 할당하고 관리하는 방법을 알아보려면 다음과 같은 아티클을 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../../role-based-access-control/role-assignments-rest.md)
- 저장소 응용 프로그램 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [Azure Storage 응용 프로그램에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
- Azure 컨테이너 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물 [Azure Storage에 대한 Azure AD 인증 미리 보기 발표](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)를 참조하세요.
- 