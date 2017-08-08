---
title: "Marketplace에서 Azure 관리되는 응용 프로그램 | Microsoft Docs"
description: "Marketplace를 통해 사용할 수 있는 Azure 관리되는 응용 프로그램에 대해 설명합니다."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: bd7880bdbca8cbf1abcab2cbade050876e26aea1
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace에서 Azure 관리되는 응용 프로그램

[관리되는 응용 프로그램 개요](managed-application-overview.md) 문서에 설명된 것처럼 MSP, ISV 및 SI(시스템 통합자)는 Microsoft Azure Marketplace에서 관리되는 응용 프로그램을 통해 자신의 솔루션을 모든 Azure 고객에게 제공할 수 있습니다. 이러한 솔루션으로 고객에 대한 유지 관리 및 서비스 오버헤드가 줄어듭니다. 게시자는 Marketplace를 통해 인프라 및 소프트웨어를 판매하고 서비스 및 운영 지원을 연결할 수 있습니다. 

이 문서에서는 MSP, ISV 또는 SI가 응용 프로그램을 Marketplace에 게시하고 고객에게 광범위하게 제공하는 방법을 설명합니다.  

## <a name="pre-requisites-for-publishing-a-managed-application"></a>관리되는 응용 프로그램을 게시하기 위한 필수 조건

Marketplace에 등록하기 위한 필수 조건

* 기술

    *  Resource Manager 템플릿의 기본 구조 및 구문에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)을 참조하세요.
    *  전체 템플릿 솔루션을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/en-us/documentation/templates/) 또는 [빠른 시작 템플릿 리포지토리](https://github.com/azure/azure-quickstart-templates)를 참조하세요.
    *  마켓플레이스를 통해 응용 프로그램을 배포하는 고객을 위한 인터페이스를 만드는 방법은 [사용자 인터페이스 정의 파일 만들기](managed-application-createuidefinition-overview.md)를 참조하세요.

* 비기술(비즈니스 요구 사항)

    *   회사(또는 해당 자회사)는 Marketplace에서 지원하는 판매처 국가에 위치해야 합니다.
    *   제품은 Marketplace에서 지원하는 청구 모델과 호환되는 방식으로 허가를 받아야 합니다.
    *   상업적이고 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다. 이러한 지원은 평가판, 유료 또는 커뮤니티 지원을 통해 이루어질 수 있습니다.
    *   소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받을 책임이 있습니다.
    *   Azure Marketplace 및 Azure Portal에 등록할 제품에 대한 기준을 충족하는 콘텐츠를 제공해야 합니다.
    *   Azure Marketplace 참가 정책 및 게시자 약관의 조건에 동의해야 합니다.
    *   사용 약관, Microsoft 개인정보처리방침 및 Microsoft Azure Certified 프로그램 계약을 준수한다는 데 동의해야 합니다.

## <a name="how-to-create-a-new-azure-application-offer"></a>새 Azure 응용 프로그램 제품을 만드는 방법

필수 구성 요소를 충족하면 관리되는 응용 프로그램 제품을 작성할 준비가 된 것입니다. 제품 및 SKU를 간략히 살펴보겠습니다.

### <a name="offer"></a>제안

관리되는 응용 프로그램에 대한 제품은 게시자가 제공하는 제품의 클래스에 해당합니다. 새로운 유형의 솔루션/응용 프로그램을 Marketplace에서 사용할 수 있도록 하려는 경우 새 제품으로 설정할 수 있습니다. 제품은 SKU의 컬렉션입니다. 모든 제품은 Marketplace에 고유 엔터티로 나타납니다.

### <a name="sku"></a>SKU

SKU는 제품의 가장 작은 구매 가능 단위입니다. 같은 제품 클래스(제품)에 속하더라도 SKU를 사용하면 지원되는 다양한 기능, 제품이 관리형인지, 비관리형인지 여부 및 지원되는 청구 모델을 구별할 수 있습니다.

SKU는 Marketplace의 부모 제품 아래에 표시됩니다. Azure Portal에는 고유 구입 가능 엔터티로 표시됩니다.

### <a name="set-up-offer"></a>제품 설정

1.  [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2.  왼쪽 탐색 모음에서 **+ 새 제품**을 클릭하고 **Azure 응용 프로그램**을 선택합니다.

    ![새 제품](./media/managed-application-author-marketplace/newOffer.png)

3.  이제 사용자에게 새 제품 **편집기** 뷰가 열리며 작성할 준비가 된 것입니다.

    ![제품 설정](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  작성해야 할 양식은 **편집기** 뷰 내의 왼쪽에서 볼 수 있습니다. 각 양식은 작성해야 할 필드의 집합으로 구성됩니다. 필수 필드는 빨간색 별표(*)로 표시됩니다.

 **관리되는 응용 프로그램을 작성하기 위한 4가지 기본 양식이 있습니다.**

    *   제품 설정
    *   SKU
    *   마켓플레이스
    *   지원

이러한 양식은 다음 섹션에 자세히 설명되어 있습니다.

## <a name="offer-settings-form"></a>제품 설정 양식

제품 설정 양식은 제품 설정을 지정하는 기본 양식입니다. 다양한 필드는 다음과 같습니다.

* 제품 ID - 이 필드는 게시자 프로필 내에서 제품의 고유 식별자입니다. 이 ID는 제품 URL, Resource Manager 템플릿 및 청구 보고서에서 볼 수 있습니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝내면 안 되며 최대 50문자를 포함할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.
* 게시자 ID - 이 필드 드롭다운을 사용하여 이 제품을 게시할 게시자 프로필을 선택할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.
* 이름 - 이 필드는 제품에 대한 표시 이름입니다. Marketplace 및 포털에 표시되는 이름입니다. 최대 50문자를 포함할 수 있습니다. 여기서 제공하는 지침은 제품의 인식 가능한 브랜드 이름을 포함하는 것입니다. 회사 이름을 포함하는 것이 마케팅 전략인 경우를 제외하고 여기에 회사 이름을 포함하지 마세요. 이 제품을 자신의 고유 웹 사이트에 마케팅하는 경우 이 이름은 웹 사이트에 표시되는 이름과 같아야 합니다.

**저장**을 클릭하여 진행 상황을 저장합니다. 다음 단계는 제품에 대한 SKU를 추가하는 것입니다.

## <a name="skus-form"></a>SKU 양식

**SKU** 양식을 선택합니다. 여기에서 **새 SKU**에 대한 옵션을 볼 수 있습니다. 이 옵션을 선택하면 **SKU ID**를 입력할 수 있습니다.

![새 SKU 선택](./media/managed-application-author-marketplace/newOffer_skus.png)

**SKU ID**는 제품 내의 SKU에 대한 고유 식별자입니다. 이 ID는 제품 URL, Resource Manager 템플릿 및 청구 보고서에서 볼 수 있습니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝내면 안 되며 최대 50문자를 포함할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다. 제품 내에 여러 SKU를 포함할 수 있습니다. 게시하려는 각 이미지에 대해 SKU가 필요합니다.

**새 SKU**를 선택한 후 다음 양식을 입력해야 합니다.

![새 SKU 제공](./media/managed-application-author-marketplace/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>SKU 세부 정보 섹션을 작성하는 방법

* 제목 - 이 SKU의 제목을 입력합니다. 갤러리에서 이 항목에 대해 표시되는 내용입니다.
* 요약 - 이 sku에 대한 간단한 요약을 제공합니다. 이 텍스트는 제목 바로 아래 표시됩니다.
* 설명 - SKU에 대한 자세한 설명을 제공합니다.
* SKU 형식 - 허용된 값은 **관리되는 응용 프로그램** 및 **솔루션 템플릿**입니다. 이 경우 **관리되는 응용 프로그램**을 선택합니다.

### <a name="how-to-fill-package-details-section"></a>패키지 세부 정보 섹션을 작성하는 방법

패키지 섹션에서는 다음 필드를 작성해야 합니다.

![패키지](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**현재 버전** - 업로드하는 패키지에 대한 버전을 제공합니다. `{number}.{number}.{number}{number}` 형식이어야 합니다.

**패키지 파일** - 이 패키지에는 .zip 파일로 압축되는 다음 파일이 포함됩니다.

* **applianceMainTemplate.json** - 솔루션/응용 프로그램을 배포하는 데 사용되는 배포 템플릿 파일입니다. [첫 번째 Azure Resource Manager 템플릿 만들기](resource-manager-create-first-template.md)에서 배포 템플릿 파일을 작성하는 방법을 자세히 확인할 수 있습니다.
* **appliancecreateUIDefinition.json** - 이 파일은 Azure Portal에서 이 솔루션/응용 프로그램을 프로비전하기 위한 사용자 인터페이스를 생성하는 데 사용됩니다. 자세한 내용은 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
* **mainTemplate.json** - Microsoft.Solution/appliances 리소스만 포함하는 템플릿 파일입니다. 알아 두어야 할 이 리소스의 주요 속성은 다음과 같습니다.

mainTemplate에는 다음과 같은 속성이 포함됩니다.

*  kind - Marketplace에서 관리되는 응용 프로그램으로 **Marketplace** 사용
*  ManagedResourceGroupId - applianceMainTemplate.json에 정의된 모든 리소스가 배포되는 고객 구독에 있는 리소스 그룹입니다.
*  PublisherPackageId - 패키지를 고유하게 식별하는 문자열입니다. `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` 형식의 값을 제공합니다.
  publisherId 및 OfferId는 게시 포털에서 가져올 수 있습니다.

  ![제품 ID](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
  다음 이미지에 나와 있는 것처럼 SKU ID를 얻을 수 있습니다.

  ![SKU ID](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
  다음 이미지에 나와 있는 것처럼 패키지 버전을 얻을 수 있습니다.

  ![패키지 버전](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  따라서 위의 예제를 사용할 경우 **PublisherPackageId**는 `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`입니다.

  샘플 mainTemplate.json:

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

이 패키지는 이 응용 프로그램을 성공적으로 프로비전하는 데 필요한 기타 중첩된 템플릿 또는 스크립트를 포함해야 합니다. mainTemplate.json, applianceMainTemplate.json 및 applianceCreateUIDefinition.json은 루트 폴더에 있어야 합니다.

**권한 부여** - 이 속성은 고객 구독에서 리소스에 대해 어떤 사용자가 어떤 수준의 액세스 권한을 갖는지 정의합니다. 따라서 게시자는 고객을 대신하여 응용 프로그램을 관리할 수 있습니다.

* PrincipalId - 이 속성은 고객 구독에서 리소스에 대한 특정 권한(역할 정의에 설명됨)이 부여된 사용자, 사용자 그룹 또는 응용 프로그램의 Azure Active Directory 식별자입니다.
* 역할 정의 - 이 속성은 Azure AD가 제공한 모든 기본 제공 RBAC 역할 목록입니다. 가장 적합한 역할을 선택하고 고객을 대신하여 리소스를 관리할 수 있습니다.

여러 권한 부여를 추가할 수 있습니다. 그러나 Active Directory 사용자 그룹을 만들고 **PrincipalId**에 해당 ID를 지정하는 것이 좋습니다. 이렇게 하면 사용자 그룹에 더 많은 사용자를 추가할 수 있으며 SKU를 업데이트하지 않아도 됩니다.

RBAC에 대한 자세한 내용은 [Azure Portal에서 역할 기반 액세스 제어 시작](../active-directory/role-based-access-control-what-is.md)을 참조하세요.

## <a name="marketplace-form"></a>Marketplace 양식

Marketplace 양식에서 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에 표시되는 필드를 요청합니다.

### <a name="preview-subscription-ids"></a>미리 보기 구독 ID

제품이 게시된 후 해당 제품에 액세스 권한을 갖고자 하는 Azure 구독 ID 목록을 여기에 입력합니다. 이러한 허용 목록 구독을 사용하여 제품을 라이브 상태로 만들기 전에 미리 본 제품을 테스트할 수 있습니다. 파트너 포털을 사용하여 구독을 100개까지 허용 목록에 등록할 수 있습니다.

### <a name="suggested-categories"></a>권장 범주

제공된 목록에서 제품이 가장 잘 연결될 수 있는 범주를 5개까지 선택합니다. 선택한 범주는 고객의 제품을 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [포털](https://portal.azure.com/)에서 사용할 수 있는 제품 범주에 매핑하는 데 사용됩니다.

#### <a name="azure-marketplace"></a>Azure 마켓플레이스

관리되는 응용 프로그램 요약에는 다음 필드가 표시됩니다.

![마켓플레이스 요약](./media/managed-application-author-marketplace/publishvm10.png)

관리되는 응용 프로그램 개요에는 다음 필드가 표시됩니다.

![마켓플레이스 개요](./media/managed-application-author-marketplace/publishvm11.png)

관리되는 응용 프로그램의 계획 및 가격 책정에는 다음 필드가 표시됩니다.

![마켓플레이스 계획](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure 포털

관리되는 응용 프로그램 요약에는 다음 필드가 표시됩니다.

![포털 요약](./media/managed-application-author-marketplace/publishvm12.png)

관리되는 응용 프로그램 개요에는 다음 필드가 표시됩니다.

![포털 개요](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>로고 지침

클라우드 파트너 포털에 업로드되는 모든 로고에 다음 지침이 적용됩니다.

*   Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.
*   포털의 테마 색은 흰색과 검은색입니다. 따라서 로고의 배경색으로 이러한 색을 사용하지 않도록 합니다. 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다. **투명한 배경을 사용하는 경우 로고/텍스트가 흰색, 검은색 또는 파란색이 아닌지 확인합니다.**
*   로고의 배경에 그라데이션 효과는 사용하지 않습니다.
*   로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 않습니다. 로고의 모양과 느낌은 평면적이어야 하며 그라데이션은 사용하지 않습니다.
*   로고가 늘어나지 않았는지 확인합니다.

#### <a name="hero-logo"></a>대표 로고

대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. 그러나 일단 업로드되면 대표 로고를 삭제할 수 없습니다. 이때 파트너는 대표 아이콘에 대한 Marketplace 지침을 따라야 합니다.

##### <a name="guidelines-for-the-hero-logo-icon"></a>대표 로고 아이콘에 대한 지침

*   게시자 표시 이름, 계획 제목 및 제품의 자세한 요약은 흰색 글꼴 색으로 표시됩니다. 따라서 대표 아이콘의 백그라운드는 밝은 색으로 두지 않아야 합니다. 대표 아이콘에는 검은색, 흰색 및 투명한 배경이 허용되지 않습니다.
*   제품이 나열되면 대표 로고 안에 게시자 표시 이름, 계획 제목, 자세한 제품 요약과 만들기 버튼이 프로그래밍 방식으로 포함됩니다. 따라서 대표 로고를 디자인할 때 이런 내용을 입력하지 않아야 합니다. 텍스트(즉, 게시자 표시 이름, 계획 제목, 제품의 자세한 요약)는 프로그래밍 방식으로 포함되므로 오른쪽에 빈 공간을 남겨둡니다. 텍스트의 오른쪽 빈 공간은 415x100이어야 합니다(왼쪽에서 370px만큼 오프셋됨).

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>지원 양식

다음에 작성할 양식은 지원 양식입니다. 이 양식은 엔지니어링 연락처 정보 및 고객 지원 연락처 정보 등 고객 회사의 지원 연락처를 요구합니다.

## <a name="how-to-publish-an-offer"></a>제품 게시 방법

모든 섹션을 완료한 후 **게시**를 선택하여 고객에게 제품을 제공하는 과정을 시작합니다.

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [Azure Managed Application 개요](managed-application-overview.md)를 참조하세요.
* Marketplace의 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램 사용](managed-application-consume-marketplace.md)을 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 사용](managed-application-consumption.md)을 참조하세요.

