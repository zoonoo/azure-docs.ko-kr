---
title: Azure Marketplace에 Azure 관리되는 애플리케이션 게시
description: Azure Marketplace에 Azure 관리되는 애플리케이션 게시
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: bc044c8b59c939163336ecab01546fc26a7a2643
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808025"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Azure Marketplace에 Azure 관리되는 애플리케이션 게시 
========================================================

이 문서에서는 Azure Marketplace에 관리되는 애플리케이션 제품을 게시하는 과정에서 수행하는 여러 단계에 대해 설명합니다.

<a name="pre-requisites-for-publishing-a-managed-application"></a>관리되는 애플리케이션을 게시하기 위한 필수 구성 요소 
---------------------------------------------------

Azure Marketplace에 등록하기 위한 필수 조건

1.  기술

    -   [Azure 리소스 관리자 템플릿 작성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Azure 빠른 시작 템플릿

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   UI 정의 만들기

        -   [사용자 인터페이스 정의 파일 만들기](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  비기술(비즈니스 요구 사항)

    -   회사(또는 해당 자회사)는 Azure Marketplace에서 지원하는 판매처 국가에 위치해야 합니다.

    -   제품은 Azure Marketplace에서 지원하는 청구 모델과 호환되는 방식으로 허가를 받아야 합니다.

    -   무료, 유료 또는 커뮤니티 지원을 통해 고객에게 기술 지원을 제공해야 합니다.

    -   소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받아야 합니다.

    -   Azure Marketplace 및 Azure 관리 포털에 등록할 제품에 대한 기준을 충족하는 콘텐츠를 제공해야 합니다.

    -   Azure Marketplace 참가 정책 및 게시자 약관의 조건에 동의해야 합니다.

    -   사용 약관, Microsoft 개인정보처리방침 및 Microsoft Azure Certified 프로그램 계약을 준수한다는 데 동의해야 합니다.

<a name="how-to-create-a-new-azure-application-offer"></a>새 Azure 애플리케이션 제품을 만드는 방법 
-------------------------------------------

모든 필수 구성 요소를 충족하면 관리되는 애플리케이션 제품을 작성할 준비가 된 것입니다. 시작하기 전에 제품 및 SKU를 간략히 요약하겠습니다.

**제안**

Azure 애플리케이션 제품은 게시자가 제공하는 제품 클래스에 해당합니다. Azure Marketplace에 게시할 새 솔루션/응용 프로그램이 있다면 새 제품을 만들어야 합니다. 제품은 SKU의 컬렉션입니다. 모든 제품은 Azure Marketplace에 고유 엔터티로 나타납니다.

**SKU**

SKU는 최소 구매 가능 단위의 제품입니다. 같은 제품 클래스(제품)에 속하더라도 SKU를 사용하면 지원되는 다양한 기능, 제품의 관리 방식(관리형/비관리형) 및 청구 모델을 구별할 수 있습니다.

사용자 라이선스 필요(BYOL), 종량제(PAYG) 등 다양한 청구 모델을 지원하려는 경우 여러 SKU를 추가합니다. 

각 SKU가 서로 다른 기능 집합을 지원하며 가격이 각기 다르게 책정되는 경우 여러 SKU를 추가합니다.

SKU는 Azure Marketplace의 상위 제품 아래에 표시되지만 azure.com에는 고유 구매 가능 엔터티로 표시됩니다.

1.  [클라우드 파트너 포털](http://cloudpartner.azure.com)에 로그인합니다.

2.  왼쪽 탐색 모음에서 \"+ 새 제품\"을 클릭하고 \"Azure 애플리케이션\"을 선택합니다.

    ![새 제품](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  이제 새 제품 \"편집기\" 보기가 열립니다. 그러면 작성을 시작할 수 있습니다.

4.  작성해야 할 \"양식\"은 \"편집기\" 보기 내의 왼쪽에 표시됩니다. 각 \"양식\"은 작성해야 할 필드 집합으로 구성됩니다. 필수 필드는 빨간색 별표(\*)로 표시됩니다.

    > 관리되는 애플리케이션을 작성하기 위한 4가지 기본 양식은 다음과 같습니다.

    -   제품 설정

    -   SKU

    -   Marketplace

    -   지원

<a name="how-to-fill-out-the-offer-settings-form"></a>제품 설정 양식을 작성하는 방법 
---------------------------------------

제품 설정 양식은 제품 설정을 지정하는 기본 양식입니다.
아래에 서로 다른 필드를 설명합니다.

**제품 ID**

게시자 프로필에 있는 제품의 고유 식별자입니다.
이 ID는 제품 URL, Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)만 사용할 수 있습니다. ID는 대시로 끝나거나 50자를 초과할 수 없습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.

**게시자 ID**

이 드롭다운 목록을 사용하여 이 제품을 게시할 게시자 프로필을 선택할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.

**Name**

제품의 표시 이름입니다. 이 이름은 Azure Marketplace 및 Azure Portal에 표시됩니다. 최대 50문자를 포함할 수 있습니다. 여기서 제공하는 지침은 제품의 인식 가능한 브랜드 이름을 포함하는 것입니다. 회사 이름을 포함하는 것이 마케팅 전략인 경우를 제외하고 여기에 회사 이름을 포함하지 마세요. 이 제품을 자신의 고유 웹 사이트에 마케팅하는 경우 이 이름은 웹 사이트에 표시되는 이름과 같아야 합니다.

\"저장\"을 클릭하여 진행 상황을 저장합니다. 다음 단계는 제품에 대한 SKU를 추가하는 것이 될 수 있습니다.

<a name="how-to-create-skus"></a>SKU를 만드는 방법 
------------------

\"SKUs\" 양식을 클릭합니다. 이 양식에는 \"SKU 추가\" 옵션이 표시되며 이 옵션을 클릭하면 \"SKU ID\"를 입력할 수 있습니다.

![새 제품 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"SKU ID\"는 제품 내의 SKU에 대한 고유 식별자입니다. 이 ID는 제품 URL, ARM 템플릿 및 청구 보고서에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다.
ID는 대시로 끝나면 안 되며 50자까지 입력할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다. 제품 내에 여러 SKU를 포함할 수 있습니다. 게시하려는 각 이미지에 대해 SKU가 필요합니다.

추가한 SKU는 \"SKUs\" 양식 내의 SKU 목록에 표시됩니다. SKU 이름을 클릭하여 특정 SKU에 대한 정보를 가져옵니다. 다음은 몇몇 필드에 대한 일부 정보입니다.

\"새 SKU\"를 클릭한 후에는 다음 양식을 작성해야 합니다.

![새 제품 - 새 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>SKU 세부 정보 섹션을 작성하는 방법 

**제목** - 이 SKU의 제목을 입력합니다. 갤러리에서 이 항목에 표시되는 제목입니다.

**요약** - 이 SKU에 대한 간단한 요약을 입력합니다. 이 텍스트는 제목 바로 아래 표시됩니다.

**설명** - SKU에 대한 자세한 설명을 입력합니다.

**SKU 형식** - 허용되는 값은 \"관리되는 응용 프로그램\" 및 \"솔루션 템플릿\"입니다. 여기서는 \"관리되는 애플리케이션\"을 선택합니다.

### <a name="how-to-fill-package-details-section"></a>패키지 세부 정보 섹션을 작성하는 방법 

패키지 섹션에서는 다음 필드를 작성해야 합니다.

![새 제품 - 새 SKU 패키지](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**현재 버전** - 업로드하는 패키지의 버전을 입력합니다.
다음과 같은 형식이어야 합니다.

**패키지 파일** - 패키지에는 .zip 파일로 압축되는 다음 파일이 포함됩니다.

MainTemplate.json - 솔루션/응용 프로그램을 배포하고 해당 솔루션/응용 프로그램 내에 정의된 리소스를 만드는 데 사용되는 배포 템플릿 파일입니다. 배포 템플릿 파일을 작성하는 방법의 세부 정보는 다음 페이지에서 확인할 수 있습니다. <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json - azure.com의 포털에서 이 솔루션/응용 프로그램을 프로비전하기 위한 사용자 인터페이스를 생성하는 데 사용되는 파일입니다. 이 파일을 만드는 방법의 세부 정보는 다음 페이지에서 확인할 수 있습니다. <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json - Microsoft.Solution/appliances 리소스만 포함하는 템플릿 파일입니다. 알아 두어야 할 이 리소스의 주요 속성은 다음과 같습니다.

-   \"kind\" - Marketplace 관리되는 응용 프로그램 시나리오의 경우 값은 \"Marketplace\"여야 합니다.
-   \"ManagedResourceGroupId\": applianceMainTemplate.json에 정의된 모든 리소스가 배포되는 고객 구독에 있는 리소스 그룹입니다.
-   \"PublisherPackageId\": 패키지를 고유하게 식별하는 문자열입니다. 

값은 \[publisherId\].\[OfferId\]-preview\[SKUID\].\[PackageVersion\]과 같이 연결하는 방식으로 생성해야 합니다.
이러한 각 값은 아래에 나와 있는 방법으로 가져올 수 있습니다.

이 패키지는 이 애플리케이션을 올바르게 프로비전하는 데 필요한 기타 중첩된 템플릿 또는 스크립트를 포함해야 합니다. mainTemplate.json, applianceMainTemplate.json 및 applianceCreateUIDefinition.json은 루트 폴더에 있어야 합니다.

**Authorizations** - 이 속성은 고객 구독의 리소스 액세스 권한이 제공되는 사용자 및 리소스 액세스 권한 수준을 정의합니다. 따라서 게시자는 고객을 대신하여 애플리케이션을 관리할 수 있습니다.

-   PrincipalId - 고객 구독에서 리소스에 대한 특정 권한(역할 정의에 설명됨)을 부여할 사용자, 사용자 그룹 또는 애플리케이션의 Azure Active Directory 식별자입니다.

-   역할 정의 - Azure AD가 제공하는 모든 기본 제공 RBAC 역할 목록입니다. 가장 적합한 역할을 선택하여 고객 대신 리소스를 관리할 수 있습니다.

여러 권한 부여를 추가할 수 있습니다. 그러나 Active Directory 사용자 그룹을 만들고 \"PrincipalId\."에 해당 ID를 지정하는 것이 좋습니다.  이렇게 하면 사용자 그룹에 더 많은 사용자를 추가할 수 있으며 SKU를 업데이트하지 않아도 됩니다.

RBAC에 대한 자세한 내용은 다음 페이지에서 확인할 수 있습니다. <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Marketplace 양식
----------------

Azure 애플리케이션 제품 내의 Marketplace 양식에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에 표시할 정보를 필드에 입력해야 합니다. 다음은 몇몇 필드에 대한 일부 정보입니다.

#### <a name="preview-subscription-ids"></a>미리 보기 구독 ID

게시한 제품의 액세스 권한을 제공할 Azure 구독 ID의 목록을 여기에 입력합니다. 이러한 허용 목록 구독을 사용하여 제안을 라이브 상태로 만들기 전에 미리 본 제안을 시험할 수 있습니다. 파트너 포털을 통해 최대 100개의 구독을 허용 목록에 추가할 수 있습니다.

#### <a name="suggested-categories"></a>권장 범주

제공된 목록에서 제품이 가장 잘 연결될 수 있는 범주를 5개까지 선택합니다. 선택한 범주는 고객의 제품을 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에서 사용할 수 있는 제품 범주에 매핑하는 데 사용됩니다.

다음은 이 양식에 제공하는 데이터가 표시되는 몇몇 장소입니다.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>azure.com의 포털

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>로고 지침

클라우드 파트너 포털에 업로드되는 모든 로고는 다음 지침을 따라야 합니다.

-   Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.

-   azure.com의 포털 테마 색은 흰색과 검은색입니다. 로고의 배경색으로는 이러한 색을 사용하지 마세요. azure.com의 포털에서 로고가 돋보이도록 하는 색을 사용합니다.
    간단한 기본 색을 사용하는 것이 좋습니다. **투명한 배경을 사용하는 경우 로고 및 텍스트가 흰색, 검은색 또는 파란색이 아닌지 확인합니다.**

-   로고의 배경에 그라데이션 효과는 사용하지 않습니다.

-   로고에 회사 또는 브랜드 이름을 포함한 텍스트를 추가하지 않습니다.
    로고의 모양과 느낌은 \'평면적\'이어야 하며 그라데이션은 사용하지 않습니다.

-   로고는 늘이지 않습니다.

##### <a name="hero-logo"></a>대표 로고

대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. 그러나 일단 업로드되면 대표 로고를 삭제할 수 없습니다. 이때 파트너는 대표 아이콘에 대한 Azure Marketplace 지침을 따라야 합니다.

###### <a name="guidelines-for-the-hero-logo-icon"></a>대표 로고 아이콘에 대한 지침

-   게시자 표시 이름, 계획 제목 및 제안의 자세한 요약은 흰색 글꼴 색으로 표시됩니다. 그러므로 대표 아이콘의 배경에는 밝은 색을 사용하지 마세요. 대표 아이콘에는 검은색, 흰색 및 투명한 배경이 허용되지 않습니다.

-   제품이 목록에 추가되면 게시자 표시 이름, 계획 제목, 자세한 제품 요약 및 만들기 단추가 대표 로고 내에 프로그래밍 방식으로 포함됩니다. 따라서 대표 로고를 디자인할 때는 텍스트를 입력할 필요가 없습니다. 게시자 표시 이름, 계획 제목, 제안의 자세한 요약 등이 표시되도록 오른쪽에 빈 공간을 남겨 두면 됩니다. 이러한 내용은 프로그래밍 방식으로 포함됩니다.
    오른쪽의 텍스트용 빈 공간 크기는 415x100이어야 하며 왼쪽에서 370px만큼 오프셋되어야 합니다.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>지원 양식
------------

다음에 작성할 양식은 지원 양식입니다. 이 양식에는 회사의 지원 연락처를 입력해야 합니다.  엔지니어링 연락처, 고객 지원 연락처 정보 등을 예로 들 수 있습니다.

<a name="how-to-publish-an-offer"></a>제품 게시 방법
-----------------------

이제 제품 초안을 작성했으므로 다음 단계는 Azure Marketplace에 제품을 게시하는 것입니다. \"게시\" 단추를 클릭하여 제품을 라이브로 전환하는 프로세스를 시작합니다.
