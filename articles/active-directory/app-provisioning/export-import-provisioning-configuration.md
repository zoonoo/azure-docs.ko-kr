---
title: Graph API |를 사용 하 여 프로 비전 구성 내보내기 또는 가져오기 Microsoft Docs
description: Graph API를 사용 하 여 프로 비전 구성을 내보내고 가져오는 방법에 대해 알아봅니다.
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
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: af699fa2201bce5627426dcdefc1b98c1d885ae5
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066617"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Graph API를 사용 하 여 프로 비전 구성 내보내기 또는 가져오기

Microsoft Graph API 및 Graph 탐색기를 사용 하 여 사용자 프로 비전 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다. 여기에 캡처된 단계를 사용 하 여 프로 비전 구성의 백업을 만들 수도 있습니다. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1 단계: 프로 비전 App Service 보안 주체 ID (개체 ID)를 검색 합니다.

1. [Azure Portal](https://portal.azure.com)를 시작 하 고 프로 비전 응용 프로그램의 속성 섹션으로 이동 합니다. 예를 들어 *Workday를 AD 사용자 프로 비전 응용 프로그램* 매핑으로 내보내려는 경우 해당 앱의 속성 섹션으로 이동 합니다. 
1. 프로비전 앱의 속성 섹션에서 ‘개체 ID’ 필드와 연결된 GUID 값을 복사합니다. 이 값은 앱의 **ServicePrincipalId**라고도 하고 Graph Explorer 작업에서 사용됩니다.

   ![Workday 앱 서비스 주체 ID](./media/export-import-provisioning-configuration/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2 단계: Microsoft Graph 탐색기에 로그인

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) 시작
1. “Microsoft에 로그인” 단추를 클릭하고 Azure AD 전역 관리자 또는 앱 관리자 자격 증명을 사용하여 로그인합니다.

    ![Graph 로그인](./media/export-import-provisioning-configuration/wd_export_02.png)

1. 로그인에 성공하면 왼쪽 창에 사용자 계정 세부 정보가 표시됩니다.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3 단계: 프로 비전 앱의 프로 비전 작업 ID 검색

Microsoft Graph Explorer에서 [servicePrincipalId]를 **1단계**에서 추출된 [ServicePrincipalId](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

다음과 같은 응답이 제공됩니다. 응답에 있는 “id 특성”을 복사합니다. 이 값은 **ProvisioningJobId**이고 기본 스키마 메타데이터를 검색하는 데 사용됩니다.

   [![프로 비전 작업 ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>4 단계: 프로 비전 스키마 다운로드

Microsoft Graph Explorer에서 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꾸고 다음 GET 쿼리를 실행합니다.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

응답에서 JSON 개체를 복사하고 파일에 저장하여 스키마 백업을 만듭니다.

## <a name="step-5-import-the-provisioning-schema"></a>5 단계: 프로 비전 스키마 가져오기

> [!CAUTION]
> Azure Portal을 사용하여 변경할 수 없는 구성의 스키마를 수정해야 하거나 이전에 백업된 파일의 구성을 작동하는 유효한 스키마로 복원해야 하는 경우에만 이 단계를 수행합니다.

Microsoft Graph Explorer에서 다음 PUT 쿼리를 구성하고 [servicePrincipalId] 및 [ProvisioningJobId]를 이전 단계에서 검색된 ServicePrincipalId 및 ProvisioningJobId로 바꿉니다.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

“요청 본문” 탭에서 JSON 스키마 파일의 콘텐츠를 복사합니다.

   [![요청 본문](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

“요청 헤더” 탭에서 “application/json” 값과 함께 Content-Type 헤더 특성을 추가합니다.

   [![요청 헤더](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

“쿼리 실행” 단추를 클릭하여 새 스키마를 가져옵니다.
