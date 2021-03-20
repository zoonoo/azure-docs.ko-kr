---
title: 검사를 통해 삭제된 자산을 검색하는 방법
description: 이 문서에서는 Azure 부서의 범위 계정이 검색 중 삭제 된 자산을 검색 하는 방법을 설명 합니다.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 9b1515ef00355c831e161c01227678197792cc20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96555383"
---
# <a name="how-scans-detect-deleted-assets"></a>검사를 통해 삭제된 자산을 검색하는 방법

이 문서에서는 Azure 부서의 범위에서 스캔 결과를 사용 하 여 삭제 된 자산을 검색 하는 방법을 설명 합니다.

## <a name="background-info"></a>배경 정보

Azure 부서의 범위 catalog는 검색 하는 경우에만 데이터 저장소의 상태를 인식 합니다. 카탈로그는 파일, 테이블 또는 컨테이너가 삭제 되었는지 여부를 알 수 있도록 마지막 검사 출력과 현재 검색 출력을 비교 합니다. 예를 들어 Azure Data Lake Storage Gen2 계정을 마지막으로 검색 한 경우 *folder1* 라는 폴더를 포함 한다고 가정 합니다. 동일한 계정을 다시 검사 하면 *folder1* 가 누락 됩니다. 따라서 카탈로그는 폴더가 삭제 된 것으로 가정 합니다.

## <a name="detecting-deleted-files"></a>삭제 된 파일 검색

누락 된 파일을 검색 하는 논리는 동일한 사용자와 다른 사용자가 여러 검색을 수행 하는 데 사용할 수 있습니다. 예를 들어 사용자가 A, B, C 폴더의 Data Lake Storage Gen2 데이터 저장소에서 일회성 검색을 실행 한다고 가정 합니다. 나중에 동일한 계정에 있는 다른 사용자가 동일한 데이터 저장소의 폴더 C, D 및 E에 대해 다른 일회성 검색을 실행 합니다. C 폴더가 두 번 검색 되었으므로 카탈로그에서 가능한 삭제를 확인 합니다. 그러나 폴더 A, B, D 및 E는 한 번만 검색 되었으며 카탈로그는 삭제 된 자산을 확인 하지 않습니다.

삭제 된 파일을 카탈로그에서 유지 하려면 일반 검색을 실행 해야 합니다. 검색 간격은 중요 합니다. 카탈로그는 다른 검색을 실행할 때까지 삭제 된 자산을 검색할 수 없기 때문입니다. 따라서 특정 저장소에서 한 달에 한 번 검색을 실행 하는 경우 나중에 한 달에 검색을 실행할 때까지 카탈로그는 해당 저장소에서 삭제 된 데이터 자산을 검색할 수 없습니다.

Data Lake Storage Gen2 같은 대량 데이터 저장소를 열거 하는 경우 정보를 누락 하기 위해 여러 가지 방법 (열거 오류 및 삭제 된 이벤트 포함)이 있습니다. 특정 검색에서 파일이 만들어지거나 삭제 되지 않았을 수 있습니다. 따라서 카탈로그가 특정 파일을 삭제 한 경우를 제외 하 고는 카탈로그에서 삭제 하지 않습니다. 이 전략은 검색 된 데이터 저장소에 없는 파일이 여전히 카탈로그에 있는 경우 오류가 발생할 수 있음을 의미 합니다. 일부 경우에 데이터 저장소는 삭제 된 특정 자산을 포착 하기 전에 2-3 번 검사 해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 부서의 범위 카탈로그를 시작 하려면 [빠른 시작: 부서의 범위 계정 만들기](create-catalog-portal.md)를 참조 하세요.
