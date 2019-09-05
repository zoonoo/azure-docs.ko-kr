---
title: Azure 제작 리소스로 마이그레이션
titleSuffix: Azure Cognitive Services
description: Azure authoring resource로 마이그레이션합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: 13eb301daadb70456c0c0d7db8991d7a695b1de9
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259761"
---
# <a name="steps-to-migrate-to-the-azure-authoring-resource"></a>Azure authoring resource로 마이그레이션하는 단계

Language Understanding (LUIS) 포털에서 Azure 제작 리소스를 사용 하도록 소유 하 고 있는 모든 앱을 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

* **필요에 따라**각 앱을 내보내거나 내보내기 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)를 사용 하 여 LUIS 포털의 앱 목록에서 앱을 백업 합니다.
* **필요에 따라**각 앱의 collaborator's 목록을 저장 합니다. 이 전자 메일 목록은 마이그레이션 프로세스의 일부로 제공 됩니다.
* **필수**, [Azure 구독이](https://azure.microsoft.com/free/)있어야 합니다. 구독 프로세스의 일부에는 청구 정보가 필요 합니다. 그러나 LUIS를 사용 하는 경우 무료 (F0) 가격 책정 계층을 사용할 수 있습니다. 결과적으로 사용량이 늘어남에 따라 유료 계층이 필요할 수 있습니다. 

Azure 구독이 없는 경우 [등록](https://azure.microsoft.com/free/)합니다. 

## <a name="access-the-migration-process"></a>마이그레이션 프로세스에 액세스

매주 앱을 마이그레이션할지 묻는 메시지가 표시 됩니다. 마이그레이션 없이이 창을 취소할 수 있습니다. 다음 예약 된 기간 전에 마이그레이션하려면 LUIS 포털의 상단 도구 모음에 있는 **잠금** 아이콘에서 마이그레이션 프로세스를 시작할 수 있습니다. 

## <a name="app-owner-begins-the-migration-process"></a>앱 소유자가 마이그레이션 프로세스를 시작 합니다.

LUIS apps의 소유자 인 경우 마이그레이션 프로세스를 사용할 수 있습니다. 

1. 1. [LUIS 포털](https://www.luis.ai) 에 로그인 하 고 사용 약관에 동의 합니다.
1. 마이그레이션 팝업 창에서 마이그레이션을 계속 하거나 나중에 마이그레이션할 수 있습니다. **지금 마이그레이션**을 선택 합니다. 나중에 마이그레이션하려는 경우 Azure에서 새 제작 키로 마이그레이션하는 데 9 개월이 걸릴 수 있습니다.

    ![마이그레이션 프로세스의 첫 번째 팝업 창에서 지금 마이그레이션을 선택 합니다.](./media/migrate-authoring-key/migrate-now.png)

1. 공동 작업자를 포함 하는 앱이 있는 경우 마이그레이션에 대 한 정보를 알려 주는 **전자 메일을 전송** 하 라는 메시지가 표시 됩니다. 선택적 단계입니다. 기본 전자 메일 응용 프로그램은 약간의 서식이 지정 된 전자 메일을 사용 하 여 열립니다. 

    ![마이그레이션에 대 한 협력자 전자 메일 보내기](./media/migrate-authoring-key/send-collaborators-email.png)

1. **앱을 마이그레이션할 제작 리소스를 만들어 시작**을 선택 하 여 LUIS authoring 리소스를 만들도록 선택 합니다. 

    ![제작 리소스 만들기](./media/migrate-authoring-key/choose-authoring-resource.png)

1. 다음 창에서 리소스 키 정보를 입력 합니다. 정보를 입력 한 후 **리소스 만들기**를 선택 합니다. 

    ![제작 리소스 만들기](./media/migrate-authoring-key/choose-authoring-resource-form.png)

    **새 제작 리소스를 만들**때 다음 정보를 제공 합니다. 

    * **리소스 이름** -사용자가 선택한 사용자 지정 이름으로, 작성 및 예측 끝점 쿼리의 URL의 일부로 사용 됩니다.
    * **테 넌 트** -Azure 구독이 연결 된 테 넌 트입니다. 
    * **구독 이름** -리소스에 대해 청구 되는 구독입니다.
    * **리소스 그룹** -사용자가 선택 하거나 만드는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용 하면 액세스 및 관리를 위해 Azure 리소스를 그룹화 할 수 있습니다. 
    * **Location** -선택한 위치는 **리소스 그룹** 선택에 따라 결정 됩니다.
    * **가격** 책정 계층-가격 책정 계층은 초당 최대 트랜잭션 수와 월을 결정 합니다. 

1. 제작 리소스를 만들면 성공 메시지가 표시 됩니다. **닫기** 를 선택 하 여 팝업 창을 닫습니다.

    ![작성 리소스를 만들었습니다.](./media/migrate-authoring-key/migration-success.png)

    **내 앱** 목록에는 새 제작 리소스로 마이그레이션된 앱이 표시 됩니다. 

    LUIS 포털에서 앱을 계속 편집 하기 위해 제작 리소스의 키를 알 필요가 없습니다. 앱을 프로그래밍 방식으로 편집 하려는 경우 제작 키 값이 필요 합니다. 이러한 값은 LUIS 포털의 **> Azure 리소스** 페이지에 표시 되며, 리소스의 **키** 페이지에 있는 Azure Portal 에서도 사용할 수 있습니다.  

## <a name="after-the-migration-process-add-contributors-to-your-authoring-resource"></a>마이그레이션 프로세스 후 제작 리소스에 참가자를 추가 합니다.

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

[참가자를 추가 하는 방법을](luis-how-to-collaborate.md)알아봅니다. 

## <a name="next-steps"></a>다음 단계


* 제작 및 런타임 키에 대 한 [개념](luis-concept-keys.md) 검토
* [키를 할당](luis-how-to-azure-subscription.md) 하 고 [참가자](luis-how-to-collaborate.md) 를 추가 하는 방법 검토
