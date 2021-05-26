---
author: baanders
description: Azure Digital Twins 샘플에서 DefaultAzureCredential에 대한 파일 포함 - 참고
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 1275e7e230fedc4fae77592bf1232e62ca757b8b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078935"
---
>[!NOTE]
> 이 샘플은 로컬 컴퓨터에서 실행할 때 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true)(`Azure.Identity` 라이브러리의 일부)을 사용하여 Azure Digital Twins 인스턴스에서 사용자를 인증합니다. 이 형식의 인증을 사용하면 샘플은 로컬 [Azure CLI](/cli/azure/install-azure-cli) 또는 Visual Studio/Visual Studio Code의 로그인과 같은 로컬 환경 내에서 Azure 자격 증명을 검색합니다.
>
> `DefaultAzureCredential` 및 기타 인증 옵션을 사용하는 방법에 대한 자세한 내용은 [방법: 앱 인증 코드 작성](../articles/digital-twins/how-to-authenticate-client.md)을 참조하세요.