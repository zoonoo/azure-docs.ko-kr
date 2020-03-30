---
title: 범위를 벗어난 사용자 삭제 건너뛰기 | 마이크로 소프트 문서
description: 범위를 벗어난 사용자 프로비저닝 해제의 기본 동작을 재정의하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522452"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>범위를 벗어난 사용자 계정의 삭제 건너뛰기

기본적으로 Azure AD 프로비전 엔진은 범위를 벗어난 사용자를 삭제하거나 사용하지 않도록 설정합니다. 그러나 Workday to AD 사용자 인바운드 프로비저닝과 같은 특정 시나리오의 경우 이 동작이 예상되지 않을 수 있으므로 이 기본 동작을 재정의할 수 있습니다.  

이 가이드는 Microsoft 그래프 API 및 Microsoft 그래프 API 탐색기를 사용하여 범위를 벗어난 계정의 처리를 제어하는 ***SkipOutOfScopeDeletions*** 플래그를 설정하는 방법을 설명합니다. 
* ***SkipOutOfScopeDeletions가*** 0(false)으로 설정된 경우 범위를 벗어난 계정은 대상에서 비활성화됩니다.
* ***SkipOutOfScopeDeletions가*** 1(true)으로 설정된 경우 범위를 벗어난 계정은 대상에서 비활성화되지 않습니다.이 플래그는 *프로비저닝 앱* 수준에서 설정되고 그래프 API를 사용하여 구성할 수 있습니다. 

이 구성은 Workday to *Active Directory 사용자 프로비전* 앱과 함께 널리 사용되므로 아래 단계에는 Workday 응용 프로그램의 스크린샷이 포함됩니다. 그러나 이것은 또한 같은 **다른 모든 애플 리케이션과** 함께 사용할 수 있습니다 (ServiceNow, 세일즈 포스, 드롭 박스, 기타).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1단계: 프로비저닝 앱 서비스 보안 주체 ID(개체 ID) 검색

1. Azure [포털을](https://portal.azure.com)시작하고 프로비저닝 응용 프로그램의 속성 섹션으로 이동합니다. 예를 들어 *Workday를 AD 사용자 프로비저닝 응용 프로그램 매핑으로* 내보내려면 해당 앱의 속성 섹션으로 이동합니다. 
1. 프로비전 앱의 속성 섹션에서 ‘개체 ID’ 필드와 연결된 GUID 값을 복사합니다.** 이 값은 앱의 **ServicePrincipalId**라고도 하고 Graph Explorer 작업에서 사용됩니다.

   ![Workday 앱 서비스 주체 ID](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2 단계: 마이크로소프트 그래프 탐색기로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 시작
1. “Microsoft에 로그인” 단추를 클릭하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.

    ![Graph 로그인](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>3단계: 기존 앱 자격 증명 및 연결 세부 정보 가져오기

Microsoft Graph Explorer에서 [servicePrincipalId]를 [1단계](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)에서 추출된 **ServicePrincipalId**로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![작업 쿼리 받기](./media/skip-out-of-scope-deletions/skip-03.png)

응답을 텍스트 파일로 복사합니다. 아래에 표시된 JSON 텍스트와 유사하며 배포와 관련된 노란색으로 강조 표시된 값입니다. 끝에 녹색으로 강조 표시된 선을 추가하고 파란색으로 강조 표시된 Workday 연결 암호를 업데이트합니다. 

   ![작업 응답 받기](./media/skip-out-of-scope-deletions/skip-04.png)

매핑에 추가할 JSON 블록은 다음과 같습니다. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>4 단계: SkipOutOfScopeDeletions 플래그로 비밀 끝점을 업데이트합니다.

그래프 탐색기에서 아래 명령을 실행하여 ***SkipOutOfScopeDeletions*** 플래그를 사용하여 비밀 끝점을 업데이트합니다. 

아래 URL에서 [servicePrincipalId]를 [1단계에서](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)추출한 **ServicePrincipalId로** 바꿉꿉입니다. 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
3단계에서 업데이트된 텍스트를 "요청 본문"으로 복사하고 헤더 "콘텐츠 유형"을 "헤더 요청"에서 "응용 프로그램/json"으로 설정합니다. 

   ![PUT 요청](./media/skip-out-of-scope-deletions/skip-05.png)

"쿼리 실행"을 클릭합니다. 

"성공 – 상태 코드 204"로 출력을 얻어야 합니다. 

   ![PUT 응답](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>5단계: 범위를 벗어난 사용자가 비활성화되지 않는지 확인

특정 사용자를 건너뛰도록 범위 지정 규칙을 업데이트하여 이 플래그로 인해 예상되는 동작이 발생하는지 테스트할 수 있습니다. 아래 예제에서는 새 범위 지정 규칙을 추가하여 ID 21173(이전 범위의 직원)이 있는 직원을 제외합니다. 

   ![범위 지정 예제](./media/skip-out-of-scope-deletions/skip-07.png)

다음 프로비저닝 주기에서 Azure AD 프로비저닝 서비스는 사용자 21173이 범위를 벗어났음을 식별하고 SkipOutScopeDeletions 속성이 활성화된 경우 해당 사용자의 동기화 규칙에 아래와 같이 메시지가 표시됩니다. 

   ![범위 지정 예제](./media/skip-out-of-scope-deletions/skip-08.png)


