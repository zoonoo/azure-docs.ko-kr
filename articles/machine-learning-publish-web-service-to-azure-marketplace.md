<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publishing Azure ML Web Services to the Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Azure 마켓플레이스에 Azure 기계 학습 웹 서비스 게시

문서 내용:

-   [소개][소개]
-   [게시 프로세스 개요][게시 프로세스 개요]
-   [Azure 마켓플레이스에 대한 게시 지침][Azure 마켓플레이스에 대한 게시 지침]
-   [기계 학습 특정 옵션][기계 학습 특정 옵션]

<!--Anchors-->

## 소개

Azure 마켓플레이스에서는 외부 고객이 사용하도록 유료 또는 무료 서비스로 Azure 기계 학습 웹 서비스를 게시하는 기능을 제공합니다. 이 문서에서는 해당 프로세스의 개요를 시작 지침에 대한 링크와 함께 제공합니다. 이 프로세스를 사용하여 다른 개발자가 응용 프로그램에서 사용할 수 있도록 웹 서비스 제공을 시작할 수 있습니다.

## 게시 프로세스 개요

Azure 기계 학습 웹 서비스를 Azure 마켓플레이스에 게시하는 단계는 다음과 같습니다.

1.  Azure 기계 학습 RRS(요청-응답 서비스) 또는 BES (일괄 처리 실행 서비스) 웹 서비스를 만들고 게시합니다.
2.  Azure 관리 포털에서 서비스를 프로덕션에 배포합니다.
3.  게시된 웹 서비스의 URL을 사용하여 Azure 마켓플레이스에 게시합니다.
4.  게시 프로세스 개요: <http://msdn.microsoft.com/en-us/library/azure/hh580725.aspx>
5.  제출된 제품은 검토되고 고객이 구매를 시작하기 전에 승인되어야 합니다. 게시 프로세스는 영업일 기준 몇 일이 걸릴 수 있습니다. 시간을 가능하면 많이 단축하려는 작업이 진행되고 있고 향후 통신에서 업데이트가 제공될 예정입니다.

## Azure 마켓플레이스에 대한 게시 지침

1.  게시자로 등록해야 합니다. 자세한 내용은 다음을 참조하세요. <http://msdn.microsoft.com/en-us/library/azure/hh563872.aspx>
2.  가격 계획을 포함하여 제품에 대한 정보를 제공해야 합니다. 무료 또는 유료 서비스를 제공할지 결정합니다. 자세한 내용은 다음을 참조하세요. <http://msdn.microsoft.com/en-us/library/azure/hh563873.aspx>
3.  유료로 제공하려면 은행 및 세금 정보와 같은 지급 정보를 제공해야 합니다. 자세한 내용은 다음을 참조하세요. <http://msdn.microsoft.com/en-us/library/azure/hh563873.aspx>

## 기계 학습 특정 옵션

1.  새 제품을 만들 때 **데이터 서비스**를 선택하고 **새 데이터 서비스 만들기**를 클릭합니다.

    ![Azure 마켓플레이스][Azure 마켓플레이스]

2.  **데이터 서비스** 탭에서 데이터 원본으로 **웹 서비스**를 클릭합니다.

    ![Azure 마켓플레이스][1]

3.  **서비스 URL**에는 웹 서비스 URL을 사용합니다.

    -   Azure 기계 학습 스튜디오의 왼쪽 메뉴에서 **웹 서비스**를 클릭합니다.
    -   마켓플레이스에 게시할 웹 서비스를 클릭합니다.
    -   **대시보드** 페이지에서 RRS 서비스에 대한 **API 도움말 페이지**를 클릭합니다.
    -   OData 끝점 주소를 복사합니다.

4.  인증에 대해 **헤더**를 **인증 체계**로 선택합니다.

    -   **헤더 이름**에 대해 "권한 부여"를 입력합니다.
    -   **헤더 값**에 대해:

        -   기계 학습 스튜디오의 웹 서비스에 대한 **대시보드** 페이지에서 **API 키**를 복사합니다.
        -   **헤더 값** 필드에서 "전달자"(따옴표 제외), 공백을 차례로 입력하고 API 키를 붙여넣습니다.
    -   **This Service is OData(이 서비스는 OData입니다.)** 확인란을 선택합니다.

5.  범주:

    -   **기계 학습**이 선택되었는지 확인합니다.

  [소개]: #introduction
  [게시 프로세스 개요]: #overview-of-the-publishing-process
  [Azure 마켓플레이스에 대한 게시 지침]: #guidelines-for-publishing-to-azure-marketplace
  [기계 학습 특정 옵션]: #machine-learning-specific-options
  [Azure 마켓플레이스]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
  [1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
