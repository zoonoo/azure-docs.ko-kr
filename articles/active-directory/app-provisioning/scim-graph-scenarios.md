---
title: SCIM, Microsoft 그래프 및 Azure AD 프로비저닝 서비스를 사용하여 사용자에게 프로비전하고 필요한 데이터로 응용 프로그램을 보강합니다 | 마이크로 소프트 문서
description: SCIM과 Microsoft 그래프를 함께 사용하여 사용자에게 프로비전하고 필요한 데이터로 응용 프로그램을 보강합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087622"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>SCIM과 Microsoft 그래프를 함께 사용하여 사용자에게 프로비전하고 필요한 데이터로 응용 프로그램을 보강합니다.

**타겟 대상:** 이 문서는 Azure AD와 통합된 응용 프로그램을 빌드하는 개발자를 대상으로 합니다. 줌, ServiceNow 및 DropBox와 같은 기존 응용 프로그램을 통합하려는 다른 사람들을 위해 이 것을 건너 뛰고 응용 프로그램 특정 자습서를 검토 할 수 [있습니다.](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

**일반적인 시나리오**

> [!div class="checklist"]
> * 내 응용 프로그램에서 자동으로 사용자 만들기
> * 더 이상 액세스 권한이 없어야 할 때 응용 프로그램에서 사용자를 자동으로 제거합니다.
> * 프로비저닝을 위해 응용 프로그램을 여러 ID 공급자와 통합
> * 공유점, Outlook 및 Office와 같은 Microsoft 서비스의 데이터로 응용 프로그램을 보강합니다.
> * Azure AD 및 Active Directory에서 사용자 및 그룹을 자동으로 생성, 업데이트 및 삭제

![SCIM 그래프 의사 결정 트리](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>시나리오 1: 내 앱에서 자동으로 사용자 만들기
오늘날 IT 관리자는 누군가가 액세스해야 하거나 CSV 파일을 주기적으로 업로드해야 할 때마다 내 응용 프로그램에서 사용자 계정을 수동으로 만듭니다. 이 프로세스는 고객에게 시간이 많이 걸리고 응용 프로그램의 채택 속도가 느려집니다. 사용자를 만들기 위해 이름, 전자 메일 및 userPrincipalName과 같은 기본 [사용자](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) 정보만 있으면 됩니다. 또한 고객은 다양한 IdP를 사용하며 동기화 엔진과 각 IdP에 대한 사용자 지정 통합을 유지할 리소스가 없습니다. 

**권장 사항**: SCIM 준수 [/사용자](https://aka.ms/scimreferencecode) 엔드포인트를 지원합니다. 고객은 이 끝점을 쉽게 사용하여 Azure AD 프로비저닝 서비스와 통합하고 액세스가 필요할 때 사용자 계정을 자동으로 만들 수 있습니다. 엔드포인트를 한 번 빌드할 수 있으며 동기화 엔진을 유지 관리하지 않고도 모든 IdP와 호환됩니다. 사용자를 만들 수 있는 방법에 대한 아래 예제 요청을 확인하십시오.

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>시나리오 2: 내 앱에서 사용자 자동 제거
내 응용 프로그램을 사용하는 고객은 보안에 중점을 두며 직원이 더 이상 필요하지 않을 때 계정을 제거하는 거버넌스 요구 사항이 있습니다. 응용 프로그램에서 프로비저닝 해제를 자동화하는 방법은 무엇입니까?

**권장 사항:** SCIM 준수/사용자 엔드포인트를 지원합니다. Azure AD 프로비저닝 서비스는 사용자가 더 이상 액세스 권한이 없어야 할 때 비활성화및 삭제요청을 전송합니다. 사용자를 사용하지 않도록 설정및 삭제하는 것을 모두 지원하는 것이 좋습니다. 비활성화 및 삭제 요청의 모양은 아래 예제를 참조하세요. 

사용자 사용 안 함 설정
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
사용자 삭제
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>시나리오 3: 내 앱에서 그룹 구성원 자격 관리 자동화
내 응용 프로그램은 다양한 리소스에 액세스하기 위해 그룹에 의존하며 고객은 Azure AD에 있는 그룹을 다시 사용하려고 합니다. Azure AD에서 그룹을 가져오고 멤버 자격이 변경될 때 그룹을 업데이트유지하려면 어떻게 해야 합니까?  

**권장 사항:** SCIM 준수 /그룹 [끝점](https://aka.ms/scimreferencecode)지원 . Azure AD 프로비저닝 서비스는 응용 프로그램에서 그룹을 만들고 멤버 자격 업데이트를 관리하는 데 주의를 기울입니다. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>시나리오 4: 팀, Outlook 및 OneDrive와 같은 Microsoft 서비스의 데이터로 앱을 보강합니다.
내 응용 프로그램은 Microsoft Teams에 기본 제공되며 메시지 데이터에 의존합니다. 또한, 우리는 OneDrive에 사용자를위한 파일을 저장합니다. 이러한 서비스와 Microsoft 전체의 데이터로 응용 프로그램을 보강하는 방법은 무엇입니까?

**권장 사항:** [마이크로소프트 그래프는](https://docs.microsoft.com/graph/) 마이크로소프트 데이터에 액세스 하는 진입점이다. 각 워크로드는 필요한 데이터와 API를 노출합니다. Microsoft 그래프는 위의 시나리오에 대한 [SCIM 프로비저닝과](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) 함께 사용할 수 있습니다. SCIM을 사용하여 그래프로 호출하는 동안 기본 사용자 특성을 응용 프로그램에 프로비전하여 필요한 다른 데이터를 얻을 수 있습니다. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>시나리오 5: 팀, Outlook 및 Azure AD와 같은 Microsoft 서비스의 변경 내용을 추적합니다.
팀 및 Outlook 메시지의 변경 내용을 추적하고 실시간으로 대응할 수 있어야 합니다. 이러한 변경 내용을 응용 프로그램에 푸시하려면 어떻게 해야 합니까?

**권장 사항:** Microsoft 그래프는 다양한 리소스에 대한 [변경 알림](https://docs.microsoft.com/graph/webhooks) 및 변경 사항 추적을 제공합니다. 변경 알림의 다음 제한 사항에 유의하십시오.
- 이벤트 수신자가 이벤트를 승인했지만 어떤 이유로든 이벤트를 수행하지 못하면 이벤트가 손실될 수 있습니다.
- 이벤트 수신자가 이벤트를 승인했지만 어떤 이유로든 이벤트를 수행하지 못하면 이벤트가 손실될 수 있습니다.
- 변경 알림은 항상 [리소스 데이터를](https://docs.microsoft.com/graph/webhooks-with-resource-data) 포함하지 않습니다 위의 이유, 개발자는 종종 동기화 시나리오에 대한 변경 내용 추적과 함께 변경 알림을 사용합니다. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>시나리오 6: Azure AD에서 사용자 및 그룹을 프로비전합니다.
내 응용 프로그램은 Azure AD에서 고객이 필요로 하는 사용자에 대한 정보를 만듭니다. 이는 채용을 관리하는 것보다 HR 응용 프로그램, 사용자를 위해 전화 번호를 생성하는 통신 앱 또는 Azure AD에서 중요한 데이터를 생성하는 다른 앱일 수 있습니다. Azure AD의 사용자 레코드를 해당 데이터로 채우면 어떻게 해야 합니까? 

**추천** Microsoft 그래프는 현재와 통합하여 사용자를 Azure AD로 프로비전할 수 있는 /사용자 및 /그룹 끝점을 노출합니다. Azure Active Directory는 해당 사용자를 Active Directory로 다시 작성하는 것을 지원하지 않습니다. 

> [!NOTE]
> Microsoft에는 Workday 및 SuccessFactors와 같은 HR 응용 프로그램에서 데이터를 가져오는 프로비저닝 서비스가 있습니다. 이러한 통합은 Microsoft에서 빌드하고 관리합니다. 서비스에 새 HR 응용 프로그램을 온보딩하려면 [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)에서 요청할 수 있습니다. 

## <a name="related-articles"></a>관련 문서

- [동기화 Microsoft 그래프 설명서 검토](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Azure AD와 사용자 지정 SCIM 앱 통합](use-scim-to-provision-users-and-groups.md)
