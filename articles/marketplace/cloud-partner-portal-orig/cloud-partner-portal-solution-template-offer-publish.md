---
title: Azure 솔루션 템플릿 게시 | Microsoft Docs
description: Azure Marketplace에 솔루션 템플릿을 게시합니다.
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
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 333eebfa1bae919c43164572c63f2de4f7251fe0
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261620"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Azure Marketplace에 솔루션 템플릿 게시

이 문서에서는 Azure Marketplace에 솔루션 템플릿을 게시하기 위한 단계를 제공합니다.

## <a name="prerequisites"></a>필수 조건

Azure Marketplace에 솔루션 템플릿을 나열하는 데 적용되는 기술 및 비기술 필수 조건은 다음과 같습니다

### <a name="technical"></a>기술

- [Azure Resource Manager 템플릿 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Azure 빠른 시작 템플릿
    - [Azure 빠른 시작 템플릿 설명서](https://azure.microsoft.com/documentation/templates/)
    - [GitHub의 Azure 빠른 시작 설명서](https://github.com/azure/azure-quickstart-templates)
 - [Azure Portal 사용자 인터페이스 파일 만들기](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)
 - [고객 사용량 특성](./../azure-partner-customer-usage-attribution.md)을 사용하도록 설정하여 Azure에서 소프트웨어의 고객 배포에 대한 Azure 사용량을 추적할 수 있습니다.

### <a name="non-technical-business-requirements"></a>비기술(비즈니스 요구 사항)

- 회사(또는 해당 자회사)는 Azure Marketplace에서 지원하는 판매처 국가에 위치해야 합니다.
- 제품은 Azure Marketplace에서 지원하는 청구 모델과 호환되는 방식으로 허가를 받아야 합니다.
- 무료, 유료 또는 커뮤니티 지원을 통해 상업적으로 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다.
- 소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받을 책임이 있습니다.
- Azure Marketplace 및 Azure Portal에 나열할 제품에 대한 조건을 충족하는 콘텐츠를 제공합니다.
- Azure Marketplace 참가 정책 및 게시자 약관의 조건에 동의합니다.
- 사용 약관, Microsoft 개인정보처리방침 및 Microsoft Azure Certified 프로그램 계약을 준수한다는 데 동의합니다.

## <a name="before-you-begin"></a>시작하기 전에

모든 필수 조건이 충족되면 솔루션 템플릿 제안 작성을 시작할 수 있습니다. 시작하기 전에 검토해야 하는 제안 및 SKU 정보는 다음과 같습니다.

**제안**

Azure 애플리케이션 제안은 게시자가 제공하는 제품의 유형에 해당합니다. Azure Marketplace에서 사용할 수 있는 새 유형의 솔루션/응용 프로그램이 있으면 새 제안이 가장 좋은 방법입니다. 제품은 SKU의 컬렉션입니다. 모든 제품은 Azure Marketplace에 고유 엔터티로 나타납니다.

**SKU**

SKU는 제품의 가장 작은 구매 가능 단위입니다. SKU를 사용하면 동일한 제품 클래스(제안) 내에서 지원되는 다양한 기능을 구분할 수 있습니다. 예를 들어 제안은 관리되거나 관리되지 않으며 다른 청구 모델이 지원됩니다.

여러 SKU를 추가하는 시나리오는 다음과 같습니다.
- BYOL(사용자 라이선스 필요) 또는 PAYG(종량제)와 같은 다양한 청구 모델을 지원하려고 합니다.
- 각 SKU에서 서로 다른 기능 집합을 지원하며, 각 기능 집합의 가격은 서로 다르게 책정됩니다.

SKU는 Azure Marketplace의 부모 제안 아래에 표시되고 Azure Portal에는 자체의 구입 가능한 엔터티로 표시됩니다.

## <a name="to-create-a-new-offer"></a>새 제안을 만들려면

1.  [클라우드 파트너 포털](http://cloudpartner.azure.com/)에 로그인합니다.

2.  왼쪽 탐색 모음에서 **+ 새 제안**을 클릭한 다음, **Azure 애플리케이션**을 선택합니다.

    ![새 제안 만들기](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  **새 제안** 아래에서 **편집기**를 선택합니다.

    ![새 제안 편집기](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  **편집기** 아래에서 다음 보기의 정보를 제공합니다.
    - 제품 설정
    - SKU
    - Marketplace
    - 지원

각 보기에는 사용자가 입력할 필드 집합이 포함됩니다. 필수 필드는 빨간색 별표(\*)로 표시됩니다.

## <a name="to-configure-offer-settings"></a>제안 설정 구성

1. 제안 설정에서 다음 **제안 ID** 필드를 구성합니다.

    **제안 ID**

     게시자 프로필 내에서 고유한 제안 식별자입니다. 이 ID는 제품 URL, Azure Resource Manager 템플릿 및 청구 보고서에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)만 사용할 수 있습니다. ID는 대시로 끝날 수 없으며, 최대 50자까지 포함할 수 있습니다. 
    >[!Note]
    >제안이 라이브 상태가 되면 이 필드가 잠깁니다.

    **게시자 ID**

    게시자 프로필에 대한 드롭다운 목록입니다. 제안을 게시하려는 프로필을 선택합니다. 
    >[!Note]
    >제품이 라이브 상태가 되면 이 필드가 잠깁니다.

    **Name**

    제품의 표시 이름입니다. 이 이름은 Azure Marketplace 및 Azure Portal에 표시됩니다. 최대 50문자를 포함할 수 있습니다. 제안 이름에 대해 다음 지침을 사용합니다.
    -  제품의 인식 가능한 브랜드 이름을 포함합니다. 
    - 마케팅 방식의 제안인 경우에만 여기에 회사 이름을 포함합니다.
    - 자신의 웹 사이트에서 이 제안을 마케팅하는 경우 이름이 웹 사이트의 이름과 동일한지 확인합니다.

2. **저장**을 선택하여 [제안 설정]을 완료합니다.
제안 성장

## <a name="to-create-skus"></a>SKU를 만들려면
------------------

1. **SKU**를 선택합니다. 

    ![새 SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    SKU ID는 제안 내의 SKU에 대한 고유 식별자입니다. 이 ID는 제품 URL, Resource Manager 템플릿 및 청구 보고서에서 볼 수 있습니다. SKU ID:
    - 최대 50자만 포함할 수 있습니다.
    - 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다.
    - ID는 대시로 끝날 수 없습니다.

    >[!Note]
    >추가한 SKU는 SKU 보기의 SKU 목록에 표시됩니다. SKU 세부 정보을 보려면 SKU 이름을 선택합니다. 

2. **새 SKU**를 선택하여 다음 화면 캡처에 표시된 정보를 제공합니다. 

    ![SKU 세부 정보](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>SKU 설정

다음 SKU 설정을 제공합니다.

- **제목** - SKU의 제목입니다. 이 제목은 이 항목의 갤러리에 나타납니다.
- **요약** - SKU에 대한 짧은 요약 설명입니다. 최대 길이는 100자입니다.
- **설명** - SKU에 대한 자세한 설명입니다.
- **SKU 유형** - “관리되는 응용 프로그램(미리 보기)” 및 “솔루션 템플릿” 값을 포함하는 드롭다운 목록입니다. 이 시나리오에서는 **솔루션 템플릿**을 선택합니다.
- **클라우드 가용성** - SKU의 위치입니다. 기본값은 **공용 Azure**입니다.

### <a name="package-details"></a>패키지 세부 정보

SKU 설정을 마친 후 다음 패키지 세부 정보를 제공합니다.

![패키지 세부 정보](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **현재 버전** - 업로드할 패키지의 버전입니다. 버전 태그는 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다.
- **패키지 파일** - 이 패키지는 .zip 파일에 저장된 다음 파일을 포함합니다.
    -   MainTemplate.json - 솔루션/응용 프로그램을 배포하고 솔루션에 대해 정의된 리소스를 만드는 데 사용되는 배포 템플릿 파일입니다. 자세한 내용은 [배포 템플릿 파일을 작성하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)을 참조하세요.
    -   createUIDefinition.json - 이 파일은 Azure Portal에서 이 솔루션/애플리케이션을 프로비전하기 위한 사용자 인터페이스를 생성하는 데 사용됩니다. 자세한 내용은 [관리되는 애플리케이션에 대한 Azure Portal 사용자 인터페이스 만들기](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)를 참조하세요.

    >[!IMPORTANT]
    >이 패키지는 이 애플리케이션을 프로비전하는 데 필요한 기타 중첩된 템플릿 또는 스크립트를 포함해야 합니다. mainTemplate.json 및 createUiDefinition.json은 루트 폴더에 있어야 합니다.

## <a name="to-configure-the-marketplace"></a>Marketplace를 구성하려면

Marketplace 보기를 사용하여 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에서 제품에 대해 표시되는 필드를 구성합니다.

### <a name="preview-subscription-ids"></a>미리 보기 구독 ID

제안이 게시되면 해당 제안에 액세스할 수 있는 Azure 구독 ID의 목록입니다. 이러한 허용 목록 구독을 사용하여 제안을 라이브 상태로 만들기 전에 미리 본 제안을 테스트할 수 있습니다. 파트너 포털을 통해 최대 100개의 구독을 허용 목록에 추가할 수 있습니다.

### <a name="suggested-categories"></a>권장 범주

제공된 목록에서 제품이 가장 잘 연결될 수 있는 범주를 5개까지 선택합니다. 선택한 범주는 고객의 제품을 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에서 사용할 수 있는 제품 범주에 매핑하는 데 사용됩니다.

다음 예제에서는 Azure Marketplace 및 Azure Portal에서 Marketplace 정보를 보여줍니다.

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>로고 지침

Cloud 파트너 포털에 업로드되는 로고에 대해 다음 지침을 따릅니다.

-   Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.

-   Azure 포털의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로는 이러한 색을 사용하지 마세요. Azure Portal에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다.

    >[!Note] 
    >투명한 배경을 사용하는 경우 로고/텍스트가 흰색, 검은색 또는 파란색이 아닌지 확인합니다.

-   로고의 배경에 그라데이션 효과를 사용하지 마십시오.

-   텍스트를 로고 위에 배치하지 않습니다. 이 지침에는 회사 또는 브랜드 이름이 포함됩니다. 로고의 모양과 느낌은 *평면적*이어야 하며 그라데이션은 사용하지 않습니다.

-   로고는 늘릴 수 없습니다.

#### <a name="hero-logo"></a>대표 로고

대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. 그러나 로그가 업로드되면 삭제할 수 없습니다. 파트너는 대표 아이콘에 대한 Azure Marketplace 지침을 따라야 합니다.

#### <a name="guidelines-for-the-hero-logo-icon"></a>대표 로고 아이콘에 대한 지침

-   게시자 표시 이름, 계획 제목 및 제품의 긴 요약은 흰색 글꼴로 표시됩니다. 밝은색 배경은 사용하지 않습니다. 대표 아이콘에는 검은색, 흰색 및 투명한 배경이 허용되지 않습니다.

-   제안이 나열되면 게시자 표시 이름, 계획 제목, 자세한 제안 요약 및 [만들기] 단추가 대표 로고 내에 프로그래밍 방식으로 포함됩니다. 대표 로고를 디자인할 때 텍스트는 입력하지 않습니다. 로고의 오른쪽에 빈 공간을 남겨 둡니다. 이 공간은 415 x 100픽셀이어야 하며 왼쪽에서 370픽셀만큼 오프셋됩니다.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>지원을 구성하려면

지원 보기를 사용하여 다음 정보를 제공합니다.

- 엔지니어링과 같은 회사의 기술 지원 담당자
- 고객 지원 담당자

## <a name="to-publish-the-offer"></a>제안을 게시하려면

마지막 단계는 제안을 게시하는 것입니다. **게시**를 선택합니다.
