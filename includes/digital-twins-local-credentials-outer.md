---
author: baanders
description: 포함 파일 - Azure Digital Twins 샘플에서 DefaultAzureCredential에 대한 로컬 인증 설정(소개 있음)
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 2d1a1636ca1ce7ea3bacaa872178d100ec2536ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437678"
---
### <a name="set-up-local-azure-credentials"></a>로컬 Azure 자격 증명 설정

이 샘플은 로컬 컴퓨터에서 실행할 때 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true)(`Azure.Identity` 라이브러리의 일부)을 사용하여 Azure Digital Twins 인스턴스에서 사용자를 인증합니다. 클라이언트 앱에서 Azure Digital Twins를 사용하여 인증하는 여러 방법에 대한 자세한 내용은 [앱 인증 코드 작성](../articles/digital-twins/how-to-authenticate-client.md)을 참조하세요.

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]