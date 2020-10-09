---
title: 매핑 데이터 흐름의 여러 분기
description: 여러 분기를 사용 하는 매핑 데이터 흐름에서 데이터 스트림 복제
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: daebd6a4a00d82706ceda68f94736f21b8fe5a84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606405"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>매핑 데이터 흐름에서 새 분기 만들기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

동일한 데이터 스트림에 대해 여러 작업 및 변환 집합을 수행 하는 새 분기를 추가 합니다. 새 분기를 추가 하는 것은 여러 싱크에 대해 동일한 소스를 사용 하거나 자체 조인 데이터를 함께 사용 하려는 경우에 유용 합니다.

다른 변환과 유사 하 게 변환 목록에서 새 분기를 추가할 수 있습니다. 분기 하려는 변환 뒤에 기존 변환이 있는 경우에만 **새 분기** 를 작업으로 사용할 수 있습니다.

![새 분기 추가](media/data-flow/new-branch2.png "새 분기 추가")

아래 예제에서 데이터 흐름은 taxi 여행 데이터를 읽고 있습니다. 일과 공급 업체 모두에 의해 집계 된 출력이 필요 합니다. 동일한 원본에서 읽는 두 개의 개별 데이터 흐름을 만드는 대신 새 분기를 추가할 수 있습니다. 이러한 방식으로 두 집계가 동일한 데이터 흐름의 일부로 실행 될 수 있습니다. 

![새 분기 추가](media/data-flow/new-branch.png "새 분기 추가")
