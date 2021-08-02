---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/01/2021
ms.author: glenga
ms.openlocfilehash: e65a628f846892294fdd9be693b89a535ac694a0
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901450"
---
## <a name="supported-versions"></a>지원되는 버전

Functions 런타임 버전은 .NET의 특정 버전과 호환됩니다. Functions 버전에 대해 자세히 알아보려면 [Azure Functions 런타임 버전 개요](../articles/azure-functions/functions-versions.md)를 참조하세요. In Process와 Out of Process(격리) 중 어떤 상태로 함수가 실행되는지에 따라 지원되는 버전이 달라집니다. 

다음 표에서는 특정 버전의 Functions에서 사용할 수 있는 가장 높은 수준의 .NET Core 또는 .NET Framework를 보여 줍니다. 

| Functions 런타임 버전 | In-Process<br/>([.NET 클래스 라이브러리](../articles/azure-functions/functions-dotnet-class-library.md)) | Out of Process<br/>([.NET 격리](../articles/azure-functions/dotnet-isolated-process-guide.md)) |
| ---- | ---- | --- |
| Functions 4.x<sup>1</sup> | .NET 6.0(미리 보기)| .NET 6.0(미리 보기) |
| Functions 3.x | .NET Core 3.1 | .NET 5.0 |
| Functions 2.x | .NET Core 2.1<sup>2</sup> | 해당 없음 |
| Functions 1.x | .NET Framework 4.8 | 해당 없음 |

<sup>1</sup> Azure Functions는 .NET 6.0의 미리 보기 릴리스에서 실행되는 함수를 사용해 볼 수 있도록 실험적 지원을 제공합니다. 이 시험판 버전은 공식적으로 지원되지 않습니다. 자세한 내용은 [Azure Functions v4 초기 미리 보기](https://aka.ms/functions-dotnet6earlypreview-wiki) 페이지를 참조하세요.  
<sup>2</sup> 자세한 내용은 [Functions v2.x 고려 사항](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations)을 참조하세요.   

특정한 이전 부 버전의 삭제를 비롯하여 Azure Functions 릴리스에 대한 최신 소식을 보려면 [Azure App Service 공지](https://github.com/Azure/app-service-announcements/issues)를 주기적으로 확인하세요.
