---
title: Azure 앱 제품에 대한 피드백 검토 - Microsoft 상업용 마켓플레이스
description: Microsoft Azure Marketplace 검토 팀에서 Azure 애플리케이션 제품에 대한 피드백을 처리하는 방법. 파트너 센터 자격 증명을 사용하여 Azure DevOps의 피드백에 액세스할 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: aecc64c5fa923bc29d34efd3969c2c12f30153c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320105"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Azure 애플리케이션 제품에 대한 검토 피드백 처리

이 문서에서는 [Azure DevOps](https://azure.microsoft.com/services/devops/)에서 Microsoft Azure Marketplace 검토 팀의 피드백에 액세스하는 방법을 설명합니다. **Microsoft 검토** 단계 중에 Azure 애플리케이션 제품에서 심각한 문제가 발견되면 이 시스템에 로그인하여 해당 문제에 대한 자세한 정보(검토 피드백)를 볼 수 있습니다. 문제를 모두 해결한 후 Azure Marketplace에서 계속 게시하려면 제품을 다시 제출해야 합니다. 다음 다이어그램은 이 피드백 프로세스와 게시 프로세스 간의 관계를 보여 줍니다.

![피드백 검토 프로세스](./media/review-feedback-process.png)

일반적으로 검토 문제는 PR(끌어오기 요청)로 참조됩니다. 문제에 대한 세부 정보를 포함하는 온라인 Azure DevOps 항목에 각 PR이 연결되어 있습니다. 다음 그림은 검토하는 동안 문제가 발견된 파트너 센터 환경의 예를 보여 줍니다. 

![게시 상태](./media/publishing-status.png)

제출에 대한 특정 세부 정보를 포함하는 PR은 "인증 보고서 보기" 링크에 설명되어 있습니다. 복잡한 상황의 경우 검토 및 지원 팀이 메일을 보낼 수도 있습니다.

## <a name="azure-devops-access"></a>Azure DevOps 액세스

파트너 센터의 "개발자" 역할에 액세스할 수 있는 모든 사용자는 검토 피드백에서 언급된 PR 항목을 볼 수 있습니다.

## <a name="reviewing-the-pull-request"></a>끌어오기 요청 검토

끌어오기 요청에 문서화된 문제를 검토하려면 다음 절차를 따릅니다.

1. 게시 단계 양식의 **Microsoft 검토** 섹션에서 PR 링크를 선택하여 브라우저를 시작하고 이 PR에 대한 **개요**(홈) 페이지로 이동합니다. 다음 이미지는 Contoso 샘플 앱 제품의 심각한 문제 홈페이지 예를 보여 줍니다. 이 페이지에는 Azure 앱에서 발견된 검토 문제에 대한 유용한 요약 정보가 포함되어 있습니다.

    [![끌어오기 요청 홈페이지](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *펼치려면 이 이미지를 클릭하세요.*

1. (선택 사항) 창의 오른쪽에 있는 **정책** 섹션에서 문제 메시지(이 예제에서는 **정책 유효성 검사 실패**)를 선택하여 연관된 로그 파일을 비롯한 문제의 하위 수준 세부 정보를 조사합니다. 일반적으로 오류는 로그 파일의 맨 아래에 표시됩니다.

1. 홈페이지의 왼쪽 메뉴에서 **파일**을 선택하여 이 제품의 기술 자산을 구성하는 목록 파일을 표시합니다. Microsoft 검토자가 발견된 심각한 문제를 설명하는 주석을 추가해 두었을 것입니다. 다음 예제에서는 두 가지 문제가 발견되었습니다.

    [![끌어오기 요청 홈페이지](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *펼치려면 이 이미지를 클릭하세요.*

1. 왼쪽 트리에서 각 주석 노드를 선택하여 주변 코드 컨텍스트의 주석으로 이동합니다. 팀 프로젝트의 소스 코드를 수정하여 주석에서 설명된 문제를 해결합니다.

>[!Note]
>검토 팀의 Azure DevOps 환경에서는 제품의 기술 자산을 편집할 수 없습니다. 게시자에게는 포함된 소스 코드에 대한 읽기 전용 환경입니다. 그러나 Microsoft 검토 팀을 위해 주석에 회신을 남길 수 있습니다.

   다음 예제에서는 게시자가 첫 번째 문제를 검토, 수정 및 회신했습니다.

   ![첫 번째 수정 및 주석 회신](./media/first-comment-reply.png)

## <a name="next-steps"></a>다음 단계

검토 PR에 문서화된 심각한 문제를 해결한 후에는 [Azure 앱 제품을 다시 게시](./create-new-azure-apps-offer.md#publish)해야 합니다.
