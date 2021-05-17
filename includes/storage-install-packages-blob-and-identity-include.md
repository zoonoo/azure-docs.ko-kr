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
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "74806588"
---
## <a name="install-client-library-packages"></a>클라이언트 라이브러리 패키지 설치

> [!NOTE]
> 여기에 표시된 예제는 Azure Storage 클라이언트 라이브러리 버전 12를 사용합니다. 버전 12 클라이언트 라이브러리는 Azure SDK의 일부입니다. Azure SDK에 대한 자세한 내용은 [GitHub](https://github.com/Azure/azure-sdk)의 Azure SDK 리포지토리를 참조하세요.

Blob 스토리지 패키지를 설치하려면 NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

```powershell
Install-Package Azure.Storage.Blobs
```

여기에 표시된 예제에서는 [.NET용 Azure ID 클라이언트 라이브러리](https://www.nuget.org/packages/Azure.Identity/)의 최신 버전을 사용하여 Azure AD 자격 증명으로 인증합니다. 패키지를 설치하려면 NuGet 패키지 관리자 콘솔에서 다음 명령을 실행합니다.

```powershell
Install-Package Azure.Identity
```
