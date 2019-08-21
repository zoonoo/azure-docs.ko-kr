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
ms.openlocfilehash: 060a84c06e65441381a0cf74f20fb4a9d7b0f042
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642150"
---
Blob 입력 바인딩에 대해 다음 매개 변수 형식을 사용할 수 있습니다.

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> *function.json*에서 `direction` 또는 C# 클래스 라이브러리에서 `FileAccess.ReadWrite`의 “inout” 바인딩이 필요합니다.

스토리지 SDK 형식 중 하나에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

`string` 또는 `Byte[]`에 바인딩하는 방식은 전체 Blob 내용이 메모리에 로드되므로 Blob 크기가 작은 경우에만 권장됩니다. 일반적으로 `Stream` 또는 `CloudBlockBlob` 형식을 사용하는 것이 좋습니다. 자세한 내용은 이 문서의 앞부분에 나오는 [동시성 및 메모리 사용량](#trigger---concurrency-and-memory-usage)을 참조하세요.
