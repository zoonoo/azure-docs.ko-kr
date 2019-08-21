---
title: 포함 파일
description: 포함 파일
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 3788fdb954917f28f64a0dfe035bed4ded0932d5
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642137"
---
Blob 트리거는 몇 가지 메타데이터 속성을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 값은 [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) 형식과 동일한 의미 체계를 가집니다.

|속성  |형식  |Description  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Blob을 트리거하는 경로입니다.|
|`Uri`|`System.Uri`|기본 위치에 대한 Blob의 URI입니다.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|Blob의 시스템 속성입니다. |
|`Metadata` |`IDictionary<string,string>`|Blob에 대한 사용자 정의 메타데이터입니다.|

예를 들어, 다음 C# 스크립트 및 JavaScript 예제는 컨테이너를 포함하는 트리거 Blob의 경로를 로깅합니다.

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```
