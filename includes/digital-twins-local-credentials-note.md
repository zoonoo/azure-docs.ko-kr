---
author: baanders
description: Azure Digital Twins 샘플에 DefaultAzureCredential에 대 한 포함 파일-참고
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494473"
---
>[!NOTE]
> 이 샘플에서는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (라이브러리의 일부 `Azure.Identity` )를 사용 하 여 로컬 컴퓨터에서 실행할 때 Azure Digital twins 인스턴스를 사용 하 여 사용자를 인증 합니다. 이 인증 유형을 사용 하는 샘플은 로컬 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 또는 Visual Studio/Visual Studio Code의 로그인과 같은 로컬 환경 내에서 Azure 자격 증명을 검색 합니다.
>
> `DefaultAzureCredential`및 기타 인증 옵션을 사용 하는 방법에 대 한 자세한 내용은 [*방법: 앱 인증 코드 작성*](../articles/digital-twins/how-to-authenticate-client.md)을 참조 하세요.