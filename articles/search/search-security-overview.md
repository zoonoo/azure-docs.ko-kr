---
title: "Azure Search에서 데이터 및 작업 보호 | Microsoft Docs"
description: "Azure Search 보안은 Azure Search 필터에서 사용자 및 그룹 보안 식별자를 통해 SOC 2 규정 준수, 암호화, 인증 및 ID 액세스를 기반으로 합니다."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/14/2017
ms.author: heidist
ms.openlocfilehash: 23616c70a5fd336b743f5acfad2601a6c3e23fc4
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2017
---
# <a name="data-security-and-controlled-access-to-azure-search-operations"></a>Azure Search 작업에 대한 데이터 보안 및 제어된 액세스

Azure Search는 물리적 보안, 암호화된 전송, 암호화된 저장소 및 플랫폼 전체 소프트웨어 보호 기능이라는 광범위한 보안 아키텍처로 확장된 [SOC 2와 호환](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)됩니다. 운영상 Azure Search는 인증된 요청을 허용합니다. 필요에 따라 콘텐츠에 사용자당 액세스 제어를 추가할 수 있습니다. 이 문서에서는 각 계층의 보안에 대해 다루지만 주로 Azure Search에서 데이터 및 작업을 보호하는 방법을 중점적으로 설명합니다.

![보안 계층의 블록 다이어그램](media/search-security-overview/azsearch-security-diagram.png)

Azure Search가 Azure 플랫폼의 보호 및 보호 기능을 상속하는 반면 서비스 자체에서 사용되는 기본 메커니즘은 키 기반 인증입니다. 여기서 키 형식은 액세스 수준을 결정합니다. 키는 읽기 전용 액세스에 대한 관리자 키 또는 쿼리 키입니다.

서비스에 대한 액세스 권한은 키에 의해 제공된 사용 권한(전체 또는 읽기 전용) 및 작업 범위를 정의하는 컨텍스트의 교집합 영역을 기반으로 합니다. 모든 요청은 필수 키, 작업 및 개체로 구성됩니다. 함께 연결하는 경우 두 개의 사용 권한 수준 및 컨텍스트는 서비스 작업에 전체 스펙트럼 보안을 충분히 제공할 수 있습니다. 

## <a name="physical-security"></a>물리적 보안

Microsoft 데이터 센터는 업계 최고의 물리적 보안을 제공하고 광범위한 포트폴리오의 표준 및 규정을 준수합니다. 자세한 내용을 보려면 [글로벌 데이터 센터](https://www.microsoft.com/cloud-platform/global-datacenters) 페이지로 이동하거나 데이터 센터 보안에 대한 짧은 비디오를 시청하세요.

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]

## <a name="encrypted-transmission-and-storage"></a>암호화된 전송 또는 저장

Azure Search는 HTTPS 포트 443에서 수신 대기합니다. 플랫폼 전체에서 Azure 서비스에 대한 연결이 암호화됩니다. 

Azure Search는 인덱스 및 다른 구문에 사용되는 백 엔드 저장소에서 해당 플랫폼의 암호화 기능을 활용합니다. 전체 [AICPA SOC 2 준수](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html)는 Azure Search를 제공하는 모든 데이터 센터에서 모든 검색 서비스(신규 및 기존)에 제공됩니다. 전체 보고서를 검토하려면 [Azure - 및 Azure Government SOC 2 형식 II 보고서](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports)로 이동하세요.

암호화는 내부적으로 관리되고 전역적으로 적용되는 암호화 키를 사용하므로 투명하게 유지됩니다. 특정 검색 서비스 또는 인덱스에서 해제하거나 키를 직접 관리하고 고유한 기능을 직접 제공할 수 없습니다. 

## <a name="azure-wide-logical-security"></a>Azure 전체 논리 보안

일부 보안 메커니즘은 Azure Stack에서 사용할 수 있으므로 사용자가 만든 Azure Search 리소스에 자동으로 사용할 수 있습니다.

+ [삭제를 방지하기 위해 구독 또는 리소스 수준 잠금](../azure-resource-manager/resource-group-lock-resources.md)
+ [정보 및 관리 작업에 대한 액세스를 제어하는 RBAC(역할 기반 액세스 제어)](../active-directory/role-based-access-control-what-is.md)

모든 Azure 서비스는 모든 서비스에서 일관된 수준의 액세스를 설정하기 위해 RBAC(역할 기반 액세스 제어)를 지원합니다. 예를 들어 관리 키와 같은 중요한 데이터는 소유자 및 참가자 역할에서만 확인할 수 있는 반면 서비스 상태는 역할에 관계없이 모든 구성원이 확인할 수 있습니다. RBAC는 소유자, 참가자 및 독자 역할을 제공합니다. 기본적으로 모든 서비스 관리자는 소유자 역할의 구성원입니다.

## <a name="service-authentication"></a>서비스 인증

Azure Search는 자체 인증 방법을 제공합니다. 인증은 각 요청에서 발생하고 작업 범위를 결정하는 액세스 키를 기반으로 합니다. 유효한 액세스 키는 요청이 신뢰할 수 있는 엔터티에서 시작된 증명으로 간주됩니다. 

서비스당 인증은 모든 권한 또는 쿼리 전용이라는 두 가지 수준으로 존재합니다. 키의 형식은 적용할 액세스 수준을 결정합니다.

|키|설명|제한|  
|---------|-----------------|------------|  
|관리자|서비스를 관리하며 **인덱스**, **인덱서** 및 **데이터 원본**을 만들고 삭제하는 기능을 비롯한 모든 작업에 전체 권한을 부여합니다.<br /><br /> 포털에서 *기본* 및 *보조* 키라고 하는 두 개의 관리자 **api-key**는 서비스를 만들 때 생성되고 요청 시 개별적으로 다시 생성할 수 있습니다. 키가 두 개이면 서비스에 대해 액세스를 지속하는 데 하나의 키를 사용하는 동안 다른 키를 롤오버할 수 있습니다.<br /><br /> 관리자 키는 HTTP 요청 헤더에서만 지정됩니다. URL에 관리자 **api-key**를 배치할 수 없습니다.|서비스당 최대 2개|  
|쿼리|인덱스 및 문서에 대한 읽기 전용 액세스를 부여하며 일반적으로 검색 요청을 수행하는 클라이언트 응용 프로그램에 배포됩니다.<br /><br /> 쿼리 키는 요청 시 생성됩니다. 포털에서 수동으로 만들거나 [관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement/)를 통해 프로그래밍 방식으로 만들 수 있습니다.<br /><br /> 검색, 제안 또는 조회 작업의 HTTP 요청 헤더에서 쿼리 키를 지정할 수 있습니다. 또는 쿼리 키를 URL에 매개 변수로 전달할 수 있습니다. 클라이언트 응용 프로그램이 요청을 생성하는 방법에 따라 키를 쿼리 매개 변수로 전달하는 것이 쉬울 수 있습니다.<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2016-09-01&api-key=A8DA81E03F809FE166ADDB183E9ED84D`|서비스당 50개|  

 시각적으로는 관리자 키 및 쿼리 키 간의 구분이 없습니다. 두 키는 임의로 생성된 32개의 영숫자 문자로 구성된 문자열입니다. 응용 프로그램에서 지정된 키의 형식을 잃어버린 경우 [포털에서 키 값을 확인](https://portal.azure.com)하거나 [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)를 사용하여 값 및 키 형식을 반환할 수 있습니다.  

> [!NOTE]  
>  요청 URI에서 `api-key`와 같은 중요한 데이터를 전달하는 낮은 수준의 보안 사례로 간주됩니다. 따라서 Azure Search에서는 쿼리 키만을 쿼리 문자열의 `api-key`로 허용하며, 인덱스 콘텐츠가 공개적으로 제공되어야 하는 경우가 아니면 중요한 데이터를 요청 URI에서 전달해서는 안 됩니다. 일반적으로 `api-key`를 요청 헤더로 전달하는 것이 좋습니다.  

### <a name="how-to-find-the-access-keys-for-your-service"></a>서비스의 액세스 키를 찾는 방법

포털에서 또는 [관리 REST API](https://docs.microsoft.com/rest/api/searchmanagement/)를 통해 액세스 키를 가져올 수 있습니다. 자세한 내용은 [키 관리](search-manage.md#manage-api-keys)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 구독에 대한 [검색 서비스](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)를 나열합니다.
3. 서비스를 선택하고 서비스 페이지에서 **설정** >**키**를 찾아 관리자 및 쿼리 키를 봅니다.

![포털 페이지, 설정, 키 섹션](media/search-security-overview/settings-keys.png)

## <a name="index-access"></a>액세스 인덱싱

Azure Search에서 개별 인덱스는 보안 개체가 아닙니다. 대신 인덱스에 대한 액세스는 작업의 컨텍스트와 함께 서비스 계층(읽기 또는 쓰기 액세스)으로 결정됩니다.

최종 사용자 액세스의 경우 응용 프로그램에서 쿼리 키를 사용하여 연결하도록 쿼리 요청을 구성할 수 있습니다. 여기서는 모든 요청을 읽기 전용으로 설정하고 앱에서 사용하는 특정 인덱스를 포함합니다. 쿼리 요청에서 동시에 인덱스를 조인하거나 여러 인덱스에 액세스한다는 개념은 없으므로 모든 요청은 정의에 따라 단일 인덱스를 대상으로 지정합니다. 따라서 쿼리 요청 자체의 구조(키 및 단일 대상 인덱스)는 보안 경계를 정의합니다.

인덱스에 대한 관리자 및 개발자 액세스는 구분되지 않습니다. 둘 다 서비스에서 관리되는 개체를 만들고, 삭제하고, 업데이트하는 쓰기 액세스 권한이 필요합니다. 서비스에 대한 관리자 키가 있는 모든 사용자는 동일한 서비스에서 모든 인덱스를 읽기, 수정 또는 삭제할 수 있습니다. 인덱스를 우발적이거나 악의적인 삭제로부터 보호하기 위해 코드 자산에 대한 내부 원본을 제어하여 원치 않는 삭제나 수정을 되돌리는 문제를 해결합니다. Azure Search에는 가용성을 보장하기 위해 클러스터 내에서 장애 조치 기능이 있지만 인덱스를 만들거나 로드하는 데 사용되는 전용 코드를 저장하거나 실행하지 않습니다.

인덱스 수준에서 보안 경계를 요구하는 다중 테넌트 솔루션의 경우 이러한 솔루션에는 일반적으로 중간 계층이 포함됩니다. 고객은 인덱스 격리를 처리하기 위해 이를 사용합니다. 다중 테넌트 사용 사례에 대한 자세한 내용은 [다중 테넌트 SaaS 응용 프로그램 및 Azure Search에 대한 디자인 패턴](search-modeling-multitenant-saas-applications.md)을 참조하세요.

## <a name="admin-access-from-client-apps"></a>클라이언트 앱에서 관리자 액세스

Azure Search 관리 REST API는 Azure 리소스 관리자의 확장이며 종속성을 공유합니다. 이와 같이 Active Directory는 Azure Search 서비스 관리의 필수 구성 요소입니다. 클라이언트 코드의 모든 관리 요청은 요청이 리소스 관리자에 도달하기 전에 Azure Active Directory를 사용하여 인증되어야 합니다.

인덱스 만들기(Azure Search Service REST API) 또는 문서 검색(Azure Search Service REST API)과 같은 Azure Search Service 끝점에 대한 데이터 요청은 요청 헤더에서 api-key를 사용합니다.

응용 프로그램 코드가 검색 인덱스나 문서에 대한 데이터 작업뿐만 아니라 서비스 관리 작업을 처리하는 경우 코드에서 Azure Search에 고유한 액세스 키 및 리소스 관리자에서 필요한 Active Directory 인증 방법과 같은 두 가지 인증 방식을 구현합니다. 

Azure Search에서 요청을 구성하는 방법에 대한 정보는 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)를 참조하세요. 리소스 관리자를 위한 인증 요구 사항에 대한 자세한 내용은 [리소스 관리자 인증 API를 사용하여 구독에 액세스](../azure-resource-manager/resource-manager-api-authentication.md)를 참조하세요.

## <a name="user-access-to-index-content"></a>인덱스 콘텐츠에 대한 사용자 액세스

인덱스의 콘텐츠에 대한 사용자당 액세스는 쿼리에 대한 보안 필터를 통해 구현되며 지정된 보안 ID와 연결된 문서를 반환합니다. 미리 정의된 역할 및 역할 할당 대신 ID 기반 액세스 제어는 ID에 따라 문서 및 콘텐츠의 검색 결과를 잘라내는 필터로 구현됩니다. 다음 표에서는 권한이 없는 콘텐츠의 검색 결과를 잘라내는 방법에 대한 두 가지 방법을 설명합니다.

| 접근 방식 | 설명 |
|----------|-------------|
|[ID 필터에 따라 보안 조정](search-security-trimming-for-azure-search.md)  | 사용자 ID 액세스 제어를 구현하기 위한 기본 워크플로를 문서화합니다. 인덱스에 보안 식별자를 추가하는 방법을 다루고 금지된 콘텐츠의 결과를 잘라내는 해당 필드에 대한 필터링을 설명합니다. |
|[Azure Active Directory ID에 따라 보안 조정](search-security-trimming-for-azure-search-with-aad.md)  | 이 문서는 이전 문서에서 확장되어 Azure 클라우드 플랫폼에서 제공하는 [체험 서비스](https://azure.microsoft.com/free/) 중 하나인 AAD(Azure Active Directory)에서 ID를 검색하는 단계를 제공합니다. |

## <a name="table-permissioned-operations"></a>표: 권한이 지정된 작업

다음 표에서는 Azure Search에서 허용되는 작업 및 특정 작업에 대한 액세스의 잠금을 해제하는 키를 요약합니다.

| 작업 | 권한 |
|-----------|-------------------------|
| 서비스 만들기 | Azure 구독 소유자|
| 서비스 크기 조정 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자  |
| 서비스 삭제 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자 |
| 서비스에서 개체를 만들고, 수정하고, 삭제합니다. <br>인덱스 및 구성 요소 부분(분석기 정의, 점수 매기기 프로필, CORS 옵션 포함), 인덱서, 데이터 원본, 동의어, 확인기 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자  |
| 인덱스 쿼리 | 관리자 또는 쿼리 키(RBAC 적용되지 않음) |
| 통계, 개수 및 개체의 목록을 반환하는 등 쿼리 시스템 정보입니다. | 관리자 키, 리소스에 대한 RBAC(소유자, 참가자, 독자) |
| 관리자 키 관리 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자 |
| 쿼리 키 관리 |  관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자 RBAC 독자는 쿼리 키를 볼 수 있습니다. |


## <a name="see-also"></a>참고 항목

+ [.NET 시작(관리자 키를 사용하여 인덱스를 만드는 방법을 보여줍니다.)](search-create-index-dotnet.md)
+ [REST 시작(관리자 키를 사용하여 인덱스를 만드는 방법을 보여줍니다.)](search-create-index-rest-api.md)
+ [Azure Search 필터를 사용하여 ID 기반 액세스 제어](search-security-trimming-for-azure-search.md)
+ [Azure Search 필터를 사용하여 Active Directory ID 기반 액세스 제어](search-security-trimming-for-azure-search-with-aad.md)
+ [Azure Search의 필터](search-filters.md)