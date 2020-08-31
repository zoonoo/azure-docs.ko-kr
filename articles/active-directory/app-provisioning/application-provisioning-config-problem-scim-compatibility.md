---
title: SCIM (도메인 간 Id 관리) 2.0 프로토콜 준수에 대 한 시스템의 알려진 문제-Azure AD
description: SCIM 2.0을 지원하는 비갤러리 애플리케이션을 Azure AD에 추가할 때 발생하는 일반적인 프로토콜 호환성 문제를 해결하는 방법
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 08/05/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 39a4cbd5ffd04aa3346b1ce4f3b73576b92c4d3b
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065491"
---
# <a name="known-issues-and-resolutions-with-scim-20-protocol-compliance-of-the-azure-ad-user-provisioning-service"></a>Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 준수와 관련하여 알려진 문제 및 해결 방법

Azure AD(Active Directory)는 [SCIM(System for Cross-Domain Identity Management) 2.0 프로토콜 사양](https://tools.ietf.org/html/draft-ietf-scim-api-19)에 정의된 인터페이스를 가진 웹 서비스가 향하는 애플리케이션 또는 시스템에 사용자 및 그룹을 자동으로 프로비전할 수 있습니다. 

Azure AD의 SCIM 2.0 프로토콜 지원은 [Using System for Cross-Domain Identity Management (SCIM) to automatically provision users and groups from Azure Active Directory to applications](use-scim-to-provision-users-and-groups.md)(SCIM(System for Cross-Domain Identity Management)을 사용하여 Azure Active Directory에서 애플리케이션으로 사용자 및 그룹 자동 프로비전)에서 설명합니다. 여기서는 SCIM 2.0을 지원하는 애플리케이션으로 Azure AD에서 사용자 및 그룹을 자동 프로비전하기 위해 구현하는 프로토콜의 특정 부분을 나열합니다.

이 문서에서는 Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 준수와 관련한 현재와 과거의 문제 및 이러한 문제를 해결하는 방법을 설명합니다.

## <a name="understanding-the-provisioning-job"></a>프로 비전 작업 이해
프로 비전 서비스는 작업 개념을 사용 하 여 응용 프로그램에 대해 작동 합니다. JobID는 [진행률 표시줄](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)에서 찾을 수 있습니다. 모든 새 프로 비전 응용 프로그램은 "scim"으로 시작 하는 jobID를 사용 하 여 만들어집니다. Scim 작업은 서비스의 현재 상태를 나타냅니다. 이전 작업에는 ID "customappsso"가 있습니다. 이 작업은 2018의 서비스 상태를 나타냅니다. 

갤러리에서 응용 프로그램을 사용 하는 경우 작업에는 일반적으로 앱의 이름 (예: zoom 눈송이, dataBricks 등)이 포함 됩니다. 갤러리 응용 프로그램을 사용 하는 경우이 설명서를 건너뛸 수 있습니다. 이는 주로 jobID SCIM 또는 customAppSSO를 사용 하는 비 갤러리 응용 프로그램에 적용 됩니다.

## <a name="scim-20-compliance-issues-and-status"></a>SCIM 2.0 준수 문제 및 상태
아래 표에서 fixed로 표시 된 항목은 SCIM 작업에서 적절 한 동작을 찾을 수 있음을 의미 합니다. 변경한 내용에 대해 이전 버전과의 호환성을 보장 하기 위해 노력 했습니다. 그러나 이전 동작을 구현 하지 않는 것이 좋습니다. 새 구현에 새로운 동작을 사용 하 고 기존 구현을 업데이트 하는 것이 좋습니다.

> [!NOTE]
> 2018에서 변경 된 내용을 적용 하려면 customappsso 동작으로 되돌릴 수 있습니다. 2018 이후 변경 된 내용에 대해 Url을 사용 하 여 이전 동작으로 되돌릴 수 있습니다. 이전 jobID로 되돌리거나 플래그를 사용 하 여 이전에 변경한 내용에 대해 이전 버전과의 호환성을 보장 하기 위해 노력 했습니다. 그러나 앞에서 설명한 대로 이전 동작을 구현 하지 않는 것이 좋습니다. 새 구현에 새로운 동작을 사용 하 고 기존 구현을 업데이트 하는 것이 좋습니다.

| **SCIM 2.0 준수 문제** |  **고정?** | **수정 날짜**  |  **이전 버전과의 호환성** |
|---|---|---|
| Azure AD가 애플리케이션의 SCIM 엔드포인트 URL의 루트에 위치하려면 “/scim”이 필요함  | 예  |  2018년 12월 18일 | customappSSO로 다운 그레이드 |
| 확장 특성은 특정 이름 앞에 콜론 “:” 대신 점 “.” 표기법을 사용함 |  예  | 2018년 12월 18일  | customappSSO로 다운 그레이드 |
| 다중 값 특성의 패치 요청에 있는 경로 필터 구문이 잘못됨 | 예  |  2018년 12월 18일  | customappSSO로 다운 그레이드 |
| 그룹 생성 요청에 있는 스키마 URI가 잘못됨 | 예  |  2018년 12월 18일  |  customappSSO로 다운 그레이드 |
| 규정 준수를 보장 하기 위해 패치 동작 업데이트 (예: 부울로 활성 및 적절 한 그룹 멤버 자격 제거) | 아니요 | TBD| 미리 보기 플래그 사용 |

## <a name="flags-to-alter-the-scim-behavior"></a>SCIM 동작을 변경 하기 위한 플래그
기본 SCIM 클라이언트 동작을 변경 하려면 응용 프로그램의 테 넌 트 URL에서 아래 플래그를 사용 합니다.

:::image type="content" source="media/application-provisioning-config-problem-scim-compatibility/scim-flags.jpg" alt-text="이후 동작에 대 한 SCIM 플래그입니다.":::

* 다음 URL을 사용 하 여 패치 동작을 업데이트 하 고 SCIM 준수를 보장 합니다 (예:를 부울로 활성화 하 고 적절 한 그룹 멤버 자격 제거). 이 동작은 현재 플래그를 사용 하는 경우에만 사용할 수 있지만 다음 몇 달 동안 기본 동작이 됩니다.
  * **URL (SCIM 규격):** AzureAdScimPatch062020
  * **SCIM RFC 참조:** 
    * https://tools.ietf.org/html/rfc7644#section-3.5.2
  * **행동**
  ```json
   PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
   {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "remove",
            "path": "members[value eq \"16b083c0-f1e8-4544-b6ee-27a28dc98761\"]"
        }
    ]
   }

    PATCH https://[...]/Groups/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "members",
            "value": [
                {
                    "value": "10263a6910a84ef9a581dd9b8dcc0eae"
                }
            ]
        }
    ]
    } 

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].value",
            "value": "someone@contoso.com"
        },
        {
            "op": "replace",
            "path": "emails[type eq \"work\"].primary",
            "value": true
        },
        {
            "op": "replace",
            "value": {
                "active": false,
                "userName": "someone"
            }
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "replace",
            "path": "active",
            "value": false
        }
    ]
    }

    PATCH https://[...]/Users/ac56b4e5-e079-46d0-810e-85ddbd223b09
    {
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ],
    "Operations": [
        {
            "op": "add",
            "path": "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department",
            "value": "Tech Infrastructure"
        }
    ]
    }
   
  ```

  * **다운 그레이드 URL:** 새 SCIM 규격 동작이 비 갤러리 응용 프로그램에서 기본값이 되 면 다음 URL을 사용 하 여 이전 비 SCIM 규격 동작으로 롤백할 수 있습니다. AzureAdScimPatch2017
  


## <a name="upgrading-from-the-older-customappsso-job-to-the-scim-job"></a>이전 customappsso 작업에서 SCIM 작업으로 업그레이드
다음 단계를 수행 하면 기존 customappsso 작업이 삭제 되 고 새 scim 작업이 생성 됩니다. 
 
1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. Azure Portal의 **Azure Active Directory > 엔터프라이즈 애플리케이션** 섹션에서 기존의 SCIM 애플리케이션을 찾아 선택합니다.
3. 기존의 SCIM 앱의 **속성** 섹션에서 **개체 ID**를 복사합니다.
4. 새 웹 브라우저 창에서 https://developer.microsoft.com/graph/graph-explorer로 이동하여 앱이 추가된 Azure AD 테넌트의 관리자로 로그인합니다.
5. Graph 탐색기에서 아래 명령을 실행하여 프로비저닝 작업의 ID를 찾습니다. “[object-id]”를 세 번째 단계에서 복사한 서비스 주체 ID(개체 ID)로 바꿉니다.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs` 

   ![작업 가져오기](media/application-provisioning-config-problem-scim-compatibility/get-jobs.PNG "작업 가져오기") 


6. 결과에서 “customappsso” 또는 “scim”으로 시작하는 전체 “ID” 문자열을 복사합니다.
7. 백업을 수행할 수 있도록, 아래 명령을 실행하여 특성 매핑 구성을 검색합니다. 이전과 동일한 [object-id]를 사용하고, [job-id]를 마지막 단계에서 복사한 프로비저닝 작업 ID로 바꿉니다.
 
   `GET https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]/schema`
 
   ![스키마 가져오기](media/application-provisioning-config-problem-scim-compatibility/get-schema.PNG "스키마 가져오기") 

8. 마지막 단계의 JSON 출력을 복사하여 텍스트 파일에 저장합니다. JSON에는 이전 앱에 추가한 모든 사용자 지정 특성 매핑이 포함 되어 있으며, 약 1000 줄의 JSON이 있어야 합니다.
9. 아래 명령을 실행하여 프로비저닝 작업을 삭제합니다.
 
   `DELETE https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[job-id]`

10. 아래 명령을 실행하여 최신 서비스 수정 사항이 있는 새 프로비저닝 작업을 만듭니다.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
 `{   templateId: "scim"   }`
   
11. 마지막 단계의 결과에서 “scim”으로 시작하는 전체 “ID” 문자열을 복사합니다. 필요에 따라 다음 명령을 실행 하 여 [새-job-id]를 복사한 새 작업 ID로 바꾸고 #7 단계의 JSON 출력을 요청 본문으로 입력 하 여 이전 특성 매핑을 다시 적용 합니다.

 `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs/[new-job-id]/schema`
 `{   <your-schema-json-here>   }`

12. 첫 번째 웹 브라우저 창으로 돌아가서 애플리케이션에 해당하는 **프로비저닝** 탭을 선택합니다.
13. 구성을 확인한 후 프로비저닝 작업을 시작합니다. 

## <a name="downgrading-from-the-scim-job-to-the-customappsso-job-not-recommended"></a>SCIM 작업에서 customappsso 작업으로 다운 그레이드 (권장 하지 않음)
 이전 동작으로 다시 다운 그레이드 하는 것이 좋지만 customappsso가 어떤 업데이트를 활용 하지 않으며 영원히 지원 되지 않을 수도 있으므로 권장 하지 않습니다. 

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. Azure Portal의 **Azure Active Directory > 엔터프라이즈 애플리케이션 > 애플리케이션 만들기** 섹션에서 새 **비갤러리** 애플리케이션을 만듭니다.
3. 새 사용자 지정 앱의 **속성** 섹션에서 **개체 ID**를 복사합니다.
4. 새 웹 브라우저 창에서 https://developer.microsoft.com/graph/graph-explorer로 이동하여 앱이 추가된 Azure AD 테넌트의 관리자로 로그인합니다.
5. Graph 탐색기에서 아래 명령을 실행하여 앱의 프로비저닝 구성을 초기화합니다.
   “[object-id]”를 세 번째 단계에서 복사한 서비스 주체 ID(개체 ID)로 바꿉니다.

   `POST https://graph.microsoft.com/beta/servicePrincipals/[object-id]/synchronization/jobs`
   `{   templateId: "customappsso"   }`
 
6. 첫 번째 웹 브라우저 창으로 돌아가서 애플리케이션에 해당하는 **프로비저닝** 탭을 선택합니다.
7. 일반적으로 하는 방법대로 사용자 프로비저닝 구성을 완료합니다.


## <a name="next-steps"></a>다음 단계
[SaaS 애플리케이션에 대한 사용자 프로비전 및 프로비전 해제 구성에 대해 자세히 알아보기](user-provisioning.md)
