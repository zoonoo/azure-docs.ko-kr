---
title: Azure Data Factory의 파워 쿼리 작업
description: Data Factory 파이프라인의 데이터 랭 글 링 기능에 파워 쿼리 활동을 사용 하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385458"
---
# <a name="power-query-activity-in-data-factory"></a>데이터 팩터리의 파워 쿼리 작업

파워 쿼리 활동을 사용 하 여 Data Factory 파이프라인에서 대규모로 랭 글 링 데이터를 실행 하는 파워 쿼리 매시업를 빌드하고 실행할 수 있습니다. 새 리소스 메뉴 옵션 또는 파이프라인에 전원 작업을 추가 하 여 새 파워 쿼리 매시업를 만들 수 있습니다.

![팩터리 리소스 창에 파워 쿼리를 보여 주는 스크린샷](media/data-flow/power-query-wrangling.png)

이전에 Azure Data Factory의 데이터 랭 글 링 데이터 흐름 메뉴 옵션에서 작성 되었습니다. 새 파워 쿼리 작업에서 제작 하도록 변경 되었습니다. 파워 쿼리 매시업 편집기 내에서 직접 작업 하 여 대화형 데이터 탐색을 수행한 다음 작업을 저장할 수 있습니다. 완료 되 면 파워 쿼리 활동을 사용 하 여 파이프라인에 추가할 수 있습니다. Azure Data Factory은 Azure Data Factory의 데이터 흐름 Spark 환경을 사용 하 여 자동으로 규모를 확장 하 고 데이터 랭 글 링을 운영 합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>데이터 흐름 스크립트로 변환

파워 쿼리 작업을 사용 하 여 크기를 조정 하기 위해 Azure Data Factory는 ```M``` 스크립트를 데이터 흐름 스크립트로 변환 하므로 Azure Data Factory 데이터 흐름 Spark 환경을 사용 하 여 규모에 따라 파워 쿼리을 실행할 수 있습니다. 코드 없는 데이터 준비를 사용 하 여 랭 글 링 데이터 흐름을 작성 합니다. 사용 가능한 함수 목록은 [변환 함수](wrangling-functions.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Data Factory에서 파워 쿼리를](wrangling-tutorial.md) 사용 하 여 데이터 랭 글 링 개념에 대해 자세히 알아보세요.
