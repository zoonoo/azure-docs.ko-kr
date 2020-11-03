---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 10/30/2020
ms.topic: include
ms.openlocfilehash: e592c11062e81d48014a90895a0e42b460d4b77c
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128237"
---
## <a name="sign-in-to-luis-portal"></a>LUIS 포털에 로그인

LUIS에 대한 새 사용자는 다음 절차를 수행해야 합니다.

1. [LUIS 포털](https://www.luis.ai)에 로그인한 후 국가/지역을 선택하고 사용 약관에 동의합니다. **내 앱** 이 대신 표시되는 경우 LUIS 리소스가 이미 존재하므로 앱 만들기로 건너뛰어야 합니다. 두 가지 가입 옵션이 있습니다.

    * Azure 리소스 사용(권장) - LUIS 계정을 신규 또는 기존 Azure Authoring 리소스와 연결할 수 있습니다. 이는 이미 마이그레이션된 가입과 동일합니다. 나중에 [마이그레이션 프로세스](../luis-migration-authoring.md#what-is-migration)를 진행할 필요가 없습니다.

    * 평가판 키 사용. 이렇게 하면 설정할 필요가 없는 평가판 리소스로 LUIS에 로그인할 수 있습니다. 이 옵션을 선택하면 결국 [계정을 마이그레이션](../luis-migration-authoring.md#migration-steps)하고 애플리케이션을 작성 리소스에 연결해야 합니다.

1. 표시되는 **작성 선택** 창에서 Azure 구독 및 LUIS 작성 리소스를 찾습니다. 리소스가 없는 경우 새 리소스 그룹을 만들 수 있습니다.

    :::image type="content" source="../media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Language Understanding 작성 리소스의 유형을 선택합니다.":::
    
    새 작성 리소스를 만들 때 다음 정보를 제공합니다.
    * **테넌트 이름** - Azure 구독이 연결된 테넌트입니다.
    * **Azure 구독 이름** - 리소스에 대해 청구되는 구독입니다.
    * **Azure 리소스 그룹 이름** - 사용자가 선택하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다.
    * **Azure 리소스 이름** - 사용자가 선택한 사용자 지정 이름으로, 작성 및 예측 엔드포인트 쿼리의 URL 중 일부로 사용됩니다.
    * **가격 책정 계층** - 가격 책정 계층은 초 및 월별 최대 트랜잭션 수를 결정합니다.


