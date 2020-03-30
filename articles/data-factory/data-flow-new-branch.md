---
title: 매핑 데이터 흐름의 여러 분기
description: 여러 분기를 사용하여 데이터 흐름 매핑에서 데이터 스트림 복제
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834489"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>매핑 데이터 흐름에 새 분기 만들기

새 분기를 추가하여 동일한 데이터 스트림에 대해 여러 작업 및 변환 집합을 수행합니다. 새 분기를 추가하면 동일한 소스를 여러 싱크에 사용하거나 데이터를 함께 자체 조인하려는 경우에 유용합니다.

다른 변환과 유사한 변환 목록에서 새 분기를 추가할 수 있습니다. **새 분기는** 분기하려는 변환 다음에 기존 변환이 있는 경우에만 작업으로 사용할 수 있습니다.

![새 분기 추가](media/data-flow/new-branch2.png "새 분기 추가")

아래 예제에서 데이터 흐름은 택시 여행 데이터를 읽는 것입니다. 일 및 공급업체모두에 의해 집계된 출력이 필요합니다. 동일한 원본에서 읽는 두 개의 별도 데이터 흐름을 만드는 대신 새 분기를 추가할 수 있습니다. 이렇게 하면 동일한 데이터 흐름의 일부로 두 집계를 실행할 수 있습니다. 

![새 분기 추가](media/data-flow/new-branch.png "새 분기 추가")
