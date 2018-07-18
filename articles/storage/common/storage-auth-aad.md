---
title: Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증(미리 보기) | Microsoft Docs
description: Azure Active Directory를 사용하여 Azure Storage에 대한 액세스를 인증합니다(미리 보기).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.openlocfilehash: 9a0782b96b45d27c9b7e603959ecadf5b2632064
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737649"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증(미리 보기)

Azure Storage는 Blob 및 큐 서비스에 대해 Azure AD(Active Directory)를 사용하는 인증 및 권한 부여를 지원합니다. Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 사용자, 그룹 또는 응용 프로그램 서비스 사용자에 대한 액세스 권한을 부여할 수 있습니다. 

Azure AD를 사용하여 Azure Storage에 액세스하는 응용 프로그램에 권한을 부여하면 다른 권한 부여 옵션보다 우월한 보안 및 편이성을 제공합니다. 응용 프로그램에서 공유 키 인증을 계속 사용할 수 있는 동안 Azure AD를 사용하면 코드에서 계정 액세스 키를 저장하지 않아도 됩니다. 마찬가지로 SAS(공유 액세스 서명)를 계속 사용하여 저장소 계정의 리소스에 세분화된 액세스 권한을 부여할 수 있습니다. 하지만 Azure AD에서는 SAS 토큰을 관리하거나 손상된 SAS를 해지하는 방법을 걱정할 필요 없이 유사한 기능을 제공합니다.

## <a name="about-the-preview"></a>미리 보기 정보

미리 보기에 대한 다음 사항을 주의하세요.

- Azure AD 통합은 미리 보기 상태인 Blob 및 큐 서비스에서만 지원됩니다.
- Azure AD 통합은 모든 공용 지역에서 GPv1, GPv2 및 Blob Storage 계정에 지원됩니다. 
- Resource Manager 배포 모델을 사용하여 만든 저장소 계정만이 지원되지 않습니다. 
- Azure 저장소 분석 로깅의 호출자 ID 정보에 대한 지원이 곧 제공됩니다.
- 표준 저장소 계정의 리소스에 대한 액세스 권한의 Azure AD 권한 부여는 현재 지원됩니다. 프리미엄 저장소 계정의 페이지 Blob에 대한 액세스 권한의 권한 부여는 곧 지원됩니다.
- Azure Storage는 기본 제공 및 사용자 지정 RBAC 역할을 지원합니다. 구독, 리소스 그룹, 저장소 계정 또는 개별 컨테이너나 큐에 이르는 범위의 역할을 할당할 수 있습니다.
- 현재 Azure AD 통합을 지원하는 Azure Storage 클라이언트 라이브러리는 다음과 같습니다.
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)(7.1.x-Preview 사용)
    - 파이썬
        - [Blob](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [큐](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs))

> [!IMPORTANT]
> 이 미리 보기는 프로덕션 이외 용도로 사용해야 합니다. Azure Storage에 대한 Azure AD 통합이 일반 공급 버전으로 선언되어야만 프로덕션 SLA(서비스 수준 계약)를 사용할 수 있습니다. 사용자 시나리오에서 Azure AD 통합이 아직 지원되지 않는 경우, 응용 프로그램에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다.
>
> 미리 보기 동안 RBAC 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.
>
> Azure Storage와 Azure AD를 통합하려면 Azure Storage 작업에 HTTPS를 사용해야 합니다.

## <a name="get-started-with-azure-ad-for-storage"></a>Storage에서 Azure AD 시작

Azure Storage에서 Azure AD 통합을 사용하는 첫 번째 단계는 저장소 데이터의 RBAC 역할을 서비스 사용자(사용자, 그룹 또는 응용 프로그램 서비스 사용자) 또는 MSI(관리 서비스 ID)에 할당하는 것입니다. RBAC 역할에는 컨테이너 및 큐에 대한 사용 권한의 공통 집합이 포함됩니다. Azure Storage의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.

Azure AD를 사용하여 응용 프로그램에서 저장소 리소스에 대한 액세스 권한을 부여하려면 코드에서 OAuth 2.0 액세스 토큰을 요청해야 합니다. 액세스 토큰을 요청하고 사용하여 Azure Storage에 대한 요청에 권한을 부여하는 방법을 알아보려면 [Azure Storage 응용 프로그램에서 Azure AD를 사용하여 인증(미리 보기)](storage-auth-aad-app.md)을 참조하세요. Azure MSI(관리 서비스 ID)를 사용하는 경우 [ Azure VM 관리 서비스 ID에서 Azure AD를 사용하여 인증(미리 보기)](storage-auth-aad-msi.md)을 참조하세요.

이제 Azure CLI 및 PowerShell에서는 Azure AD ID를 사용하여 로그인하도록 지원합니다. Azure AD ID를 사용하여 로그인하면 세션이 해당 ID에서 실행됩니다. 자세히 알아보려면 [Azure AD ID를 사용하여 CLI 또는 PowerShell을 통해 Azure Storage에 액세스(미리 보기)](storage-auth-aad-script.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Blob 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물 [Azure Storage에 대한 Azure AD 인증 미리 보기 발표](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)를 참조하세요.
