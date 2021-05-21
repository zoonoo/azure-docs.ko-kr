---
title: 매핑 데이터 흐름의 여러 분기
description: 여러 분기를 사용하는 매핑 데이터 흐름에서 데이터 스트림 복제
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a11dbfbd6d6510b5c421e54cd2547c3aedb1bfb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378199"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>매핑 데이터 흐름에서 새 분기 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

새 분기를 추가하여 동일한 데이터 스트림에 대해 여러 개의 작업 및 변환 세트를 수행할 수 있습니다. 여러 싱크에 대해 동일한 원본을 사용하거나 자체 조인 데이터를 함께 사용하려는 경우 새 분기를 추가하는 것이 유용합니다.

다른 변환과 유사하게 변환 목록에서 새 분기를 추가할 수 있습니다. 분기하려는 변환 뒤에 기존 변환이 있는 경우에만 **새 분기** 를 작업으로 사용할 수 있습니다.

![스크린샷은 다중 입력/출력 메뉴에 있는 새 분기 옵션을 보여줍니다.](media/data-flow/new-branch2.png "새 분기 추가")

아래 예제에서 데이터 흐름은 택시 여행 데이터를 읽고 있습니다. 일별 및 공급 업체별로 집계된 출력이 필요합니다. 동일한 원본에서 읽는 두 개의 개별 데이터 흐름을 만드는 대신 새 분기를 추가할 수 있습니다. 이렇게 하면 두 집계가 동일한 데이터 흐름의 일부로 실행될 수 있습니다. 

![스크린샷은 원본에서 두 개의 분기가 있는 데이터 흐름을 보여줍니다.](media/data-flow/new-branch.png "새 분기 추가")
