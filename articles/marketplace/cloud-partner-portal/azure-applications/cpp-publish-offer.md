---
title: Azure 애플리케이션 제품 게시 - Azure Marketplace | Microsoft Docs
description: Azure 애플리케이션 제품을 Azure Marketplace에 게시하는 프로세스 및 단계를 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: pbutlerm
ms.openlocfilehash: ad62b306849b2291c81399cedc1634057e2eec9d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54828097"
---
# <a name="publish-azure-application-offer"></a>Azure 애플리케이션 제품 게시

**새 제품** 페이지에 정보를 제공하여 제품을 만든 후 제품을 게시할 수 있습니다. **게시**를 선택하여 게시 프로세스를 시작합니다.

다음 다이어그램은 제안을 "라이브 상태"로 만들기 위한 게시 프로세스의 주요 단계를 보여줍니다.

![제품 게시 단계](./media/offer-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>게시 단계에 대한 자세한 설명

다음 표에는 각 게시 단계가 나열 및 설명되어 있으며, 각 단계를 완료하는 데 걸리는 예상 시간이 제공됩니다.  “일” 단위의 예상 시간은 업무일로 정의되며 주말과 휴일은 제외됩니다.

|  **게시 단계**           | **Time**    | **설명**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| 필수 구성 요소 유효성 검사         | 15분 미만    | 제안 정보 및 제안 설정의 유효성이 검사됩니다.                        |
| 영향을 받는 매출 설정의 유효성 검사 | 15분 미만  | 제안의 Azure 리소스 사용량 특성이 확인됩니다.             |
| 인증                  | 1일 미만     | Azure 인증 팀에서 제안을 분석합니다. 이 제품은 바이러스, 맬웨어, 안전 규정 준수 및 보안 문제에 대해 검사됩니다. 모든 자격 조건을 충족하는지 제품을 확인합니다. 자세한 내용은 [필수 구성 요소](./cpp-prerequisites.md)를 참조하세요. 문제가 발견되면 피드백이 제공됩니다. |
| 시험 사용 유효성 검사          | 2시간 미만   | (선택 사항) 시험 사용이 있는 경우, Microsoft는 시험 사용을 배포하고 복제할 수 있는지 확인합니다.  |
| 패키징 및 잠재 고객 생성 등록 | 1시간 미만  | 제품의 기술 자산이 고객의 사용에 맞게 패키지되고, 잠재 고객 시스템이 구성 및 배포됩니다. |
|  게시자 승인             |  수동    | 제안이 라이브 상태가 되기 전에 최종 게시자가 검토 및 확인합니다. 이제 제품 미리 보기를 사용할 수 있습니다.  선택한 구독에 제안을 배포하여(제안 정보 단계에서) 모든 요구 사항을 충족하는지 확인할 수 있습니다.  제품을 확인한 후 제품이 다음 단계로 넘어갈 수 있도록 **라이브로 전환**을 선택합니다. |
| Microsoft 검토                | 7-14일 | Microsoft는 Azure 애플리케이션을 전반적으로 검토하고, 문제가 발견되면 메일을 보냅니다.  이 단계의 기간은 애플리케이션의 복잡성, 발견된 문제, 문제에 대응하는 신속성에 따라 달라집니다.  |
| 라이브                           | 1일 미만 | 제품이 릴리스되고, 지정한 지역에 복제되고, 공개적으로 사용할 수 있게 됩니다. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |
 
Cloud 파트너 포털의 제품 **상태** 탭에서 게시 프로세스를 모니터링할 수 있습니다.

![Azure 앱 제품의 상태 탭](./media/offer-status-tab.png)

게시 프로세스가 완료되면 제품이 [Microsoft Azure Marketplace 애플리케이션 범주](https://azuremarketplace.microsoft.com/marketplace/apps/)에 나열됩니다.


## <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

제품의 게시 상태를 표시하는 것 외에도 **상태** 탭에는 오류 메시지 및 **Microsoft 검토** 단계의 피드백이 표시됩니다.  일반적으로 검토 문제는 PR(끌어오기 요청)로 참조됩니다.  문제에 대한 세부 정보를 포함하는 온라인 VSTS(Visual Studio Team Services, [Azure DevOps](https://azure.microsoft.com/services/devops/)로 이름이 바뀜) 항목에 각 PR이 연결되어 있습니다.  다음 이미지에는 검토 PR 참조 예가 나와 있습니다.  더 복잡한 상황의 경우 검토 및 지원 팀이 메일을 보낼 수 있습니다. 

![검토 피드백이 표시된 상태 탭](./media/status-tab-ms-review.png)

제품이 게시 프로세스를 계속 진행하려면 먼저 보고된 각 문제를 해결해야 합니다.  다음 다이어그램은 이 피드백 프로세스와 게시 프로세스 간의 관계를 보여 줍니다.

![VSTS 피드백이 있는 게시 단계](./media/pub-flow-vsts-access.png)


### <a name="vsts-access"></a>VSTS 액세스

검토 피드백에서 참조된 VSTS 항목을 보려면 게시자에게 적절한 권한이 부여되어야 합니다.  그러지 않으면 새 게시자에게 `401 - Not Authorized` 응답 페이지가 표시됩니다.  제품 검토 VSTS 시스템에 대한 액세스를 요청하려면 다음 단계를 수행합니다.

1. 다음 정보를 수집합니다.
    - 게시자 이름 및 ID
    - 제품 유형(Azure 앱), 제품 이름 및 SKU ID
    - 끌어오기 요청 링크(예: `https://solutiontemplates.visualstudio.com/marketplacesolutions/_git/contoso/pullrequest/<number>`) 이 URL은 알림 메시지 또는 401 응답 페이지의 주소에서 검색할 수 있습니다.
    - 액세스 권한을 부여할 게시 조직의 개인 메일 주소.  이 메일 주소는 Cloud 파트너 포털에서 게시자로 등록할 때 제공한 소유자 주소를 포함해야 합니다.
2. 지원 인시던트를 만듭니다.  Cloud 파트너 포털의 제목 표시줄에서 **도움말** 단추를 선택한 다음, 메뉴에서 **지원**을 선택합니다.  기본 웹 브라우저가 시작되고 Microsoft 새 지원 인시던트 페이지로 이동합니다.  먼저 로그인해야 할 수도 있습니다.
3. **문제 유형**을 **마켓플레이스 온보딩**으로, **범주**를 **액세스 문제**로 지정한 다음, **요청 시작**을 선택합니다.

    ![지원 티켓 범주](./media/support-incident1.png)

4. **1/2단계** 페이지에서 연락처 정보를 제공하고 **계속**을 선택합니다.
5. **2/2단계** 페이지에서 인시던트 제목(예: `Request VSTS access`)을 지정하고 첫 번째 단계(위)에서 수집한 정보를 제공합니다.  계약을 읽고 동의한 다음, **제출**을 선택합니다.

인시던트 만들기에 성공하면 확인 페이지가 표시됩니다.  참조할 수 있도록 확인 정보를 저장합니다.  Microsoft 지원은 업무일 기준으로 며칠 이내에 액세스 요청에 응답합니다.


## <a name="next-steps"></a>다음 단계

Azure 앱이 게시되고 나면 [기존 제품을 업데이트](./cpp-update-existing-offer.md)하여 변화하는 비즈니스 또는 기술 요구 사항을 반영할 수 있습니다. 
