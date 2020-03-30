---
title: 상용 마켓플레이스에서 Azure Apps 제안에 대한 검토 피드백 처리
description: Azure 마켓플레이스, AppSource 또는 Microsoft 파트너 센터의 상용 마켓플레이스 포털을 사용하여 CSP(클라우드 솔루션 공급자) 프로그램을 통해 Azure 앱 에 대한 검토 피드백을 처리하는 방법.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 3932f3fc71dc6427b6cdf93d3a7bc58534a9981c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80279796"
---
# <a name="handling-review-feedback"></a>검토 피드백 처리

이 문서에서는 Microsoft Azure Marketplace 검토 팀이 사용하는 Azure DevOps 환경에 액세스하는 방법을 설명합니다. **Microsoft 검토** 단계 중에 Azure 애플리케이션 제품에서 심각한 문제가 발견되면 이 시스템에 로그인하여 해당 문제에 대한 자세한 정보(검토 피드백)를 볼 수 있습니다. 이러한 문제를 모두 해결한 후 Azure Marketplace에서 계속 게시하려면 제품을 다시 제출해야 합니다. 다음 다이어그램은 이 피드백 프로세스와 게시 프로세스 간의 관계를 보여 줍니다.

![피드백 프로세스 검토](./media/review-feedback-process.png)

일반적으로 검토 문제는 PR(끌어오기 요청)로 참조됩니다. 문제에 대한 세부 정보를 포함하는 온라인 [Azure DevOps](https://azure.microsoft.com/services/devops/)(이전의 VSTS(Visual Studio Team Services)) 항목에 각 PR이 연결됩니다. 다음 이미지는 검토 중에 문제가 발견되면 파트너 센터 환경의 예를 표시합니다. 

![게시 상태](./media/publishing-status.png)

제출에 대한 자세한 내용이 포함된 PR은 "인증 보고서 보기" 링크에 언급됩니다. 복잡한 상황의 경우 검토 및 지원 팀이 메일을 보낼 수도 있습니다.

## <a name="azure-devops-access"></a>Azure 데브옵스 액세스

파트너 센터의 "개발자" 역할에 액세스할 수 있는 모든 사용자는 검토 피드백에서 참조된 PR 항목을 볼 수 있습니다.

<!---
To view the PR items referenced in review feedback, publishers must first be granted proper authorization. Otherwise, new publishers receive a 401 - Not Authorized response page when trying to view PRs. To request access to this Azure DevOps repository, perform the following steps:

1. Collect the following information:
    * Your publisher name and ID
    * Offer type (Azure app), offer name, and SKU ID
    * The pull request link, for example: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`. This URL can be retrieved from the notification message or the address of the 401 response page.
    * The email address(es) of the individuals from your publishing organization that you want access granted to. This list should include the owner address(es) you provided when registering as a publisher on the Cloud Partner Portal (or Partner Center??).
1. Create a support incident. In the title bar of the Cloud Partner Portal (??), select the **Help** button, then choose **Support** from the menu. Your default web browser should launch and navigate to the Microsoft new support incident page. (You may have to sign in first.)
1. Specify the **Problem type** as **marketplace onboarding** and **Category** as **Access problem**, then select **Start request**.

    ![Create an incident](./media/create-an-incident.png)

1. In **Step 1 of 2** page, supply your contact information and select **Continue**.
1. In **Step 2 of 2** page, specify an incident title (for example Request Azure DevOps access) and supply the information you collected in the first step (above). Read and accept the agreement, then select **Submit**.

If the incident creation was successful, a confirmation page is displayed. Save the confirmation information on this page for your reference. The Microsoft Support Team should reply to your access request within a few business days. --->

## <a name="reviewing-the-pull-request"></a>끌어오기 요청 검토

끌어오기 요청에 문서화된 문제를 검토하려면 다음 절차를 따릅니다.

1. 게시 단계 양식의 **Microsoft 검토** 섹션에서 PR 링크를 클릭하여 브라우저를 실행하고 이 PR의 **개요(홈)** 페이지로 이동합니다. 다음 이미지는 Contoso 샘플 앱 제공에 대한 중요한 문제 홈 페이지의 예를 설명합니다. 이 페이지에는 Azure 앱에서 발견된 검토 문제에 대한 유용한 요약 정보가 포함되어 있습니다.

    [![끌어오기 요청 홈 페이지](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> 펼치려면 이미지를 클릭하세요.**

1. (선택 사항) 창의 오른쪽에서 **정책**섹션에서 문제 메시지(이 예: 정책 유효성 **검사 실패)를**클릭하여 관련 로그 파일을 포함하여 문제의 하위 수준 세부 정보를 조사합니다. 일반적으로 오류는 로그 파일의 맨 아래에 표시됩니다.
1. 홈페이지의 왼쪽 메뉴에서 **파일**을 선택하여 이 제품의 기술 자산을 구성하는 목록 파일을 표시합니다. Microsoft 검토자가 발견된 심각한 문제를 설명하는 주석을 추가해 두었을 것입니다. 다음 예제에서는 두 가지 문제가 발견되었습니다.

    [![끌어오기 요청 홈 페이지](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> 펼치려면 이미지를 클릭하세요.**

1. 왼쪽 트리에서 각 주석 노드를 클릭하여 주변 코드 컨텍스트의 주석으로 이동합니다. 팀 프로젝트의 소스 코드를 수정하여 주석에서 설명된 문제를 해결합니다.

>[!Note]
>검토 팀의 Azure DevOps 환경에서는 제품의 기술 자산을 편집할 수 없습니다. 게시자에게는 포함된 소스 코드에 대한 읽기 전용 환경입니다. 그러나 Microsoft 검토 팀을 위해 주석에 회신을 남길 수 있습니다.

   다음 예제에서는 게시자가 첫 번째 문제를 검토, 수정 및 회신했습니다.

   ![첫 번째 수정 및 주석 회신](./media/first-comment-reply.png)

## <a name="next-steps"></a>다음 단계

검토 PR에 문서화된 심각한 문제를 해결한 후에는 [Azure 앱 제품을 다시 게시](./create-new-azure-apps-offer.md#publish)해야 합니다.
