---
author: baanders
description: Azure Digital Twins를 사용하여 테넌트 사이의 제한에 대한 코드 솔루션을 설명하는 파일을 포함하기
ms.service: digital-twins
ms.topic: include
ms.date: 4/13/2021
ms.author: baanders
ms.openlocfilehash: 2702f3c70d9e6f1a0bdad8695414e2d5fab02411
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589379"
---
다음 예제에서는 `DefaultAzureCredential` 옵션에서 `InteractiveBrowserTenantId`에 대한 테넌트 ID 값을 설정하는 방법을 보여줍니다.

:::image type="content" source="../articles/digital-twins/media/troubleshoot-error-404/defaultazurecredentialoptions.png" alt-text="DefaultAzureCredentialOptions 메서드를 보여주는 코드의 스크린샷. InteractiveBrowserTenantId 값은 샘플 테넌트 ID 값으로 설정됩니다.":::

Visual Studio 및 Visual Studio Code 인증을 위한 테넌트 설정에 사용할 수 있는 유사한 옵션이 있습니다. 사용 가능한 옵션에 대한 자세한 내용은 [DefaultAzureCredentialOptions 설명서](/dotnet/api/azure.identity.defaultazurecredentialoptions?view=azure-dotnet&preserve-view=true)를 참조하세요.