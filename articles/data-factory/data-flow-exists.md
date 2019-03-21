---
title: Azure Data Factory Mapping Data Flow 있음 변환
description: Azure Data Factory Mapping Data Flow 있음 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734492"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory Mapping Data Flow 있음 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

있음 변환은 데이터의 행을 중지하거나 통과 허용하는 행 필터링 변환입니다. 있음 변환은 ```SQL WHERE EXISTS``` 및 ```SQL WHERE NOT EXISTS```와 비슷합니다. 필터 변환 후 데이터 스트림의 결과 행에는 원본 1의 열 값이 원본 2에 있거나 원본 2에 없는 모든 행이 포함됩니다.

![있음 설정](media/data-flow/exsits.png "있음 1")

Data Flow가 스트림 1의 값을 스트림 2와 비교할 수 있도록 있음의 두 번째 원본을 선택합니다.

있음 또는 없음에 대해 값을 확인하려는 원본 1과 원본 2의 열을 선택합니다.
