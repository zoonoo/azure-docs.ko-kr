---
title: 검사를 통해 삭제된 자산을 검색하는 방법
description: 이 문서는 Azure Purview 계정이 검사 중 삭제된 자산을 검색하는 방법을 설명합니다.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555383"
---
# <a name="how-scans-detect-deleted-assets"></a>검사를 통해 삭제된 자산을 검색하는 방법

이 문서는 Azure Purview에서 검사 결과를 사용하여 삭제된 자산을 검색하는 방법을 설명합니다.

## <a name="background-info"></a>배경 정보

Azure Purview 카탈로그는 검사하는 경우에만 데이터 저장소의 상태를 인식합니다. 카탈로그는 파일, 테이블 또는 컨테이너가 삭제되었는지 여부를 알 수 있도록 마지막 검사 출력과 현재 검사 출력을 비교합니다. 예를 들어 Azure Data Lake Storage Gen2 계정을 마지막으로 검사한 경우 *폴더1* 이라는 폴더를 포함한다고 가정합니다. 동일한 계정을 다시 검사하면 *폴더1* 이 누락됩니다. 따라서 카탈로그는 폴더가 삭제된 것으로 가정합니다.

## <a name="detecting-deleted-files"></a>삭제된 파일 검색

누락된 파일을 검색하는 논리는 동일한 사용자와 다른 사용자가 여러 검색을 수행하는 데 사용할 수 있습니다. 예를 들어 사용자가 A, B, C 폴더의 Data Lake Storage Gen2 데이터 저장소에서 일회성 검사를 실행한다고 가정합니다. 나중에 동일한 계정에 있는 다른 사용자가 동일한 데이터 저장소의 폴더 C, D 및 E에 대해 다른 일회성 검사를 실행합니다. C 폴더가 두 번 검사되었으므로 카탈로그는 이것이 삭제할 수 있는 것인지 확인합니다. 그러나 폴더 A, B, D 및 E는 한 번만 검사되었으며 카탈로그는 그것들이 삭제된 자산인지 확인하지 않습니다.

삭제된 파일을 카탈로그에서 유지하려면 일반 검사를 실행해야 합니다. 검사 간격은 중요합니다. 카탈로그는 다른 검사를 실행할 때까지 삭제된 자산을 검색할 수 없기 때문입니다. 따라서 특정 저장소에서 한 달에 한 번 검사를 실행하는 경우 나중에 한 달에 검사를 실행할 때까지 카탈로그는 해당 저장소에서 삭제된 데이터 자산을 검색할 수 없습니다.

Data Lake Storage Gen2 같은 대량 데이터 저장소를 열거하는 경우 정보가 누락될 수 있는 방법이 여러 가지(열거 오류 및 끊어진 이벤트 등) 있습니다. 특정 검사에서 파일이 만들어지거나 삭제되지 않았을 수 있습니다. 따라서 카탈로그는 특정 파일이 삭제된 것이 확실한 경우를 제외하고는 카탈로그에서 삭제하지 않습니다. 이 전략은 검사된 데이터 저장소에 없는 파일이 여전히 카탈로그에 있는 경우 오류가 발생할 수 있음을 의미합니다. 일부 경우에 데이터 저장소는 삭제된 특정 자산을 포착하기 전에 2-3번 검사해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Purview 카탈로그를 시작하려면 [빠른 시작: Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
