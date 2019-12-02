---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666168"
---
## <a name="install-client-library-packages"></a>클라이언트 라이브러리 패키지 설치

> [!NOTE]
> 여기에 표시 된 예제에서는 Azure Storage 클라이언트 라이브러리 버전 12를 사용 합니다. 버전 12 클라이언트 라이브러리는 Azure SDK의 일부입니다. Azure SDK에 대 한 자세한 내용은 [GitHub](https://github.com/Azure/azure-sdk)의 azure sdk 리포지토리를 참조 하세요.

Blob storage 패키지를 설치 하려면 NuGet 패키지 관리자 콘솔에서 다음 명령을 실행 합니다.

```powershell
Install-Package Azure.Storage.Blobs
```

여기에 표시 된 예제에서는 Azure AD 자격 증명을 사용 하 여 인증 하기 위해 최신 버전의 [.net 용 Azure id 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Identity/) 도 사용 합니다. 패키지를 설치 하려면 NuGet 패키지 관리자 콘솔에서 다음 명령을 실행 합니다.

```powershell
Install-Package Azure.Identity
```

Azure Storage에서 Azure Id 클라이언트 라이브러리를 사용 하 여 인증 하는 방법에 대 한 자세한 내용은 azure [리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)에서 **azure id 라이브러리를 사용** 하 여 인증 섹션을 참조 하세요.