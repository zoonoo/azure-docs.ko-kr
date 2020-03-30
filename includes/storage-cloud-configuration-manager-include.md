---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72038185"
---
[.NET용 Microsoft Azure 구성 관리자 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) 는 구성 파일에서 연결 문자열을 구문 분석하기 위해 클래스를 제공합니다. [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) 클래스는 구성 설정을 구문 분석합니다. 데스크톱, 모바일 장치, Azure 가상 컴퓨터 또는 Azure 클라우드 서비스에서 실행되는 클라이언트 응용 프로그램에 대한 설정을 구문 분석합니다.

`CloudConfigurationManager` 패키지를 참조하려면 다음 `using` 지시문을 추가합니다.

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

Azure 구성 관리자 사용은 선택 사항입니다. .NET Framework의 [ConfigurationManager 클래스와](/dotnet/api/system.configuration.configurationmanager)같은 API를 사용할 수도 있습니다.
