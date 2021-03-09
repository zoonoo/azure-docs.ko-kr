---
author: baanders
description: Azure Digital Twins 샘플에 DefaultAzureCredential에 대 한 포함 파일-참고
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473672"
---
>[!NOTE]
> 이 샘플은 로컬 컴퓨터에서 실행할 때 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)(`Azure.Identity` 라이브러리의 일부)을 사용하여 Azure Digital Twins 인스턴스에서 사용자를 인증합니다. 이 인증 유형을 사용 하는 샘플은 로컬 [Azure CLI](/cli/azure/install-azure-cli) 또는 Visual Studio/Visual Studio Code의 로그인과 같은 로컬 환경 내에서 Azure 자격 증명을 검색 합니다.
>
> `DefaultAzureCredential`및 기타 인증 옵션을 사용 하는 방법에 대 한 자세한 내용은 [*방법: 앱 인증 코드 작성*](../articles/digital-twins/how-to-authenticate-client.md)을 참조 하세요.