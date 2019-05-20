---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 761c3a9aecadd9c1eabdb586f95c47e2988720d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "65756354"
---
[.NET용 Microsoft Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) 는 구성 파일에서 연결 문자열을 구문 분석하기 위해 클래스를 제공합니다. [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) 클래스는 클라이언트 애플리케이션이 데스크톱, 모바일 장치, Azure 가상 머신 또는 Azure 클라우드 서비스 중 어디서 실행되는지에 관계없이 구성 설정을 구문 분석합니다.

CloudConfigurationManager 패키지를 참조하려면 다음 `using` 지시문을 추가합니다.

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage;
```

구성 파일에서 연결 문자열 검색하는 방법을 보여 주는 예제는 다음과 같습니다.

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Azure 구성 관리자 사용은 선택 사항입니다. 또한 .NET Framework의 [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) 클래스와 같은 API를 사용할 수 있습니다.

