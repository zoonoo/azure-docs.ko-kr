---
title: Azure VM 관리 서비스 ID(미리 보기)에서 Azure AD를 사용하여 인증 | Microsoft Docs
description: Azure VM 관리 서비스 ID(미리 보기)에서 Azure AD를 사용하여 인증합니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 83d3a2d973604e3b8a709b24cabcb3abba1e304c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660297"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Azure 관리 서비스 ID(미리 보기)에서 Azure AD를 사용하여 인증

Azure Storage는 [관리 서비스 ID](../../active-directory/managed-service-identity/overview.md)를 사용하여 Azure AD(Azure Active Directory) 인증을 지원합니다. MSI(관리 서비스 ID)는 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 제공합니다. Azure 가상 머신, 함수 앱, 가상 머신 확장 집합 등에서 실행되는 응용 프로그램에서 MSI를 사용하여 Azure Storage를 인증할 수 있습니다. MSI를 사용하고 Azure AD 인증 기능을 활용하면 클라우드에서 실행되는 응용 프로그램에 자격 증명을 저장할 필요가 없습니다.  

저장소 컨테이너 또는 큐에 대한 관리 서비스 ID에 권한을 부여하려면 저장소 권한이 포함된 RBAC 역할을 MSI에 할당합니다. 저장소의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요. 

> [!IMPORTANT]
> 이 미리 보기는 프로덕션 이외의 용도로만 사용해야 합니다. Azure Storage에 대한 Azure AD 통합이 일반 공급 버전으로 선언되어야만 프로덕션 SLA(서비스 수준 계약)를 사용할 수 있습니다. 사용자 시나리오에서 Azure AD 통합이 아직 지원되지 않는 경우, 응용 프로그램에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다. 미리 보기에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 액세스 인증(미리 보기)](storage-auth-aad.md)을 참조하세요.
>
> 미리 보기 동안 RBAC 역할 할당이 전파되는 데 최대 5분이 걸릴 수 있습니다.

이 문서에서는 Azure VM에서 MSI를 사용하여 Azure Storage를 인증하는 방법에 대해 설명합니다.  

## <a name="enable-msi-on-the-vm"></a>VM에서 MSI 사용

MSI를 사용하여 VM에서 Azure Storage를 인증하려면 먼저 VM에서 MSI를 사용하도록 설정해야 합니다. MSI를 사용하도록 설정하는 방법은 다음 문서 중 하나를 참조하세요.

- [Azure Portal](https://docs.microsoft.com/en-us/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>MSI 액세스 토큰 가져오기

MSI를 사용하여 인증하려면 응용 프로그램 또는 스크립트에서 MSI 액세스 토큰을 획득해야 합니다. 액세스 토큰을 획득하는 방법을 알아보려면 [토큰 획득을 위해 Azure VM MSI(관리 서비스 ID)를 사용하는 방법](../../active-directory/managed-service-identity/how-to-use-vm-token.md)을 참조하세요.

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

코드 예제에서는 MSI 액세스 토큰이 있다고 가정합니다. 액세스 토큰은 관리 서비스 ID에 권한을 부여하여 블록 Blob을 만드는 데 사용됩니다.

### <a name="add-references-and-using-statements"></a>참조 추가 및 명령문 사용  

Visual Studio에서 Azure Storage 클라이언트 라이브러리의 미리 보기 버전을 설치합니다. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. 다음 명령을 콘솔에 입력합니다.

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

다음 using 문을 코드에 추가합니다.

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>MSI 액세스 토큰에서 자격 증명 만들기

블록 Blob을 만들려면 미리 보기 패키지에서 제공하는 **TokenCredentials** 클래스를 사용합니다. 앞에서 가져온 MSI 액세스 토큰을 전달하여 새 **TokenCredentials** 인스턴스를 만듭니다.

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure Storage와 Azure AD를 통합하려면 Azure Storage 작업에 HTTPS를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure Storage의 RBAC 역할에 대해 자세히 알아보려면 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.
- 저장소 응용 프로그램 내에서 컨테이너 및 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [저장소 응용 프로그램에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
- Azure AD ID를 사용하여 Azure CLI 및 PowerShell에 로그인하는 방법을 알아보려면 [Azure AD ID를 사용하여 CLI 또는 PowerShell을 통해 Azure Storage에 액세스(미리 보기)](storage-auth-aad-script.md)를 참조하세요.
- Azure Blob 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물인 [Azure Storage에 대한 Azure AD 인증 미리 보기 발표](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)를 참조하세요.
