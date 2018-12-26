---
title: Cortana intelligence | Microsoft Docs
description: Cortana Intelligence 제품을 게시하는 방법을 소개합니다.
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
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 74cde720af012b3355b813cf6da2b2bdf10b9b8e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257210"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>클라우드 파트너 포털을 사용하여 Cortana Intelligence 제품 게시

이 문서에서는 클라우드 파트너 포털을 사용하여 Cortana Intelligence 제품을 게시하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

클라우드 파트너 포털에서는 역할 기반 포털 액세스를 지원하므로 여러 참가자들이 제품 게시 작업을 공동으로 수행할 수 있습니다. 자세한 내용은 [클라우드 포털 사용자 관리](./cloud-partner-portal-manage-users.md)를 참조하세요.

게시자 계정을 대신하여 제품을 게시하려면 \"소유자\" 역할의 사용자 중 한 명이 [사용 약관](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft 개인정보처리방침](https://www.microsoft.com/privacystatement/default.aspx) 및 [Microsoft Azure 인증 프로그램 계약](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/) 준수에 동의해야 합니다.

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Cortana Intelligence 제품 만들기를 시작하려면

모든 필수 조건을 충족했으면 Cortana Inteligence 제품 작성을 시작할 준비가 완료된 것입니다.

1.  [클라우드 파트너 포털](http://cloudpartner.azure.com/)에 로그인합니다.
2.  왼쪽 탐색 모음에서 **+ 새 제품**을 선택합니다.
3. **Cortana Intelligence**를 선택합니다.
4. **새 제품** 보기에서 **편집기** 탭을 선택하여 다음 옵션을 구성합니다.
    -   제품 설정
    -   기술 정보
    -   상점 세부 정보
    -   연락처

    위의 각 옵션을 클릭하면 작성해야 하는 양식이 표시됩니다.  각 양식의 필수 필드는 빨간색 별표(\*)로 표시됩니다.

## <a name="to-configure-offer-settings"></a>제품 설정을 구성하려면

제품 설정에서는 제품 ID, 게시자 ID, 제품 이름 등 제품에 대한 기본 정보를 제공합니다.

### <a name="offer-id"></a>제품 ID

게시자 프로필 내에서 제품의 고유 ID입니다.
이 ID는 제품 URL에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝날 수 없으며, 최대 50자까지 포함할 수 있습니다. 
>[!Note]
>제품이 라이브 상태가 되면 이 필드가 잠깁니다.

**예제:**

게시자 **contoso**가 제품 ID *sample-Cortana Intelligence*로 만드는 제품은 AppSource에 "https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-Cortana Intelligence*?tab=Overview"로 표시됩니다.

### <a name="publisher-id"></a>게시자 ID

게시자 프로필의 드롭다운 목록입니다. 이 제품을 게시할 게시자 프로필을 선택하는 데 사용됩니다.

>[!Note]
>제품이 라이브 상태가 되면 이 필드가 잠깁니다.

### <a name="name"></a>이름

제품의 표시 이름입니다. 이 이름은 [AppSource](https://appsource.microsoft.com)에 표시됩니다. 최대 50문자를 포함할 수 있습니다.

제품 설정에 필요한 정보를 모두 입력한 후에 **저장**을 선택하여 제품 옵션의 기술 정보 부분으로 이동합니다.

## <a name="to-configure-technical-info"></a>기술 정보를 구성하려면

이 보기에서 제품 페이지에 표시할 기술 정보를 입력합니다. 이 보기에는 다음 필드가 적용됩니다.

### <a name="partner-mpn-id"></a>파트너 MPN ID

등록된 Microsoft 파트너의 경우 [파트너 웹 사이트](https://partners.microsoft.com/)로 이동하여 로그인하면 조직 파트너 ID를 받을 수 있습니다. **파트너 MPN ID**에는 해당 ID를 입력합니다.

### <a name="analytics-components-used"></a>사용되는 분석 구성 요소

제품이 사용하는 모든 구성 요소를 선택합니다. 구성 요소는 하나 이상 선택해야 합니다. VM에서 실행되는 MRS, HDInsight Premium 또는 SQL 2016 R 서비스의 MRS 라이선스 중 하나를 사용 중인 경우에만 **Microsoft R**을 선택합니다.

### <a name="additional-components-used"></a>사용되는 추가 구성 요소

솔루션에서 사용하는 기타 모든 구성 요소를 선택합니다.

### <a name="customer-name-using-solution"></a>솔루션을 사용하는 고객 이름

프로덕션 환경에서 이 솔루션을 사용하는 고객 이름을 입력합니다. 

>[!Note]
>이 정보는 AppSource에 게시되지 않으며 솔루션 평가에만 사용됩니다.

### <a name="azure-consumption-requirement-met"></a>Azure Consumption 요구 사항 충족 여부 확인

솔루션이 Azure Suite 구성 요소 고객당 매월 $1,000의 수익을 창출하는지 여부 또는 Microsoft R을 사용하는지 여부를 나타냅니다.

>[!Note]
>이 정보는 AppSource에 게시되지 않으며 솔루션 평가에만 사용됩니다.

### <a name="demo-video-url"></a>데모 비디오 URL

사전 판매 팀이 고객에게 표시할 수 있는 솔루션/앱의 데모 비디오 URL을 입력합니다. 

>[!Note]
>이 정보는 AppSource에 게시되지 않으며 솔루션 평가에만 사용됩니다.

#### <a name="demo-video-guidelines"></a>데모 비디오 지침

- 비디오 길이는 15분 미만이어야 합니다.
- 비디오는 솔루션 기능을 보여 주는 최대한 짧게 편집된 녹화본이어야 합니다. 
- 비디오에서는 사용자용 기능의 주요 측면을 부각하고 고급 분석 기능 통합을 중점적으로 설명해야 합니다. 
- 데모 비디오는 고객에게 공개적으로 제공되지 **않으며** 솔루션을 잠재 고객에게 표시*할 수 있는* 방법을 나타내는 것으로 예상됩니다. 그런 점에서 스크립트로 되어 있으며 반복 가능합니다.
- 표준 비디오 형식(예: MPEG)을 내보내는 화면 녹화 도구를 사용하여 비디오를 만듭니다. 

다음 지침에서는 비즈니스용 Skype를 사용하여 비디오를 만드는 방법을 설명합니다. 

1. [회의 시작](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [데스크톱 공유](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [기록 시작](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. 기록을 중단한 후 [기록 관리자를 사용하여 기록을 게시](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)합니다.

기록된 비디오를 공유 URL 생성이 가능한 서비스에 업로드합니다. 예를 들어 [OneDrive 또는 Sharepoint의 게스트 링크](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)를 사용할 수 있습니다.

### <a name="supported-regions"></a>지원되는 지역

솔루션이 지원하는 모든 지역을 선택합니다. 지역은 하나 이상 선택해야 합니다.

### <a name="power-bi-desktop-file-pbix"></a>Power BI 데스크톱 파일(.pbix)

해당하는 경우 .pbix 파일을 업로드합니다. 데이터를 파일로 가져왔으며 외부에서 참조하지 않는지 확인합니다. 포함된 보고서는 자동으로 생성됩니다.

### <a name="solution-architecture"></a>솔루션 아키텍처

솔루션의 아키텍처를 자세히 설명하는 문서를 업로드합니다. 솔루션 아키텍처 다이어그램 업로드 지침은 [고급 분석 솔루션 워크플로 템플릿](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx)을 참조하세요.

>[!Note]
>이 정보는 AppSource에 게시되지 않으며 솔루션 평가에만 사용됩니다.

### <a name="select-segments"></a>세그먼트 선택

모든 관련 산업 세그먼트를 선택합니다. 앱이 목록에 없는 세그먼트용이라면 **기타** 필드에 세그먼트 이름을 입력합니다. 세그먼트 이름은 3단어까지만 입력할 수 있습니다.

### <a name="select-business-processes"></a>비즈니스 프로세스 선택

솔루션을 가장 적절하게 설명하는 비즈니스 프로세스를 선택합니다. 앱이 목록에 없는 프로세스용이라면 **기타** 필드에 프로세스 이름을 입력합니다. 프로세스 이름은 3단어까지만 입력할 수 있습니다.

### <a name="trial-info"></a>평가판 정보

-   **SaaS 평가판 URL:** 앱의 평가판 환경 URL을 입력합니다.
-   **시험 사용 평가판 URL:** 앱의 시험 사용 환경 URL을 입력합니다.

평가판에 대한 자세한 내용은 이 문서의 다음 섹션에서 **앱 유형**을 참조하세요.

기술 정보에 필요한 정보를 모두 입력한 후에 **저장**을 선택하여 제품의 상점 정보 부분으로 이동합니다. 

## <a name="to-configure-storefront-details"></a>상점 정보를 구성하려면

### <a name="offer-summary"></a>제품 요약

제품 가치 제안의 요약입니다. 제품 검색 페이지에 표시됩니다. 요약의 최대 길이는 100자입니다.

### <a name="offer-description"></a>제품 설명

앱 세부 정보 페이지에 표시되는 설명입니다.
설명의 최대 길이는 1300자입니다.

이 필드에는 \<p\>, \<h1\>, \<h2\>, \<li\> 등의 태그가 포함된 html 콘텐츠를 사용할 수 있으므로 콘텐츠를 훨씬 더 멋지게 꾸밀 수 있습니다. 게시 포털 팀에서는 게시자가 상점 정보 미리 보기를 확인하여 대화식으로 콘텐츠를 꾸밀 수 있는 기능을 추가하기 위한 작업을 진행하고 있습니다. 현재는 [http://htmledit.squarefree.com](http://htmledit.squarefree.com/) 등의 모든 온라인 실시간 html 도구를 사용하여 설명이 표시되는 모양을 확인할 수 있습니다.

텍스트를 가치 제안에 따라 하위 섹션으로 나누고 각 하위 섹션을 부제로 강조 표시하는 설명 형식을 사용하면 효율적입니다. 일반적으로 방문자는 "제품 요약" 필드와 부제를 통해 앱이 다루는 내용과 앱을 고려해야 하는 이유를 한눈에 살펴봅니다. 따라서 사용자의 주의를 끌고 구체적인 정보를 계속 살펴볼 이유를 제공하는 것이 중요합니다.

#### <a name="partner-examples"></a>파트너 예제

- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)(Neal Analytics Inventory 최적화)
- [Plexure Retail Personalization](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)(Plexure Retail 개인 설정)
- [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)(AvePoint 대국민 서비스)

### <a name="industries"></a>산업

앱에 가장 부합되는 산업을 선택합니다. 여러 산업과 관련이 있는 앱의 경우 이 필드를 비워 둡니다.

### <a name="categories"></a>범주

앱과 관련이 있는 범주를 선택합니다. 범주는 2개까지 선택할 수 있습니다.

### <a name="app-type"></a>앱 유형

앱이 AppSource에서 지원할 평가판의 유형을 선택합니다. 다음 평가판 중 하나를 선택합니다.
- **무료**: 앱이 무료로 제공됩니다.
- **평가판**: 고객이 AppSource에서 지정된 기간 동안 앱을 사용해 볼 수 있습니다.
- **평가판 요청**: 고객이 앱 평가판을 요청할 수 있습니다.

파트너는 AppSource에서 두 가지 유형의 평가판 환경을 제공할 수 있습니다.

- **CLT(고객 진행 평가판)** 라고도 하는 **평가판** 옵션은 다음 유형 중 하나일 수 있습니다. 
    - SaaS 평가판
        - 고객이 게시자 또는 파트너가 제공하는 URL로 이동할 수 있습니다. 고객은 AAD에 페더레이션된 SSO로 이동하여 시간 제한 평가판 환경을 사용합니다.
        - 이 앱은 사용자 구성/데이터를 다른 사용자에게서 격리하는 다중 테넌트 지원 SaaS 앱이거나, 이 환경에서는 읽기 전용 작업을 사용하는 하위 집합만 제공합니다.

        **예제:**

        - [AFS POP 소매점 운영 솔루션](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [AvePoint Citizen Services](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview). 이 앱은 선택한 가상 사용자 집합용의 명확한 경로를 포함하는 큐레이팅된 환경을 제공합니다.

     - 시험 사용
        - AppSource에서 인스턴스화할 수 있는 Azure Resource Manager 템플릿을 사용하여 게시자나 파트너의 솔루션 또는 솔루션 하위 집합을 패키지로 만들 수 있습니다. AppSource는 사용 기간을 제한하고 인스턴스 핫/콜드 풀을 유지 관리하는 등의 방식으로 파트너 구독에서 앱을 관리할 수 있습니다.
        - 이 옵션을 선택하는 경우 Microsoft에서 게시자를 지원하기 위해 템플릿과 샘플 코드를 제공할 수 있습니다.

        **예제:**

        - [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)(Neal Analytics Inventory 최적화)

- **평가판 요청**(**파트너 진행 평가판/PLT**) 옵션을 선택하는 경우 파트너가 추가 작업을 진행할 수 있도록 고객이 연락처 정보 양식을 작성해야 합니다. 그러면 파트너가 추가 작업을 통해 앱 평가판이나 데모를 제공합니다. 자세한 내용을 확인하려면 [AppSource 평가판 환경 연습](https://aka.ms/trialexperienceforwebapps) 비디오에서 대략적인 개요를 확인하세요.

>[!Note]
>데이터에 따르면 **파트너 진행 평가판**보다 **고객 진행 평가판**의 잠재 고객 생성 가능성이 더 높은 것으로 나타났습니다.


### <a name="help-link-for-your-app"></a>앱 도움말 링크

앱의 도움말 정보가 포함된 페이지의 URL을 입력합니다.

### <a name="supported-countriesregions"></a>지원되는 국가/지역

이 필드의 정보에 따라 제품 평가판을 사용할 수 있는 국가/지역이 결정됩니다.

![지원되는 국가/지역](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>지원되는 언어

앱에서 지원하는 언어를 선택합니다. 앱에서 이 목록에 없는 추가 언어를 지원하는 경우 제품을 게시하고 다른 언어 지원 관련 정보를 <appsource@microsoft.com>에 전자 메일로 보내 주시기 바랍니다.

### <a name="app-version"></a>앱 버전

앱의 버전 번호를 입력합니다.

### <a name="products-your-app-works-with"></a>앱과 연동되는 제품

앱과 연동되는 특정 제품을 입력합니다. 제품은 3개까지 입력할 수 있습니다. 제품을 입력하려면 새로 만들기 옆의 **더하기 기호**를 선택합니다. 그러면 빈 텍스트 필드가 자동으로 생성됩니다. 이 필드에 앱이 연동되는 제품의 이름을 입력합니다.

### <a name="hide-key"></a>숨기기 키

공용 보기에서 제품을 숨기기 위해 제품 미리 보기 URL과 결합되는 키이며 암호는 아닙니다. 아무 영숫자 문자열이나 입력할 수 있습니다.

### <a name="offer-logo-small"></a>제품 로고(작은 크기)

이 로고는 앱의 검색 페이지에 표시됩니다. png 이미지 형식만 사용할 수 있습니다. 이미지 크기는 48x48픽셀입니다.

### <a name="offer-logo-large"></a>제품 로고(큰 크기)

이 로고는 앱의 세부 정보 페이지에 표시됩니다. png 이미지 형식만 사용할 수 있습니다. 이미지 크기는 48x48픽셀입니다.

### <a name="video"></a>비디오

최대 4개의 동영상을 업로드할 수 있습니다. 업로드하려는 각 비디오에 대해 다음 정보를 입력합니다.
- 비디오 이름
- URL(YouTube 또는 Vimeo URL만 입력 가능)
- 비디오와 연결할 썸네일. 썸네일은 png 이미지 형식을 사용해야 하며 크기는 1280x720픽셀이어야 합니다. 

새 비디오를 추가하려면 다음 화면 캡처에 나와 있는 것처럼 **+ 새로 만들기**를 선택합니다.

![새 비디오 추가](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>문서

문서는 3개까지 업로드할 수 있습니다. 각 문서는 PDF 파일 형식을 사용해야 합니다. 새 문서를 추가하려면 다음 작업을 수행합니다.

- **+ 새로 만들기**를 선택합니다.
- 문서 이름을 입력합니다.
- **업로드**를 선택하여 문서를 업로드합니다.

![새 문서 추가](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>개인정보취급방침

앱 개인정보취급방침의 URL을 입력합니다.

### <a name="terms-of-use"></a>사용 약관

앱 사용 약관을 입력합니다. AppSource 고객은 이러한 약관에 동의해야 앱 평가판을 사용할 수 있습니다.

>[!Note]
>이 필드에는 html 콘텐츠 <p\>, <h1\>, <li\> 등의 HTML 태그를 사용하는 콘텐츠를 입력할 수 있습니다. 이러한 태그를 사용하여 콘텐츠의 서식을 지정할 수 있습니다. 

### <a name="lead-destination"></a>잠재 대상 고객

잠재 고객을 저장할 CRM 시스템을 선택합니다. 

사용 중인 CRM 시스템이 Salesforce, Marketo, Microsoft Dynamics CRM 중 하나라면 **Azure 테이블**을 선택합니다. 

사용하려는 CRM 시스템에 대한 추가 정보를 확인하려면 지원되는 시스템에 해당하는 다음 링크 중 하나를 선택합니다.

-   [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
