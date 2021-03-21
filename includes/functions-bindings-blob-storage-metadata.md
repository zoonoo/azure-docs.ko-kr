---
title: 파일 포함
description: 파일 포함
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455920"
---
Blob 트리거는 몇 가지 메타데이터 속성을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 값은 [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob) 형식과 동일한 의미 체계를 가집니다.

|속성  |Type  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Blob을 트리거하는 경로입니다.|
|`Uri`|`System.Uri`|기본 위치에 대한 Blob의 URI입니다.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blob의 시스템 속성입니다. |
|`Metadata` |`IDictionary<string,string>`|Blob에 대한 사용자 정의 메타데이터입니다.|

예를 들어, 다음 C# 스크립트 및 JavaScript 예제는 컨테이너를 포함하는 트리거 Blob의 경로를 로깅합니다.

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```