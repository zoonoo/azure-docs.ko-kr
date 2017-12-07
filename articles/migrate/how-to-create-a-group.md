---
title: "Azure Migrate를 사용하여 평가할 컴퓨터 그룹화 | Microsoft Docs"
description: "Azure Migrate 서비스를 사용하여 평가를 실행하기 전에 컴퓨터를 그룹화하는 방법을 설명합니다."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>평가할 컴퓨터 그룹화

이 문서에서는 [Azure Migrate](migrate-overview.md)에서 평가할 컴퓨터 그룹을 만드는 방법에 대해 설명합니다. Azure Migrate는 Azure로 마이그레이션하는 데 적합한지를 확인하기 위해 그룹에 있는 컴퓨터를 평가하고, Azure에서 컴퓨터를 실행하기 위한 크기 조정 및 예상 비용을 예측합니다.


## <a name="create-a-group"></a>그룹 만들기

1. Azure Migrate 프로젝트의 **대시보드**에서  **그룹** > **+ 그룹**을 차례로 클릭하고 그룹 이름을 지정합니다.
2. 그룹에 컴퓨터를 하나 이상 추가하고  **만들기**를 클릭합니다. 
3. 필요에 따라 그룹에 대해 새 평가를 실행하도록 선택할 수 있습니다. 

    ![그룹 만들기](./media/how-to-create-a-group/create-group.png)

그룹을 만든 후에는 **그룹** 페이지에서 그룹을 선택하고 컴퓨터를 추가하거나 제거하여 그룹을 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [컴퓨터 종속성 매핑](how-to-create-group-machine-dependencies.md)을 사용하여 더 자세한 그룹을 만드는 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
