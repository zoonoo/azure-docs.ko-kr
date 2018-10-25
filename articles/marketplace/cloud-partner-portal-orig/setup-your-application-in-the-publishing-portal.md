---
title: 게시 포털에서 응용 프로그램 설정 | Microsoft Docs
description: 클라우드 게시 포털에서 응용 프로그램을 설정하는 방법에 대한 지침입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 9386f0e0bf552789b027df96e9e54ebcf31d7d34
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807325"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>게시 포털에서 응용 프로그램 설정

이제 게시 포털에서 응용 프로그램을 설정할 준비가 되었습니다.

## <a name="login-and-create-a-new-offer"></a>로그인 및 새 제품 만들기

1. [클라우드 파트너 포털](http://cloudpartner.azure.com/)에 로그인합니다.
2. 왼쪽 탐색 모음에서 “+ 새 제품”을 클릭하고 “Dynamics 365 for Customer Engagement”를 선택합니다.

![새 제품 선택](./media/CRMScreenShot14.png)

3. 새 제품 “편집기” 보기가 자동으로 열리고 작성을 시작할 준비가 완료되었습니다.

![새 제품 화면](./media/CRMScreenShot15.png)

4. 작성해야 할 “양식”은 “편집기” 보기 내의 왼쪽에 표시됩니다. 각 “양식”은 작성해야 할 필드 집합으로 구성됩니다. 필수 필드에는 빨간색 별표(\*)가 표시됩니다.

Dynamics 365 for Customer Engagement 제품을 작성하기 위한 4개의 기본 양식이 있습니다.

* 제품 설정
* 기술 정보
* 상점 세부 정보
* 연락처

## <a name="fill-out-the-offer-settings-form"></a>제품 설정 양식 작성

제품 설정 양식은 제품 설정을 지정하는 기본 양식입니다. 아래에 서로 다른 필드를 설명합니다.

### <a name="offer-id"></a>제품 ID

게시자 프로필 내에서 제품의 고유 ID입니다. 이 ID는 제품 URL에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝내면 안 되며 최대 50문자를 포함할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.

예를 들어, 게시자 **“contoso”** 가 제품 ID가 **“sample-WebApp”** 인 제품을 만드는 경우 AppSource에 “https://appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview”로 표시됩니다.

### <a name="publisher-id"></a>게시자 ID

이 드롭다운 목록을 사용하여 이 제품을 게시할 게시자 프로필을 선택할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.

### <a name="name"></a>이름

제품에 대한 표시 이름입니다. [AppSource](https://appsource.microsoft.com/)에 표시할 이름입니다. 최대 50문자를 포함할 수 있습니다.

“저장”을 클릭하여 진행 상황을 저장합니다. 다음 단계는 제품에 대한 기술 정보를 추가하는 것입니다.

## <a name="fill-out-the-technical-info-form"></a>기술 정보 양식 작성


기술 정보 양식에서는 Dynamics 365 for Customer Engagement 솔루션에 특정한 정보를 입력합니다. 마우스로 가리키면 자세한 정보가 표시됩니다. 아래 예제를 참조하세요.

![기술 정보 화면](./media/CRMScreenShot16.png)

### <a name="application-info"></a>응용 프로그램 정보

대부분의 게시자는 위의 스크린샷에 따라 이러한 필드를 기본값, 사용자, 아니요, 아니요 및 빈 응용 프로그램 구성 URL로 남겨 둡니다.

### <a name="crm-package"></a>CRM 패키지

![CRM 패키지 정보](./media/CRMScreenShot17.png)

다음은 이러한 필드에 대한 설명입니다.

* 패키지의 파일 이름: CRM AppSource 패키지인 zip 파일을 만들 때 위 단계에서 만든 파일 이름입니다. 위의 예제에서는 “Microsoft\_ SamplePackage.zip”입니다.
* 패키지 위치의 URL: 위에서 지정한 패키지 파일 이름을 포함하는 Azure Storage 계정의 URL입니다. 위 섹션의 9단계에서 만든 URL입니다.
* 패키지 파일에 둘 이상의 CRM 패키지가 있는 경우: 여러 패키지가 포함된 여러 CRM 버전을 지원하는 경우**에만** [예]를 선택합니다. 대부분 파트너의 경우 “아니요”가 됩니다. [예]를 선택하는 경우 솔루션의 각 버전에 대한 AppSource 패키지를 만들어야 합니다. ‘참고: 여러 **zip** 파일이 있는지 묻는 것이 아닙니다. 여러 solution.zip 파일이 있지만 버전이 하나뿐이라면 “아니요”를 선택해야 합니다. 패키징 도구가 이러한 파일을 자동으로 결합합니다.’

### <a name="crm-package-availability"></a>CRM 패키지 가용성

이 섹션에서는 패키지를 사용할 수 있는 CRM의 패키지 지역을 선택합니다. 어떤 지역에서 어떤 국가를 제공하는지에 대한 자세한 내용은 [http://o365datacentermap.azurewebsites.net/](http://o365datacentermap.azurewebsites.net/) 링크를 참조하세요.

참고: 독일 “자치 및 미국 정부 클라우드” 자치에 배포하려면 인증 중에 특수 권한 및 유효성 검사가 필요합니다.

## <a name="storefront-details"></a>상점 세부 정보

### <a name="offer-summary"></a>제품 요약

제품 가치 제안의 요약입니다. 제품 검색 페이지에 표시됩니다. 최대 100자여야 합니다.

### <a name="offer-description"></a>제품 설명

앱 세부 정보 페이지에 표시될 설명입니다. 허용되는 최댓값은 1300자입니다.

### <a name="industries"></a>산업

앱에 가장 부합되는 산업을 선택합니다. 앱이 여러 산업과 관련되는 경우 이 항목은 비워 둘 수 있습니다.

### <a name="categories"></a>범주

앱과 관련이 있는 범주를 선택합니다. 최대 3개를 선택합니다.

### <a name="app-type"></a>앱 유형

앱이 AppSource에서 사용하도록 설정할 평가판의 유형을 선택합니다. ‘무료’는 앱이 무료임을 의미합니다. ‘평가판’은 고객이 AppSource에서 짧은 기간 동안 앱을 사용해 볼 수 있음을 의미합니다. ‘평가판 요청’은 Dynamics 365 for Customer Engagement 앱에서 지원되지 않습니다. 이 옵션을 선택하지 마세요.

### <a name="help-link-for-your-app"></a>앱 도움말 링크

앱의 도움말 관련 정보가 포함된 페이지의 URL을 입력합니다.

### <a name="supported-countriesregions"></a>지원되는 국가/지역

이 필드의 정보에 따라 제품 평가판을 사용할 수 있는 국가/지역이 결정됩니다.

### <a name="supported-languages"></a>지원되는 언어

앱에서 지원하는 언어를 선택합니다. 앱에서 이 목록에 없는 추가 언어를 지원하는 경우 제품을 계속 게시하고 [appsource@microsoft.com](mailto:appsource@microsoft.com)으로 메일을 보내 알려 주시기 바랍니다.

### <a name="app-version"></a>앱 버전

앱의 버전 번호를 입력합니다.

### <a name="app-release-date"></a>앱 릴리스 날짜

앱의 릴리스 날짜를 입력합니다.

### <a name="products-your-app-works-with-max-3"></a>앱이 작동하는 제품(최대 3개)

앱이 작동하는 특정 제품을 나열합니다. 최대 3개의 제품을 나열할 수 있습니다. 제품을 나열하려면 [새로 만들기] 옆에 있는 더하기 기호를 클릭합니다. 그러면 열린 새 텍스트 필드가 만들어져 앱이 작동하는 제품의 이름을 입력할 수 있습니다.

### <a name="search-keywords-max-3"></a>검색 키워드(최대 3개)

AppSource를 사용하면 고객이 키워드를 기반으로 검색을 수행할 수 있습니다. 고객에게 응용 프로그램을 표시할 키워드 집합을 입력할 수 있습니다.

예를 들어, 응용 프로그램이 “내 메일 서비스”인 경우 메일, 메일링, 메일 서비스가 일부 키워드일 수 있습니다. 사용자가 AppSource 검색 상자에서 앱을 검색하는 데 사용할 수 있는 단어를 선택합니다.

### <a name="hide-key"></a>숨기기 키

이 키는 공용 보기에서 제품을 숨기기 위해 제품 미리 보기 URL과 결합됩니다. 암호가 아닙니다. 여기에는 아무 문자열이나 입력할 수 있습니다.

### <a name="offer-logo-png-format-48x48"></a>제품 로고(.png 형식, 48x48)

앱의 검색 페이지에 표시됩니다. **png 형식만 허용됩니다.** 48PX\*48PX 해상도의 png 이미지를 업로드합니다.

### <a name="offer-logo-png-format-216x216"></a>제품 로고(.png 형식, 216x216)

앱 세부 정보 페이지에 표시됩니다. **png 형식만 허용됩니다.** 216PX\*216PX 해상도의 png 이미지를 업로드합니다.

### <a name="videos"></a>동영상

최대 4개의 동영상을 업로드할 수 있습니다. 업로드하려는 동영상마다 동영상과 연결할 동영상 이름, URL(YouTube 또는 Vimeo만 해당) 및 썸네일을 입력해야 합니다. 썸네일은 1280PX\*720PX의 png 형식이어야 합니다. 새 동영상을 추가하려면 더하기 기호를 클릭합니다. 동영상 썸네일은 앱 세부 정보 페이지에 표시됩니다.

### <a name="documents"></a>문서

PDF 형식으로 최대 3개의 문서를 업로드할 수 있습니다. 업로드하려는 문서마다 문서 이름을 입력하고 문서를 업로드해야 합니다. 문서는 pdf 형식이어야 합니다.

새 문서를 추가하려면 더하기 기호를 클릭합니다.

### <a name="screenshots"></a>스크린샷

이러한 스크린샷은 앱의 AppSource 세부 정보 페이지에 표시됩니다.

### <a name="privacy-policy"></a>개인 정보 취급 방침

앱의 개인 정보 취급 방침 URL을 입력합니다.

### <a name="terms-of-use"></a>사용 약관

앱 사용 약관을 입력합니다. AppSource 고객은 앱을 사용하기 전에 이러한 약관에 동의해야 합니다.

### <a name="support-url"></a>지원 URL

앱의 지원 URL을 입력합니다.

### <a name="lead-destination"></a>잠재 대상 고객

잠재 고객을 저장할 CRM 시스템을 선택합니다. CRM 시스템인 Salesforce, Marketo, Microsoft Dynamics CRM 중 하나가 있는 경우 “Azure Table”을 선택합니다. 여기서 선택하는 CRM 시스템은 AppSource에서 앱을 사용해 보는 최종 사용자(잠재 고객)의 세부 정보를 작성하는 곳입니다. 선택한 CRM 시스템에 따라 다음 필드 집합을 완료하는 방법을 알아보려면 아래에서 해당 URL을 클릭합니다.

* [Azure Table](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>상점 세부 정보

연락처 세부 정보는 파트너와 Microsoft 간의 내부 통신에만 사용됩니다. 참고: 이러한 필드에서 모니터링되는 메일 주소를 사용해야 합니다. Microsoft에서는 이 메일을 사용하여 AppSource에 게시하는 동안 진행 상황을 알립니다. 고객에게는 지원 URL만 표시됩니다.
