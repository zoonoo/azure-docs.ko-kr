---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/11/2021
ms.openlocfilehash: 42af7691d7401e397e7e0d9819fded60b3821b26
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109785321"
---
1. Azure 구독에 대한 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure Portal의 왼쪽 위 모서리에서 새 개의 막대를 선택한 다음, **+ 리소스 만들기** 를 선택합니다.

    :::image type="content" source="media/aml-create-in-portal/create-resource.png" alt-text="+ 리소스 만들기를 보여주는 스크린샷.":::

1. 검색 창을 사용하여 **Machine Learning** 을 찾습니다.

1. **Machine Learning** 을 선택합니다.

   :::image type="content" source="media/aml-create-in-portal/machine-learning.png" alt-text="스크린샷은 Machine Learning을 선택하기 위한 검색 결과를 보여줍니다.":::


1. **Machine Learning** 창에서 **만들기** 를 선택하여 시작합니다.

1. 새 작업 영역을 구성하려면 다음 정보를 제공하세요.

   필드|Description
   ---|---
   작업 영역 이름 |작업 영역을 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **docs-ws** 를 사용합니다. 이름은 리소스 그룹 전체에서 고유해야 합니다. 다른 사용자가 만든 작업 영역과 구별되고 기억하기 쉬운 이름을 사용하세요.
   Subscription |사용할 Azure 구독을 선택합니다.
   Resource group | 구독에서 기존 리소스 그룹을 사용하거나 이름을 입력하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유합니다. 이 예에서는 **docs-aml** 을 사용합니다. 
   위치 | 사용자 및 데이터 리소스와 가장 가까운 위치를 선택하여 작업 영역을 만듭니다.

1. 작업 영역 구성을 마쳤으면 **검토 + 만들기** 를 선택합니다.
1. **만들기** 를 선택하여 작업 영역을 만듭니다.

   > [!Warning]
   > 클라우드에서 작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다.

   프로세스가 완료되면 배포 성공 메시지가 표시됩니다.
 
 1. 새 작업 영역을 보려면 **리소스로 이동** 을 선택합니다.
 1. 작업 영역의 포털 보기에서 **스튜디오 시작** 을 선택하여 Azure Machine Learning 스튜디오로 이동합니다. 

