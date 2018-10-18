---
title: Azure 리소스(미리 보기)의 Azure Active Directory 관리 ID로 Blob 및 큐에 대한 액세스 인증 - Azure Storage | Microsoft Docs
description: Azure Blob 및 Queue Storage는 Azure 리소스의 관리 ID를 사용하여 Azure Active Directory 인증을 지원합니다. Azure 리소스의 관리 ID를 사용하여 Azure Virtual Machine, 기능 앱, Virtual Machine Scale Set 및 기타 기능에서 실행 중인 응용 프로그램의 Blob 및 큐에 대한 액세스를 인증할 수 있습니다. Azure 리소스의 관리 ID를 사용하고 Azure AD 인증 기능을 활용하면 클라우드에서 실행되는 응용 프로그램에 자격 증명을 저장할 필요가 없습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/05/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 67e0731c1f10bb635baa4e0d1a26dce0a336b555
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44090358"
---
# <a name="authenticate-access-to-blobs-and-queues-with-azure-managed-identities-for-azure-resources-preview"></a>Azure 리소스(미리 보기)의 Azure 관리 ID를 사용하여 Blob 및 큐에 대한 액세스 인증

Azure Blob 및 Queue Storage는 [Azure 리소스에 대한 관리 ID를 사용](../../active-directory/managed-identities-azure-resources/overview.md)하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스의 관리 ID를 사용하여 Azure VM(Virtual Machine), 기능 앱, Virtual Machine Scale Set 및 기타 기능에서 실행 중인 응용 프로그램의 Blob 및 큐에 대한 액세스를 인증할 수 있습니다. Azure 리소스의 관리 ID를 사용하고 Azure AD 인증 기능을 활용하면 클라우드에서 실행되는 응용 프로그램에 자격 증명을 저장할 필요가 없습니다.  

관리 ID에 대한 사용 권한을 Blob 컨테이너 또는 큐에 부여하려면 적절한 범위에서 해당 리소스에 대한 사용 권한을 포함하는 관리 ID에 역할 기반 액세스 제어 (RBAC) 역할을 할당합니다. 저장소의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요. 

이 문서에서는 Azure VM에서 관리 ID를 사용하여 Azure Blob 또는 Queue Storage를 인증하는 방법에 대해 설명합니다.  

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure 리소스의 관리 ID를 사용하여 VM에서 Blob 및 큐에 대한 액세스를 인증하려면 먼저 VM에서 Azure 리소스의 관리 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="get-a-managed-identity-access-token"></a>관리 ID 액세스 토큰 가져오기

관리 ID로 인증하려면 응용 프로그램 또는 스크립트가 관리 ID 액세스 토큰을 가져와야 합니다. 액세스 토큰을 가져오는 방법에 대해 알아보려면 [Azure VM에서 Azure 리소스의 관리 ID를 사용하여 액세스 토큰을 가져오는 방법](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)을 참조하세요.

## <a name="net-code-example-create-a-block-blob"></a>.NET 코드 예제: 블록 Blob 만들기

코드 예제에서는 관리 ID 액세스 토큰이 있다고 가정합니다. 액세스 토큰은 관리 서비스 ID에 권한을 부여하여 블록 Blob을 만드는 데 사용됩니다.

### <a name="add-references-and-using-statements"></a>참조 추가 및 명령문 사용  

Visual Studio에서 Azure Storage 클라이언트 라이브러리의 미리 보기 버전을 설치합니다. **도구** 메뉴에서 **Nuget 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 선택합니다. 다음 명령을 콘솔에 입력합니다.

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage  
```

다음 using 문을 코드에 추가합니다.

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-managed-identity-access-token"></a>관리 ID 액세스 토큰에서 자격 증명 만들기

블록 Blob을 만들려면 미리 보기 패키지에서 제공하는 **TokenCredentials** 클래스를 사용합니다. 앞에서 가져온 관리 ID 액세스 토큰을 전달하여 새 **TokenCredentials** 인스턴스를 만듭니다.

```dotnet
// Create storage credentials from your managed identity access token.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Azure Storage와 Azure AD를 통합하려면 Azure Storage 작업에 HTTPS를 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure Storage의 RBAC 역할에 대한 자세한 내용은 [RBAC를 사용하여 저장소 데이터에 대한 액세스 권한 관리(미리 보기)](storage-auth-aad-rbac.md)를 참조하세요.
- 저장소 응용 프로그램 내에서 컨테이너 및 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [저장소 응용 프로그램에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
- Azure AD ID를 사용하여 Azure CLI 및 PowerShell에 로그인하는 방법을 알아보려면 [Azure AD ID를 사용하여 CLI 또는 PowerShell을 통해 Azure Storage에 액세스(미리 보기)](storage-auth-aad-script.md)를 참조하세요.
- Azure Blob 및 큐의 Azure AD 통합에 대한 자세한 내용은 Azure Storage 팀 블로그 게시물 [Azure Storage에 대한 Azure AD 인증 미리 보기 발표](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)를 참조하세요.
