---
title: QnA Maker 서비스 설정-QnA Maker
description: QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219270"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker 리소스 관리

QnA Maker 기술 자료를 만들려면 먼저 Azure에서 QnA Maker 서비스를 설정해야 합니다. 구독에 새 리소스를 만들 수 있는 권한이 있으면 누구든지 QnA Maker 서비스를 설정할 수 있습니다.

다음 개념에 대 한 확실 한 이해는 리소스를 만들기 전에 유용 합니다.

* [QnA Maker 리소스](../Concepts/azure-resources.md)
* [키 작성 및 게시](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>새 QnA Maker 서비스 만들기

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

이 절차는 기술 자료 콘텐츠를 관리 하는 데 필요한 Azure 리소스를 만듭니다. 이러한 단계를 완료 한 후에는 Azure Portal 리소스의 **키** 페이지에서 _구독_ 키를 찾을 수 있습니다.

1. Azure Portal에 로그인 하 여 QnA Maker 리소스를 [만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. 사용 약관을 읽은 후 **만들기** 를 선택 합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker** 에서 적절 한 계층 및 지역을 선택 합니다.

    ![새 QnA Maker 서비스 만들기 - 가격 책정 계층 및 지역](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * **이름** 필드에이 QnA Maker 서비스를 식별 하는 고유한 이름을 입력 합니다. 또한이 이름은 기술 자료가 연결 될 QnA Maker 끝점을 식별 합니다.
    * QnA Maker 리소스를 배포할 **구독** 을 선택 합니다.
    * QnA Maker 관리 서비스 (포털 및 관리 Api)에 대 한 **가격 책정 계층** 을 선택 합니다. [SKU 가격 책정에 대 한 자세한 내용을](https://aka.ms/qnamaker-pricing)참조 하세요.
    * 새 리소스 그룹을 만들거나 (권장)이 QnA Maker 리소스를 배포할 기존 **리소스 그룹** 을 사용 합니다. QnA Maker는 여러 Azure 리소스를 만듭니다. 이러한 리소스를 보유 하는 리소스 그룹을 만들 때 리소스 그룹 이름으로 이러한 리소스를 쉽게 찾고 관리 하 고 삭제할 수 있습니다.
    * **리소스 그룹 위치** 를 선택 합니다.
    * Azure Cognitive Search 서비스의 **검색 가격 책정 계층** 을 선택 합니다. 무료 계층 옵션을 사용할 수 없는 경우 (흐리게 표시 됨) 이미 구독을 통해 배포 된 무료 서비스가 있음을 의미 합니다. 이 경우 기본 계층으로 시작 해야 합니다. [Azure Cognitive Search 가격 정보](https://azure.microsoft.com/pricing/details/search/)를 참조 하세요.
    * Azure Cognitive Search 인덱스를 배포할 **검색 위치** 를 선택 합니다. 고객 데이터를 저장 해야 하는 위치에 대 한 제한 사항은 Azure Cognitive Search에 대해 선택한 위치를 결정 하는 데 도움이 됩니다.
    * **앱 이름** 필드에 Azure App Service 인스턴스의 이름을 입력 합니다.
    * 기본적으로 App Service 기본값은 표준 (S1) 계층입니다. 계획을 만든 후에 변경할 수 있습니다. [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)에 대해 자세히 알아보세요.
    * App Service를 배포할 **웹 사이트 위치** 를 선택 합니다.

        > [!NOTE]
        > **검색 위치** 는 **웹 사이트 위치** 와 다를 수 있습니다.

    * **Application Insights** 사용 하도록 설정할지 여부를 선택 합니다. **Application Insights** 를 사용하도록 설정하면 QnA Maker는 트래픽, 채팅 로그 및 오류에 대한 원격 분석을 수집합니다.
    * Application Insights 리소스를 배포할 **앱 정보 위치** 를 선택 합니다.
    * 비용 절감 측정값의 경우, QnA Maker용으로 생성된 일부(전체는 아님) Azure 리소스를 [공유](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource)할 수 있습니다.

1. 모든 필드의 유효성을 검사 한 후 **만들기** 를 선택 합니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

1. 배포가 완료 되 면 구독에 다음과 같은 리소스가 생성 됩니다.

   ![QnA Maker 서비스를 새로 만든 리소스](../media/qnamaker-how-to-setup-service/resources-created.png)

    _Cognitive Services_ 형식의 리소스에는 _구독_ 키가 있습니다.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리형(미리 보기 릴리스)](#tab/v2)

이 절차는 기술 자료 콘텐츠를 관리 하는 데 필요한 Azure 리소스를 만듭니다. 이러한 단계를 완료 한 후에는 Azure Portal 리소스의 **키** 페이지에서 *구독* 키를 찾을 수 있습니다.

1. Azure Portal에 로그인 하 여 QnA Maker 리소스를 [만듭니다](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. 사용 약관을 읽은 후 **만들기** 를 선택 합니다.

    ![새 QnA Maker 서비스 만들기](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. **QnA Maker** 에서 관리 (미리 보기) 확인란을 선택 하 고 적절 한 계층 및 지역을 선택 합니다.

    ![새 QnA Maker 관리 서비스-가격 책정 계층 및 지역 만들기](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * QnA Maker 리소스를 배포할 **구독** 을 선택 합니다.
    * 새 리소스 그룹을 만들거나 (권장)이 QnA Maker 관리 (미리 보기) 리소스를 배포할 기존 **리소스 그룹** 을 사용 합니다. QnA Maker 관리 (미리 보기)는 몇 가지 Azure 리소스를 만듭니다. 이러한 리소스를 보유 하는 리소스 그룹을 만들 때 리소스 그룹 이름으로 이러한 리소스를 쉽게 찾고 관리 하 고 삭제할 수 있습니다.
    * **이름** 필드에이 QnA Maker 관리 (미리 보기) 서비스를 식별 하는 고유한 이름을 입력 합니다. 
    * QnA Maker 관리 (미리 보기) 서비스를 배포할 **위치** 를 선택 합니다. 관리 Api 및 서비스 끝점은이 위치에서 호스팅됩니다. 
    * QnA Maker 관리 (미리 보기) 서비스의 **가격 책정 계층** 을 선택 합니다 (미리 보기에 사용 가능). [SKU 가격 책정에 대 한 자세한 내용을](https://aka.ms/qnamaker-pricing)참조 하세요.
    * Azure Cognitive Search 인덱스를 배포할 **검색 위치** 를 선택 합니다. 고객 데이터를 저장 해야 하는 위치에 대 한 제한 사항은 Azure Cognitive Search에 대해 선택한 위치를 결정 하는 데 도움이 됩니다.
    * Azure Cognitive Search 서비스의 **검색 가격 책정 계층** 을 선택 합니다. 무료 계층 옵션을 사용할 수 없는 경우 (흐리게 표시 됨) 이미 구독을 통해 배포 된 무료 서비스가 있음을 의미 합니다. 이 경우 기본 계층으로 시작 해야 합니다. [Azure Cognitive Search 가격 정보](https://azure.microsoft.com/pricing/details/search/)를 참조 하세요.

1. 모든 필드의 유효성을 검사 한 후 **검토 + 만들기** 를 선택 합니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

1. 배포가 완료 되 면 구독에 다음과 같은 리소스가 생성 됩니다.

    ![리소스가 새로운 QnA Maker 관리 (미리 보기) 서비스를 만듦](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    _Cognitive Services_ 형식의 리소스에는 _구독_ 키가 있습니다.

---

## <a name="upgrade-azure-resources"></a>Azure 리소스 업그레이드

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU 업그레이드

현재 계층을 벗어나 기술 자료에서 더 많은 질문과 대답을 원하는 경우 QnA Maker 서비스 가격 책정 계층을 업그레이드 하세요.

QnA Maker 관리 SKU를 업그레이드하려면

1. Azure Portal의 QnA Maker 리소스로 이동한 다음, **가격 책정 계층** 을 선택합니다.

    ![QnA Maker 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. 적절한 SKU를 선택하고 **선택** 을 누릅니다.

    ![QnA Maker 가격 책정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>업그레이드 App Service

기술 자료가 클라이언트 앱에서 더 많은 요청을 처리 해야 하는 경우 App Service 가격 책정 계층을 업그레이드 합니다.

App Service를 [강화 하거나 규모](../../../app-service/manage-scale-up.md) 를 확장할 수 있습니다.

Azure Portal에서 App Service 리소스로 이동 하 고 필요에 따라 **수직 확장** 또는 **수평 확장** 옵션을 선택 합니다.

![QnA Maker App Service 크기 조정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스 업그레이드

많은 기술 자료를 포함할 계획인 경우 Azure Cognitive Search 서비스 가격 책정 계층을 업그레이드하세요.

현재 Azure search SKU의 전체 업그레이드를 수행할 수 없습니다. 그러나 원하는 SKU로 새 Azure Search 리소스를 만들고, 데이터를 새 리소스로 복원한 다음, QnA Maker 스택에 연결할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure Portal에서 새 Azure search 리소스를 만들고 원하는 SKU를 선택 합니다.

    ![QnA Maker Azure Search 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 원래 Azure Search 리소스의 인덱스를 새 리소스로 복원합니다. [백업 복원 샘플 코드](https://github.com/pchoudhari/QnAMakerBackupRestore)를 참조하세요.

1. 데이터가 복원 된 후에는 새 Azure search 리소스로 이동 하 고, **키** 를 선택 하 고, **이름** 및 **관리자 키** 를 적어둡니다.

    ![QnA Maker Azure Search 키](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. 새 Azure search 리소스를 QnA Maker 스택에 연결 하려면 QnA Maker App Service 인스턴스로 이동 합니다.

    ![QnA Maker App Service 인스턴스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. **애플리케이션 설정** 을 선택하고 3단계의 **AzureSearchName** 및 **AzureSearchAdminKey** 필드의 설정을 수정합니다.

    ![QnA Maker App Service 설정](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. App Service 인스턴스를 다시 시작합니다.

    ![QnA Maker App Service 인스턴스 다시 시작](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>무료 검색 리소스에 대 한 비활성 정책

QnA maker 리소스를 사용 하지 않는 경우 모든 리소스를 제거 해야 합니다. 사용 하지 않는 리소스를 제거 하지 않으면 무료 검색 리소스를 만든 경우 기술 자료가 작동 하지 않습니다.

무료 검색 리소스는 API 호출을 받지 않고 90 일 후에 삭제 됩니다.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리형(미리 보기 릴리스)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Azure Cognitive Search 서비스 업그레이드

많은 기술 자료를 포함할 계획인 경우 Azure Cognitive Search 서비스 가격 책정 계층을 업그레이드하세요.

현재 Azure search SKU의 전체 업그레이드를 수행할 수 없습니다. 그러나 원하는 SKU로 새 Azure Search 리소스를 만들고, 데이터를 새 리소스로 복원한 다음, QnA Maker 스택에 연결할 수 있습니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Azure Portal에서 새 Azure search 리소스를 만들고 원하는 SKU를 선택 합니다.

    ![QnA Maker Azure Search 리소스](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. 원래 Azure Search 리소스의 인덱스를 새 리소스로 복원합니다. [백업 복원 샘플 코드](https://github.com/pchoudhari/QnAMakerBackupRestore)를 참조하세요.

1. 새 Azure search 리소스를 QnA Maker 관리 (미리 보기) 서비스에 연결 하려면 아래 항목을 참조 하세요.

### <a name="inactivity-policy-for-free-search-resources"></a>무료 검색 리소스에 대 한 비활성 정책

QnA maker 리소스를 사용 하지 않는 경우 모든 리소스를 제거 해야 합니다. 사용 하지 않는 리소스를 제거 하지 않으면 무료 검색 리소스를 만든 경우 기술 자료가 작동 하지 않습니다.

무료 검색 리소스는 API 호출을 받지 않고 90 일 후에 삭제 됩니다.

---

## <a name="delete-azure-resources"></a>Azure 리소스 삭제

QnA Maker 기술 자료에 사용되는 Azure 리소스를 삭제하는 경우 기술 자료는 더 이상 작동하지 않습니다. 모든 리소스를 삭제하기 전에 **설정** 페이지에서 기술 자료를 내보냈는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[App service](../../../app-service/index.yml) 및 [Search 서비스](../../../search/index.yml)에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [다른 사용자와 작성 하는 방법 알아보기](../index.yml)
