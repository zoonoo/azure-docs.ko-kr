---
title: Azure Data Factory Data Flow 파생 열 변환
description: Azure Data Factory Data Flow 파생 열 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 1335b86431bcf345f452ead0c95926d295ae185f
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728763"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Azure Data Factory Data Flow 파생 열 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

파생 열 변환을 사용하여 데이터 흐름에 새 열을 생성하거나 기존 필드를 수정합니다.

![열 파생](media/data-flow/dc1.png "파생 열")

단일 파생 열 변환에서 여러 개의 파생 열 작업을 수행할 수 있습니다. 단일 변환 단계에서 두 개 이상의 열을 변환하려면 "열 추가"를 클릭하십시오.

열 필드에서 기존 열을 선택하여 새로 파생된 값으로 덮어쓰거나 "새 열 만들기"를 클릭하여 새로 파생된 값으로 새 열을 생성합니다.

식 텍스트 상자는 식 작성기를 열어 식 함수를 사용하여 파생 열에 대한 식을 빌드할 수 있습니다.
