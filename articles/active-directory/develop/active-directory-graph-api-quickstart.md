---
title: Azure AD Graph API 사용 방법
description: Azure AD(Active Directory) Graph API는 OData REST API 엔드포인트을 통해 Azure AD에 프로그래밍 방식으로 액세스할 수 있게 합니다. 애플리케이션은 Azure AD Graph API를 사용하여 디렉터리 데이터 및 개체에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행할 수 있습니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e417d29341ecd175f5ef97761292568b200fc64e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884514"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>방법: Azure AD Graph API 사용

> [!IMPORTANT]
> Azure AD 그래프 API 대신 [Microsoft 그래프를](https://developer.microsoft.com/graph) 사용하여 Azure Active Directory(Azure AD) 리소스에 액세스하는 것이 좋습니다. 이제 Microsoft는 Azure AD Graph API를 더 이상 개선하지 않을 것이며 Microsoft Graph에 주력하고 있습니다. Azure AD 그래프 API가 여전히 적절할 수 있는 시나리오는 매우 제한되어 있습니다. 자세한 내용은 Microsoft [그래프 또는 Azure AD 그래프](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) 블로그 게시물을 참조하고 [Azure AD 그래프 앱을 Microsoft 그래프로 마이그레이션합니다.](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview)

Azure AD 그래프 API는 OData REST API 끝점을 통해 Azure AD에 대한 프로그래밍 방식으로 액세스를 제공합니다. 애플리케이션은 Azure AD Graph API를 사용하여 디렉터리 데이터 및 개체에 대한 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행할 수 있습니다. 예를 들어 Azure AD Graph API를 사용하여 새 사용자를 만들고, 사용자의 속성을 보거나 업데이트하거나, 사용자의 암호를 변경하거나, 역할 기반 액세스에 대한 그룹 구성원 자격 확인, 사용자를 비활성화하거나 삭제할 수 있습니다. Azure AD 그래프 API 기능 및 응용 프로그램 시나리오에 대한 자세한 내용은 [Azure AD 그래프 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) 및 Azure [AD 그래프 API 필수 구성 조건을](https://msdn.microsoft.com/library/hh974476.aspx)참조하십시오. Azure AD 그래프 API는 직장 또는 학교/조직 계정에서만 작동합니다.

이 문서는 Azure AD Graph API에 적용됩니다. Microsoft Graph API와 관련된 유사한 정보는 [Microsoft Graph API 사용](https://developer.microsoft.com/graph/docs/concepts/use_the_api)을 참조하세요.

## <a name="how-to-construct-a-graph-api-url"></a>Graph API URL을 생성하는 방법

Graph API에서 디렉터리 데이터 및 CRUD 작업을 수행하려는 개체(즉, 리소스 또는 엔터티)에 액세스하려면 OData(개방형 데이터) 프로토콜을 기반으로 하는 URL을 사용할 수 있습니다. Graph API에서 사용되는 URL은 서비스 루트, 테넌트 식별자, 리소스 경로 및 쿼리 문자열 옵션의 네 가지 주요 부분으로 구성됩니다. `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. 다음 URL을 예로 들어보겠습니다. `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **서비스 루트**: Azure AD Graph API에서 서비스 루트는 항상 https://graph.windows.net입니다.
* **테넌트 식별자**: 이 섹션은 위 예제에서 contoso.com이라는 확인된(등록된) 도메인 이름일 수 있습니다. 테넌트 개체 ID 또는 "myorganization" 또는 "me" 별칭일 수도 있습니다. 자세한 내용은 [Azure AD 그래프 API의 엔터티 및 작업 해결을](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)참조하십시오.
* **리소스 경로**: URL의 이 섹션에서는 상호 작용할 리소스(사용자, 그룹, 특정 사용자 또는 특정 그룹 등)를 식별합니다. 위의 예에서는 해당 리소스 집합을 처리하는 최상위 수준 "그룹"입니다. "사용자/{objectId}" 또는 "사용자/사용자 PrincipalName"과 같은 특정 엔터티를 해결할 수도 있습니다.
* **쿼리 매개 변수**: 물음표(?)는 리소스 경로 섹션과 쿼리 매개 변수 섹션을 구분합니다. Azure AD 그래프 API의 모든 요청에 "api 버전" 쿼리 매개 변수가 필요합니다. 또한 Azure AD Graph API는 OData 쿼리 옵션, 즉 **$filter**, **$orderby**, **$expand**, **$top** 및 **$format**을 지원합니다. **$count**, **$inlinecount** 및 **$skip** 쿼리 옵션은 현재 지원되지 않습니다. 자세한 내용은 [Azure AD Graph API에서 지원되는 쿼리, 필터 및 페이징 옵션](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)을 참조하세요.

## <a name="graph-api-versions"></a>Graph API 버전

"api 버전" 쿼리 매개 변수에서 그래프 API 요청에 대 한 버전을 지정 합니다. 버전 1.5 이상의 경우 숫자 버전 값 api-version=1.6을 사용합니다. 이전 버전의 경우 YYYY-MM-DD 형식을 준수하는 날짜 문자열을 사용합니다(예: api-version=2013-11-08). 미리 보기 기능의 경우 문자열 "베타"를 사용하십시오. 예를 들어, api 버전=베타. 그래프 API 버전 간의 차이점에 대한 자세한 내용은 [Azure AD 그래프 API 버전 조정](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)을 참조하십시오.

## <a name="graph-api-metadata"></a>Graph API 메타데이터

Azure AD 그래프 API 메타데이터 파일을 반환하려면 URL에서 테넌트 식별자 다음에 "$metadata" 세그먼트를 추가합니다. `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6` 웹 브라우저의 주소 표시줄에 이 URL을 입력하면 메타데이터를 볼 수 있습니다. 반환된 CSDL 메타데이터 문서에서는 엔터티 및 복합 형식, 해당 속성 및 요청한 Graph API 버전에서 노출되는 함수 및 작업에 대해 설명합니다. api-version 매개 변수를 생략하면 가장 최신 버전에 대한 메타데이터가 반환됩니다.

## <a name="common-queries"></a>일반 쿼리

[Azure AD Graph API 공통 쿼리에는](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) 디렉터리에서 최상위 리소스에 액세스하는 데 사용할 수 있는 쿼리와 디렉터리에서 작업을 수행하는 쿼리를 포함하여 Azure AD Graph와 함께 사용할 수 있는 일반적인 쿼리가 나열됩니다.

예를 들어 `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` 는 contoso.com 디렉터리에 대한 회사 정보를 반환합니다.

또는 `https://graph.windows.net/contoso.com/users?api-version=1.6` 는 contoso.com 디렉터리에 있는 모든 사용자 개체를 나열합니다.

## <a name="using-the-azure-ad-graph-explorer"></a>Azure AD Graph Explorer 사용
Azure AD Graph API용 Azure AD Graph Explorer를 사용하여 애플리케이션을 빌드할 때 디렉터리 데이터를 쿼리할 수 있습니다.

다음 스크린샷은 Azure AD Graph Explorer로 이동하고, 로그인한 다음, `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6`을 입력하여 로그인한 사용자의 디렉터리에 있는 모든 사용자를 표시하는 경우 나타나는 출력입니다.

![Azure AD 그래프 API 탐색기의 예제 출력](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Azure AD Graph Explorer 로드**: 도구를 로드하려면 [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)으로 이동합니다. **로그인**을 클릭하고 Azure AD 계정 자격 증명으로 로그인하여 테넌트에 대해 Azure AD Graph Explorer를 실행합니다. 고유한 테넌트에 대해 Azure AD Graph Explorer를 실행하는 경우 사용자 또는 관리자가 로그인 중에 동의해야 합니다. Office 365 구독이 있는 경우 Azure AD 테넌트를 자동으로 보유합니다. Office 365에 로그인하는 데 사용하는 자격 증명은 실제로 Azure AD 계정이며, Azure AD Graph Explorer에서 이러한 자격 증명을 사용할 수 있습니다.

**쿼리 실행**: 쿼리를 실행하려면 요청 텍스트 상자에서 쿼리를 입력하고 **GET**을 클릭하거나 **Enter** 키를 클릭합니다. 결과가 응답 상자에 표시됩니다. 예를 들어 `https://graph.windows.net/myorganization/groups?api-version=1.6`는 로그인한 사용자의 디렉터리에 있는 모든 그룹 개체를 나열합니다.

Azure AD Graph Explorer의 다음 기능 및 제한 사항을 확인합니다.

* 리소스 집합에 대한 자동 완성 기능. 이 기능을 확인하려면 회사 URL이 표시되는 요청 텍스트 상자를 클릭합니다. 드롭다운 목록에서 리소스 집합을 선택할 수 있습니다.
* 요청 기록.
* 별칭을 다루는 "나"와 "myorganization"를 지원합니다. 예를 들어 `https://graph.windows.net/me?api-version=1.6`를 사용하여 로그인한 사용자의 사용자 개체를 반환하거나 `https://graph.windows.net/myorganization/users?api-version=1.6`를 사용하여 로그인한 사용자의 디렉터리에 있는 모든 사용자를 반환할 수 있습니다.
* `POST`, `GET`, `PATCH` 및 `DELETE`를 사용하여 사용자 고유의 디렉터리에 대한 전체 CRUD 작업을 지원합니다.
* 응답 헤더 섹션입니다. 이 섹션은 쿼리를 실행할 때 발생하는 문제를 해결하는 데 활용할 수 있습니다.
* 확장 및 축소 기능이 있는 응답에 대한 JSON 뷰어입니다.
* 썸네일 사진 표시 또는 업로드를 지원하지 않습니다.

## <a name="using-fiddler-to-write-to-the-directory"></a>Fiddler를 사용하여 디렉터리에 쓰기

이 빠른 시작 가이드의 목적을 위해 Fiddler 웹 디버거를 사용하여 Azure AD 디렉터리에 대해 '쓰기' 작업을 수행하는 연습을 수행할 수 있습니다. 예를 들어 사용자의 프로필 사진을 가져와서 업로드할 수 있습니다(Azure AD Graph Explorer에서는 불가능). 자세한 내용은 Fiddler를 설치하려면 [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)을 참조하십시오.

아래 예제에서는 Fiddler 웹 디버거를 사용하여 Azure AD 디렉터리에 새 보안 그룹 'MyTestGroup'을 만듭니다.

**액세스 토큰 얻기**: Azure AD Graph에 액세스하려면 클라이언트가 먼저 Azure AD에 인증해야 합니다. 자세한 내용은 [Azure AD](authentication-scenarios.md)에 대한 인증 시나리오를 참조하세요.

**쿼리 작성 및 실행**: 다음 단계를 완료합니다.

1. Fiddler Web Debugger를 열고 **작성자** 탭으로 전환합니다.
2. 새 보안 그룹을 만들 것이므로 풀다운 메뉴에서 HTTP 방법으로 **게시**를 선택합니다. 그룹 개체의 작업 및 사용 권한에 대한 자세한 내용은 [Azure AD 그래프 REST API 참조](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)내의 [그룹을](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) 참조하십시오.
3. **Post** 옆의 필드에 요청 URL `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`을 입력합니다.
   
   > [!NOTE]
   > {mytenantdomain}을 고유한 Azure AD 디렉터리의 도메인 이름으로 바꿔야 합니다.

4. Post 풀다운 바로 아래에 있는 필드에 다음 HTTP 헤더를 입력합니다.
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > &lt;액세스 토큰&gt;을 Azure AD 디렉터리에 대한 액세스 토큰으로 대체합니다.

5. **요청 본문** 필드에 다음 JSON을 입력합니다.
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    그룹을 만드는 방법에 대한 자세한 내용은 [그룹 만들기](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)를 참조하세요.

그래프에서 노출되는 Azure AD 엔터티 및 형식에 대한 자세한 정보 및 그래프를 사용 하 여 수행할 수 있는 작업에 대 한 정보는 [Azure AD 그래프 REST API 참조를](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)참조 하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* [Azure AD 그래프 API 권한 범위에](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) 대해 자세히 알아보기
