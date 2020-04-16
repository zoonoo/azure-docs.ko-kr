---
title: QnA 메이커 서비스 설정 - QnA 메이커
description: QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 563a56fdb288568e7fe667fa54658400064a560f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402980"
---
# <a name="manage-qna-maker-resources"></a>QnA 메이커 리소스 관리

QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.

리소스를 만들기 전에 다음 개념을 잘 이해하는 것이 좋습니다.

* [QnA Maker 리소스](../Concepts/azure-resources.md)
* [키 작성 및 게시](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>새 QnA Maker 서비스 만들기

이 절차는 기술 자료 콘텐츠를 관리하는 데 필요한 Azure 리소스를 만듭니다. 이 단계를 완료하면 Azure 포털의 리소스에 대한 **키** 페이지에서 _구독_ 키를 찾을 수 있습니다.

1. Azure 포털에 로그인하고 [QnA Maker 리소스를 만듭니다.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. 이용 약관을 읽은 후 **만들기를** 선택합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker에서**적절한 계층 및 지역을 선택합니다.

    ![새 QnA Maker 서비스 만들기 - 가격 책정 계층 및 지역](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * **이름** 필드에 고유한 이름을 입력하여 이 QnA Maker 서비스를 식별합니다. 이 이름은 기술 자료가 연결될 QnA Maker 끝점도 식별합니다.
    * QnA Maker 리소스가 배포될 **구독을** 선택합니다.
    * QnA 메이커 관리 서비스(포털 및 관리 API)의 **가격 책정 계층을** 선택합니다. [SKU 가격에 대한 자세한 내용을](https://aka.ms/qnamaker-pricing)참조하십시오.
    * 새 리소스 그룹(권장)을 만들거나 이 QnA Maker 리소스를 배포할 기존 리소스그룹을 사용합니다. **Resource group** QnA Maker는 여러 Azure 리소스를 만듭니다. 이러한 리소스를 보유하는 리소스 그룹을 만들 때 리소스 그룹 이름으로 이러한 리소스를 쉽게 찾고 관리하고 삭제할 수 있습니다.
    * 리소스 **그룹 위치를**선택합니다.
    * Azure 인지 검색 서비스의 **검색 가격 책정 계층을** 선택합니다. 프리 티어 옵션을 사용할 수 없는 경우(흐리게 표시됨) 구독을 통해 이미 무료 서비스를 배포한 것입니다. 이 경우 기본 계층부터 시작해야 합니다. [Azure 인지 검색 가격 세부 정보를](https://azure.microsoft.com/pricing/details/search/)참조하십시오.
    * Azure 인지 검색 인덱스를 배포할 검색 **위치를** 선택합니다. 고객 데이터를 저장해야 하는 위치에 대한 제한은 Azure Cognitive Search에서 선택한 위치를 결정하는 데 도움이 됩니다.
    * 앱 **이름** 필드에 Azure 앱 서비스 인스턴스의 이름을 입력합니다.
    * 기본적으로 앱 서비스는 표준(S1) 계층으로 기본설정됩니다. 계획을 만든 후에 변경할 수 있습니다. [앱 서비스 가격에](https://azure.microsoft.com/pricing/details/app-service/)대해 자세히 알아보세요.
    * 앱 서비스가 배포될 **웹 사이트 위치를** 선택합니다.

        > [!NOTE]
        > **검색 위치는** 웹 **사이트 위치와**다를 수 있습니다.

    * 응용 프로그램 인사이트를 사용하도록 설정할지 여부를 **선택합니다.** **Application Insights**를 사용하도록 설정하면 QnA Maker는 트래픽, 채팅 로그 및 오류에 대한 원격 분석을 수집합니다.
    * 응용 **프로그램 인사이트** 리소스가 배포될 앱 인사이트 위치를 선택합니다.
    * 비용 절감 측정값의 경우, QnA Maker용으로 생성된 일부(전체는 아님) Azure 리소스를 [공유](#configure-qna-maker-to-use-different-cognitive-search-resource)할 수 있습니다.

1. 모든 필드의 유효성이 검사된 후 **만들기를**선택합니다. 이 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

1. 배포가 완료되면 구독에서 만든 다음 리소스가 표시됩니다.

   ![QnA Maker 서비스를 새로 만든 리소스](../media/qnamaker-how-to-setup-service/resources-created.png)

    _코그너티브 서비스_ 유형이 있는 리소스에는 _구독_ 키가 있습니다.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Azure 포털에서 구독 키 찾기

QnA Maker 리소스를 만든 Azure 포털에서 구독 키를 보고 재설정할 수 있습니다.

1. Azure 포털의 QnA Maker 리소스로 이동하여 인지 _서비스_ 유형이 있는 리소스를 선택합니다.

    ![QnA Maker 리소스 목록](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. **키로**이동 :

    ![구독 키](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>QnA Maker 포털에서 끝점 키 찾기

끝점 키는 기술 자료에 대 한 호출을 만드는 데 사용 되기 때문에 끝점은 리소스와 동일한 지역에 있습니다.

엔드포인트 키는 [QnA Maker 포털](https://qnamaker.ai)에서 관리할 수 있습니다.

1. [QnA Maker 포털에](https://qnamaker.ai)로그인하여 프로필로 이동한 다음 **서비스 설정을**선택합니다.

    ![엔드포인트 키](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. 키 보기 또는 재설정:

    > [!div class="mx-imgBorder"]
    > ![엔드포인트 키 관리자](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >키가 손상되었다고 생각되면 키를 새로 고칩니다. 클라이언트 애플리케이션 또는 봇 코드에 해당 변경 내용을 적용해야 할 수도 있습니다.

## <a name="upgrade-qna-maker-sku"></a>업그레이드 QnA 메이커 SKU

현재 계층을 넘어 기술 자료에 더 많은 질문과 답변을 원할 경우 QnA Maker 서비스 가격 책정 계층을 업그레이드하십시오.

QnA Maker 관리 SKU를 업그레이드하려면

1. Azure Portal의 QnA Maker 리소스로 이동한 다음, **가격 책정 계층**을 선택합니다.

    ![QnA Maker 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 적절한 SKU를 선택하고 **선택**을 누릅니다.

    ![QnA Maker 가격 책정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>앱 서비스 업그레이드

 기술 자료가 클라이언트 앱에서 더 많은 요청을 제공해야 하는 경우 앱 서비스 가격 책정 계층을 업그레이드합니다.

앱 서비스를 [확장하거나](https://docs.microsoft.com/azure/app-service/manage-scale-up) 확장할 수 있습니다.

Azure 포털의 앱 서비스 리소스로 이동하여 필요에 따라 **확장** 또는 **확장** 옵션을 선택합니다.

![QnA 메이커 앱 서비스 규모](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-the-azure-cognitive-search-service"></a>Azure 인지 검색 서비스 업그레이드

많은 기술 자료가 있는 경우 Azure 인지 검색 서비스 가격 책정 계층을 업그레이드합니다.

현재 Azure 검색 SKU의 내부 업그레이드를 수행할 수 없습니다. 그러나 원하는 SKU로 새 Azure Search 리소스를 만들고, 데이터를 새 리소스로 복원한 다음, QnA Maker 스택에 연결할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure 포털에서 새 Azure 검색 리소스를 만들고 원하는 SKU를 선택합니다.

    ![QnA Maker Azure Search 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 원래 Azure Search 리소스의 인덱스를 새 리소스로 복원합니다. 백업 [복원 샘플 코드를](https://github.com/pchoudhari/QnAMakerBackupRestore)참조하십시오.

1. 데이터가 복원된 후 새 Azure 검색 리소스로 이동하여 **키를**선택하고 **이름** 및 **관리 키를**적어 둡니다.

    ![QnA Maker Azure Search 키](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 새 Azure 검색 리소스를 QnA Maker 스택에 연결하려면 QnA Maker 앱 서비스 인스턴스로 이동하십시오.

    ![QnA 메이커 앱 서비스 인스턴스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. **응용 프로그램 설정을** 선택하고 3단계에서 **AzureSearchName** 및 **AzureSearchAdminKey** 필드의 설정을 수정합니다.

    ![QnA 메이커 앱 서비스 설정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. 앱 서비스 인스턴스를 다시 시작합니다.

    ![QnA 메이커 앱 서비스 인스턴스 의 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>최신 런타임 업데이트 받기

QnAMaker 런타임은 Azure 포털에서 [QnAMaker 서비스를 만들](./set-up-qnamaker-service-azure.md) 때 배포되는 Azure 앱 서비스 인스턴스의 일부입니다. 런타임은 주기적으로 업데이트됩니다. QnA Maker 앱 서비스 인스턴스는 2019년 4월 사이트 확장 릴리스(버전 5+) 이후 자동 업데이트 모드에 있습니다. 이 업데이트는 업그레이드 하는 동안 제로 가동 중지 시간을 처리 하도록 설계 되었습니다.

에서 https://www.qnamaker.ai/UserSettings현재 버전을 확인할 수 있습니다. 버전이 버전 5.x보다 오래된 경우 앱 서비스를 다시 시작하여 최신 업데이트를 적용해야 합니다.

1. [Azure 포털의](https://portal.azure.com)QnAMaker 서비스(리소스 그룹)로 이동합니다.

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 리소스 그룹](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 앱 서비스 인스턴스를 선택하고 개요 섹션을 **엽니다.**

    > [!div class="mx-imgBorder"]
    > ![QnAMaker 앱 서비스 인스턴스](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 앱 서비스를 다시 시작합니다. 업데이트 프로세스는 몇 초 안에 완료됩니다. 이 QnAMaker 서비스를 사용하는 모든 종속 응용 프로그램 이나 봇은 이 다시 시작 기간 동안 최종 사용자가 사용할 수 없습니다.

    ![QnAMaker 앱 서비스 인스턴스 의 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>다른 인지 검색 리소스를 사용 하 여 QnA 메이커 구성

포털을 통해 QnA 서비스와 해당 종속성(예: 검색)을 만드는 경우 검색 서비스가 만들어지고 QnA Maker 서비스에 연결됩니다. 이러한 리소스를 만든 후 앱 서비스 설정을 업데이트하여 기존 검색 서비스를 사용하고 방금 만든 리소스를 제거할 수 있습니다.

QnA Maker의 **앱 서비스** 리소스는 인지 검색 리소스를 사용합니다. QnA Maker에서 사용하는 인지 검색 리소스를 변경하려면 Azure 포털에서 설정을 변경해야 합니다.

1. QnA Maker에서 사용할 인지 검색 리소스의 **관리 키와** **이름을** 가져옵니다.

1. [Azure 포털에](https://portal.azure.com) 로그인하고 QnA Maker 리소스와 연결된 **앱 서비스를** 찾습니다. 둘 다 같은 이름을 가있습니다.

1. **설정을**선택한 다음 **구성**을 선택합니다. 그러면 QnA Maker의 앱 서비스에 대한 모든 기존 설정이 표시됩니다.

    > [!div class="mx-imgBorder"]
    > ![앱 서비스 구성 설정을 보여주는 Azure 포털의 스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 다음 키의 값을 변경합니다.

    * **AzureSearchAdminKey**
    * **Azure SearchName**

1. 새 설정을 사용하려면 앱 서비스를 다시 시작해야 합니다. **개요를**선택한 다음 **다시 시작을**선택합니다.

    > [!div class="mx-imgBorder"]
    > ![구성 설정변경 후 Azure 포털 다시 시작 앱 서비스의 스크린샷](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Azure 리소스 관리자 템플릿을 통해 QnA 서비스를 만드는 경우 모든 리소스를 만들고 기존 검색 서비스를 사용하도록 앱 서비스 만들기를 제어할 수 있습니다.

앱 서비스 [응용 프로그램 설정을](../../../app-service/configure-common.md#configure-app-settings)구성하는 방법에 대해 자세히 알아봅니다.

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>시간 시간을 방지하려면 앱 서비스 유휴 설정 구성

게시된 기술 자료에 대해 QnA Maker 예측 런타임을 제공하는 앱 서비스에는 유휴 시간 지정 구성이 있으며, 이 구성은 서비스가 유휴 상태인 경우 자동으로 시간 지정을 기본값으로 설정합니다. QnA Maker의 경우 예측 런타임 생성Answer API가 트래픽이 없는 기간 이후에 때때로 시간이 지남에 따라 발생한다는 의미입니다.

트래픽이 없는 경우에도 예측 끝점 앱을 로드된 상태로 유지하려면 유휴 상태를 항상 켜두도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. QnA Maker 리소스의 앱 서비스를 검색하고 선택합니다. QnA Maker 리소스와 이름이 같지만 다른 **유형의** 앱 서비스를 갖게 됩니다.
1. **설정을** 찾은 다음 **구성을**선택합니다.
1. 구성 창에서 일반 **설정을**선택한 다음 **항상 을**찾은 다음 **켜짐을** 값으로 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![구성 창에서 **일반 설정**을 선택한 다음 **항상**을 찾은 다음 **On**을 값으로 선택합니다.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. **저장** 을 선택하여 구성을 저장합니다.
1. 새 설정을 사용하려면 앱을 다시 시작할지 묻는 메시지가 표시됩니다. **계속**을 선택합니다.

앱 서비스 [일반 설정을](../../../app-service/configure-common.md#configure-general-settings)구성하는 방법에 대해 자세히 알아봅니다.

## <a name="delete-azure-resources"></a>Azure 리소스 삭제

QnA Maker 기술 자료에 사용되는 Azure 리소스를 삭제하는 경우 기술 자료는 더 이상 작동하지 않습니다. 모든 리소스를 삭제하기 전에 **설정** 페이지에서 기술 자료를 내보냈는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[앱 서비스](../../../app-service/index.yml) 및 [검색 서비스에](../../../search/index.yml)대해 자세히 알아보십시오.

> [!div class="nextstepaction"]
> [기술 자료 만들기 및 게시](../Quickstarts/create-publish-knowledge-base.md)
