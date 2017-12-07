---
title: "Azure Migrate에서 그룹 종속성 매핑을 사용하여 평가 그룹 구체화 | Microsoft Docs"
description: "Azure Migrate 서비스에서 그룹 종속성 매핑을 사용하여 평가를 구체화하는 방법을 설명합니다."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>그룹 종속성 매핑을 사용하여 그룹 구체화

이 문서에서는 [Azure Migrate](migrate-overview.md) 평가에 대한 그룹 종속성 매핑을 설정하는 방법에 대해 설명합니다. 일반적으로 이 방법은 평가를 실행하기 전에 그룹 종속성을 교차 확인하여 기존 그룹에 대한 설정을 구체화하려는 경우에 사용합니다. 그룹 종속성 매핑을 실행하려는 그룹에는 10개 이하의 컴퓨터만 포함되어 있어야 합니다.  

## <a name="modify-a-group"></a>그룹 수정

1. Azure Migrate 프로젝트의 **관리** 아래에서  **그룹**을 클릭하고 그룹을 선택합니다.
2. 그룹 페이지에서  **종속성 보기**를 클릭하여 그룹 종속성 맵을 엽니다. 

     ![그룹 보기](./media/how-to-create-group-dependencies/create-group.png)

3. 더 세부적인 종속성을 보려면 시간 범위를 클릭하여 수정합니다. 범위는 기본적으로 1시간입니다. 시간 범위를 수정하거나 시작 및 종료 날짜와 기간을 지정할 수 있습니다.
4. Ctrl+클릭을 사용하여 맵에서 컴퓨터를 선택합니다. 맵에서 컴퓨터를 추가하거나 제거합니다.
    - 검색된 컴퓨터만 추가할 수 있습니다.
    - 그룹에 컴퓨터를 추가하거나 제거하면 해당 그룹에 대한 이전의 평가가 무효화됩니다.
    - 그룹을 수정할 때 필요에 따라 새 평가를 만들 수 있습니다.
5. **확인**을 클릭하여 그룹을 저장합니다.

    ![추가 및 제거](./media/how-to-create-group-dependencies/add-remove.png)

그룹 종속성 맵에 표시되는 특정 컴퓨터의 종속성을 확인하려면 [컴퓨터 종속성 맵핑을 설정합니다](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>다음 단계

평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
