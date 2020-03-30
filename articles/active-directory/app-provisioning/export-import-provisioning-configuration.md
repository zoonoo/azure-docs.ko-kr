---
title: 프로비저닝 구성을 내보내고 재해 복구를 위해 알려진 양호한 상태로 롤백합니다.| 마이크로 소프트 문서
description: 프로비저닝 구성을 내보내고 재해 복구를 위해 알려진 양호한 상태로 롤백하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051304"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>프로비저닝 구성내보내기 및 알려진 양호 상태로 롤백

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Azure 포털에서 프로비저닝 구성 내보내기 및 가져오기

### <a name="how-can-i-export-my-provisioning-configuration"></a>프로비저닝 구성을 내보내나요?
구성을 내보내려면 다음을 수행하십시오.
1. [Azure Portal](https://portal.azure.com/)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택합니다.
2. Azure **Active Directory** 창에서 **엔터프라이즈 응용 프로그램을** 선택하고 응용 프로그램을 선택합니다.
3. 왼쪽 탐색 창에서 **프로비저닝을 선택합니다.** 프로비저닝 구성 페이지에서 **특성 매핑을**클릭한 다음 **고급 옵션을 표시하고**마지막으로 **스키마를 검토합니다.** 그러면 스키마 편집기로 이동합니다. 
5. 페이지 상단의 명령 모음에서 다운로드를 클릭하여 스키마를 다운로드합니다.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>재해 복구 - 알려진 양호한 상태로 롤백
구성을 내보내고 저장하면 이전 버전의 구성으로 롤백할 수 있습니다. 프로비저닝 구성을 내보내고 나중에 특성 매핑또는 범위 지정 필터를 변경할 때 사용할 수 있도록 저장하는 것이 좋습니다. 위의 단계에서 다운로드한 JSON 파일을 열고 JSON 파일의 전체 내용을 복사한 다음 스키마 편집기에서 JSON 페이로드의 전체 내용을 교체한 다음 저장하기만 하면 됩니다. 활성 프로비저닝 주기가 있는 경우 완료되고 다음 주기에서 업데이트된 스키마를 사용합니다. 다음 주기는 새 구성에 따라 모든 사용자와 그룹을 다시 평가하는 초기 주기가 됩니다. 이전 구성으로 롤백할 때 다음을 고려하십시오.
* 사용자가 범위에 있어야 하는지 여부를 결정하기 위해 다시 평가됩니다. 범위 지정 필터가 변경된 경우 사용자는 더 이상 범위에 속하지 않습니다. 대부분의 경우 이 동작이 원하는 동작이지만 이를 방지하고 [범위를 벗어난 삭제](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) 기능을 사용할 수 있는 경우가 있습니다. 
* 프로비저닝 구성을 변경하면 서비스가 다시 시작되고 [초기 주기가](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental)트리거됩니다.


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Microsoft 그래프 API를 사용하여 프로비저닝 구성 내보내기 및 가져오기
Microsoft 그래프 API 및 Microsoft 그래프 탐색기를 사용하여 사용자 프로비저닝 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다. 여기에 캡처된 단계를 사용하여 프로비저닝 구성의 백업을 만들 수도 있습니다. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1단계: 프로비저닝 앱 서비스 보안 주체 ID(개체 ID) 검색

1. Azure [포털을](https://portal.azure.com)시작하고 프로비저닝 응용 프로그램의 속성 섹션으로 이동합니다. 예를 들어 *Workday를 AD 사용자 프로비저닝 응용 프로그램 매핑으로* 내보내려면 해당 앱의 속성 섹션으로 이동합니다. 
1. 프로비전 앱의 속성 섹션에서 ‘개체 ID’ 필드와 연결된 GUID 값을 복사합니다.** 이 값을 앱의 **ServicePrincipalId라고도** 하며 Microsoft 그래프 탐색기 작업에 사용됩니다.

   ![Workday 앱 서비스 주체 ID](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>2 단계: 마이크로소프트 그래프 탐색기로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 시작
1. “Microsoft에 로그인” 단추를 클릭하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.

    ![마이크로소프트 그래프 로그인](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3단계: 프로비저닝 앱의 프로비저닝 작업 ID 검색

Microsoft Graph Explorer에서 [servicePrincipalId]를 [1단계](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)에서 추출된 **ServicePrincipalId**로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

다음과 같은 응답이 제공됩니다. 응답에 있는 “id 특성”을 복사합니다. 이 값은 **ProvisioningJobId**이고 기본 스키마 메타데이터를 검색하는 데 사용됩니다.

   [![프로비저닝 작업 ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>4단계: 프로비저닝 스키마 다운로드

Microsoft Graph Explorer에서 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

응답에서 JSON 개체를 복사하고 파일에 저장하여 스키마 백업을 만듭니다.

### <a name="step-5-import-the-provisioning-schema"></a>5단계: 프로비저닝 스키마 가져오기

> [!CAUTION]
> Azure Portal을 사용하여 변경할 수 없는 구성의 스키마를 수정해야 하거나 이전에 백업된 파일의 구성을 작동하는 유효한 스키마로 복원해야 하는 경우에만 이 단계를 수행합니다.

Microsoft Graph Explorer에서 다음 PUT 쿼리를 구성하고 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꿉니다.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

“요청 본문” 탭에서 JSON 스키마 파일의 콘텐츠를 복사합니다.

   [![요청 본문](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

“요청 헤더” 탭에서 “application/json” 값과 함께 Content-Type 헤더 특성을 추가합니다.

   [![헤더 요청](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

“쿼리 실행” 단추를 클릭하여 새 스키마를 가져옵니다.
