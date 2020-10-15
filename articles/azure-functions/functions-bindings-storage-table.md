---
title: Azure Functions의 Azure Table Storage 바인딩
description: Azure Functions에서 Azure Table Storage 바인딩을 사용하는 방법을 이해합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096728"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Functions의 Azure Table Storage 바인딩

Azure Functions는 [트리거와 바인딩을](./functions-triggers-bindings.md)통해 [Azure Storage](../storage/index.yml) 와 통합 됩니다. 테이블 저장소와 통합 하면 테이블 저장소 데이터를 읽고 쓰는 함수를 작성할 수 있습니다.

| 작업 | 형식 |
|---------|---------|
| 함수의 테이블 저장소 데이터 읽기 | [입력 바인딩](./functions-bindings-storage-table-input.md) |
| 함수가 테이블 저장소 데이터를 쓰도록 허용 |[출력 바인딩](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

Table Storage 바인딩은 [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 패키지 버전 3.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Table Storage 바인딩은 [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x에서 제공됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>다음 단계

- [함수가 실행 될 때 테이블 저장소 데이터 읽기](./functions-bindings-storage-table-input.md)
- [함수에서 테이블 저장소 데이터 쓰기](./functions-bindings-storage-table-output.md)
