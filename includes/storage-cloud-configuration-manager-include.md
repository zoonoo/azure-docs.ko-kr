---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557317"
---
[.NET용 Microsoft Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 는 구성 파일에서 연결 문자열을 구문 분석하기 위해 클래스를 제공합니다. [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) 클래스는 구성 설정을 구문 분석 합니다. 데스크톱, 모바일 장치, Azure 가상 머신 또는 Azure 클라우드 서비스에서 실행 되는 클라이언트 응용 프로그램에 대 한 설정을 구문 분석 합니다.

패키지를 참조 하려면 `CloudConfigurationManager` 다음 지시문을 추가 합니다 `using` .

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

구성 파일에서 연결 문자열 검색하는 방법을 보여 주는 예제는 다음과 같습니다.

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Azure 구성 관리자 사용은 선택 사항입니다. .NET Framework의 [ConfigurationManager 클래스](/dotnet/api/system.configuration.configurationmanager)와 같은 API를 사용할 수도 있습니다.