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
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Marketplace에서 Azure 관리되는 응용 프로그램

 MSP, ISV 및 SI(시스템 통합자)는 Azure 관리되는 응용 프로그램을 사용하여 모든 Azure Marketplace 고객에게 솔루션을 제공할 수 있습니다. 이러한 솔루션으로 고객에 대한 유지 관리 및 서비스 오버헤드가 줄어듭니다. 게시자는 Marketplace를 통해 인프라 및 소프트웨어를 판매할 수 있습니다. 서비스 및 작업 지원을 관리되는 응용 프로그램에 연결할 수 있습니다. 자세한 내용은 [관리되는 응용 프로그램 개요](managed-application-overview.md)를 참조하세요.

이 문서에서는 MSP, ISV 또는 SI가 응용 프로그램을 Marketplace에 게시하고 고객에게 광범위하게 제공하는 방법을 설명합니다.

## <a name="prerequisites-for-publishing-a-managed-application"></a>관리되는 응용 프로그램을 게시하기 위한 필수 조건

Marketplace에 등록하기 위한 필수 조건:

* 기술

    *  Azure Resource Manager 템플릿의 기본 구조 및 구문에 대한 자세한 내용은 [Azure Resource Manager 템플릿](resource-group-authoring-templates.md)을 참조하세요.
    *  전체 템플릿 솔루션을 보려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/en-us/documentation/templates/) 또는 [빠른 시작 템플릿 리포지토리](https://github.com/azure/azure-quickstart-templates)를 참조하세요.
    *  Marketplace를 통해 응용 프로그램을 배포하는 고객을 위한 인터페이스를 만드는 방법에 대한 자세한 정보는 [사용자 인터페이스 정의 파일 만들기](managed-application-createuidefinition-overview.md)를 참조하세요.

* 비기술(비즈니스 요구 사항)

    *   회사 또는 해당 자회사는 Marketplace에서 지원하는 판매처 국가에 위치해야 합니다.
    *   제품은 Marketplace에서 지원하는 청구 모델과 호환되는 방식으로 허가를 받아야 합니다.
    *   상업적이고 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다. 이러한 지원은 평가판, 유료 또는 커뮤니티 지원을 통해 이루어질 수 있습니다.
    *   소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받을 책임이 있습니다.
    *   Marketplace 및 Azure Portal에 등록할 제품에 대한 기준을 충족하는 콘텐츠를 제공해야 합니다.
    *   Azure Marketplace 참가 정책 및 게시자 약관의 조건에 동의해야 합니다.
    *   사용 약관, Microsoft 개인정보처리방침 및 Microsoft Azure Certified 프로그램 계약을 준수한다는 데 동의해야 합니다.

## <a name="create-a-new-azure-application-offer"></a>새 Azure 응용 프로그램 제품 만들기

필수 구성 요소를 충족하면 관리되는 응용 프로그램 제품을 만들 준비가 되었습니다. 제품 및 SKU를 간략히 살펴보겠습니다.

### <a name="offer"></a>제안

관리되는 응용 프로그램에 대한 제품은 게시자가 제공하는 제품의 클래스에 해당합니다. 새로운 유형의 솔루션/응용 프로그램을 Marketplace에서 사용할 수 있도록 하려는 경우 새 제품으로 설정할 수 있습니다. 제품은 SKU의 컬렉션입니다. 모든 제품은 Marketplace에 고유 엔터티로 나타납니다.

### <a name="sku"></a>SKU

SKU는 제품의 가장 작은 구매 가능 단위입니다. 같은 제품 클래스(제품) 내에서 SKU를 사용하여 다음을 구별할 수 있습니다.

* 제공되는 다양한 기능.
* 제품이 관리되는지 관리되지 않는지 여부.
* 지원되는 요금 청구 모델.

SKU는 Marketplace의 부모 제품 아래에 표시됩니다. Azure Portal에는 고유 구입 가능 엔터티로 표시됩니다.

### <a name="set-up-an-offer"></a>제품 설정

1. [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.

2. 왼쪽의 탐색 창에서 **+ 새 제품** > **Azure 응용 프로그램**을 선택합니다.

    ![새 제품](./media/managed-application-author-marketplace/newOffer.png)

3. **편집기** 보기의 왼쪽에 나타나는 양식을 작성합니다. 필수 필드는 빨간색 별표(*)로 표시됩니다.

    ![제품 설정](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    관리되는 응용 프로그램을 만드는 데 네 가지 기본 양식이 사용됩니다.

    a. 제품 설정

    b. SKU

    c. 마켓플레이스

    d. 지원

이러한 양식은 다음 섹션에 자세히 설명되어 있습니다.

## <a name="offer-settings-form"></a>제품 설정 양식
이 기본 양식을 사용하여 제품 설정을 지정합니다.

1. **제품 설정** 양식을 작성합니다. 다양한 필드는 다음과 같습니다.

    a. **제품 ID**: 이 고유 식별자는 게시자 프로필 내에서 제품을 식별합니다. 이 ID는 제품 URL, Resource Manager 템플릿 및 청구 보고서에서 볼 수 있습니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝날 수 없습니다. 최대 50문자로 제한됩니다. 제품이 라이브 상태가 되면 이 필드는 잠깁니다.

    b. **게시자 ID**: 이 드롭다운 목록을 사용하여 이 제품을 게시하려는 게시자 프로필을 선택합니다. 제품이 라이브 상태가 되면 이 필드는 잠깁니다.

    c. **이름**: 제품에 대한 이 표시 이름이 Marketplace 및 포털에 표시됩니다. 최대 50문자를 포함할 수 있습니다. 제품의 인식 가능한 브랜드 이름을 포함합니다. 회사 이름을 포함하는 것이 마케팅 전략인 경우를 제외하고 여기에 회사 이름을 포함하지 마세요. 이 제품을 자신의 고유 웹 사이트에 마케팅하는 경우 이 이름은 웹 사이트에 표시되는 이름과 같아야 합니다.

2. **저장**을 클릭하여 진행 상황을 저장합니다. 

## <a name="skus-form"></a>SKU 양식
다음 단계는 제품에 대한 SKU를 추가하는 것입니다.

1. **SKU** > **새 SKU**를 선택합니다. 

    ![새 SKU 선택](./media/managed-application-author-marketplace/newOffer_skus.png)

2. **SKU ID**를 입력합니다. SKU ID는 제품 내의 SKU에 대한 고유 식별자입니다. 이 ID는 제품 URL, Resource Manager 템플릿 및 청구 보고서에서 볼 수 있습니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝내면 안 되며 최대 50문자로 제한됩니다. 제품이 라이브 상태가 되면 이 필드는 잠깁니다. 제품 내에 여러 SKU를 포함할 수 있습니다. 게시하려는 각 이미지에 대해 SKU가 필요합니다.

3. 다음 양식의 **SKU 세부 정보** 섹션을 작성합니다.

    ![새 SKU 제공](./media/managed-application-author-marketplace/newOffer_newsku.png)

    다음 필드를 작성합니다.
    
    a. **제목**: 이 SKU의 제목을 입력합니다. 이 제목은 이 항목에 대한 갤러리에 나타납니다.

    b. **요약**: 이 SKU에 대한 간단한 요약을 입력합니다. 이 텍스트는 제목 아래에 나타납니다.

    c. **설명**: SKU에 대한 자세한 설명을 입력합니다.

    d. **SKU 형식**: 허용된 값은 **관리되는 응용 프로그램** 및 **솔루션 템플릿**입니다. 이 경우 **관리되는 응용 프로그램**을 선택합니다.

4. 다음 양식의 **패키지 세부 정보** 섹션을 작성합니다.

    ![패키지](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    다음 필드를 작성합니다.

    a. **현재 버전**: 업로드하는 패키지에 대한 버전을 입력합니다. `{number}.{number}.{number}{number}` 형식이어야 합니다.

    b. **패키지 파일 선택**: 이 패키지는 .zip 파일로 압축되는 다음 파일을 포함합니다.
    * **applianceMainTemplate.json**: 솔루션/응용 프로그램을 배포하는 데 사용되는 배포 템플릿 파일입니다. 배포 템플릿 파일을 작성하는 방법에 대한 정보는 [첫 번째 Azure Resource Manager 템플릿 만들기](resource-manager-create-first-template.md)를 참조하세요.
    * **appliancecreateUIDefinition.json**: 이 파일은 Azure Portal에서 이 솔루션/응용 프로그램을 프로비전하는 데 사용되는 사용자 인터페이스를 생성하는 데 사용됩니다. 자세한 내용은 [CreateUiDefinition 시작](managed-application-createuidefinition-overview.md)을 참조하세요.
    * **mainTemplate.json**: Microsoft.Solution/appliances 리소스만 포함하는 템플릿 파일입니다. mainTemplate 파일에는 다음과 같은 속성이 포함됩니다.

        *  **kind**: Marketplace에서 관리되는 응용 프로그램으로 **Marketplace**를 사용합니다.
        *  **ManagedResourceGroupId**: applianceMainTemplate.json에 정의된 모든 리소스가 배포되는 고객 구독에 있는 리소스 그룹입니다.
        *  **PublisherPackageId**: 패키지를 고유하게 식별하는 문자열입니다. `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}` 형식의 값을 제공합니다.

다음 이미지에 표시된 것처럼 게시 포털에서 **제품 ID** 및 **게시자 ID**를 가져옵니다.

![제품 ID](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
다음 이미지에 나와 있는 것처럼 **SKU ID**를 가져옵니다.

![SKU ID](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
다음 이미지에 나와 있는 것처럼 패키지 **버전**을 가져옵니다.

![패키지 버전](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  위의 예제에 따라 **PublisherPackageId**의 값은 `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`입니다.

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

이 패키지는 이 응용 프로그램을 성공적으로 프로비전하는 데 필요한 기타 중첩된 템플릿 또는 스크립트를 포함해야 합니다. mainTemplate.json, applianceMainTemplate.json 및 applianceCreateUIDefinition.json 파일은 루트 폴더에 있어야 합니다.

* **권한 부여**: 이 속성은 고객 구독에서 리소스에 대해 어떤 사용자가 어떤 수준의 액세스 권한을 갖는지 정의합니다. 게시자는 이를 사용하여 고객을 대신하여 응용 프로그램을 관리할 수 있습니다.
* **PrincipalId**: 이 속성은 고객 구독에서 리소스에 대한 특정 권한이 부여된 사용자, 사용자 그룹 또는 응용 프로그램의 Azure AD(Azure Active Directory) 식별자입니다. 역할 정의는 권한을 설명합니다. 
* **역할 정의**: 이 속성은 Azure AD가 제공한 모든 기본 제공 RBAC(역할 기반 액세스 제어) 역할 목록입니다. 고객을 대신하여 리소스를 관리하는 데 사용할 가장 적합한 역할을 선택할 수 있습니다.

여러 권한 부여를 추가할 수 있습니다. AD 사용자 그룹을 만들고 **PrincipalId**에서 해당 ID를 지정하는 것이 좋습니다. 이러한 방식으로 SKU를 업데이트할 필요 없이 사용자 그룹에 더 많은 사용자를 추가할 수 있습니다.

RBAC에 대한 자세한 내용은 [Azure Portal에서 RBAC 시작](../active-directory/role-based-access-control-what-is.md)을 참조하세요.

## <a name="marketplace-form"></a>Marketplace 양식

Marketplace 양식에서 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에 표시되는 필드를 요청합니다.

### <a name="preview-subscription-ids"></a>미리 보기 구독 ID

게시된 후 제품에 액세스할 수 있는 Azure 구독 ID의 목록을 입력합니다. 이러한 허용 목록 구독을 사용하여 제품을 라이브 상태로 만들기 전에 미리 본 제품을 테스트할 수 있습니다. 파트너 포털에서 최대 100개의 구독 허용 목록을 컴파일할 수 있습니다.

### <a name="suggested-categories"></a>권장 범주

목록에서 제품이 가장 잘 연결될 수 있는 범주를 5개까지 선택합니다. 이러한 범주는 고객의 제품을 [Azure Marketplace](https://azuremarketplace.microsoft.com) 및 [Azure Portal](https://portal.azure.com/)에서 사용할 수 있는 제품 범주에 매핑하는 데 사용됩니다.

#### <a name="azure-marketplace"></a>Azure 마켓플레이스

관리되는 응용 프로그램 요약은 다음 필드를 표시합니다.

![Marketplace 요약](./media/managed-application-author-marketplace/publishvm10.png)

관리되는 응용 프로그램에 대한 **개요** 탭은 다음 필드를 표시합니다.

![마켓플레이스 개요](./media/managed-application-author-marketplace/publishvm11.png)

관리되는 응용 프로그램에 대한 **계획 + 가격** 탭은 다음 필드를 표시합니다.

![Marketplace 계획](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Azure portal

관리되는 응용 프로그램 요약은 다음 필드를 표시합니다.

![포털 요약](./media/managed-application-author-marketplace/publishvm12.png)

관리되는 응용 프로그램에 대한 개요는 다음 필드를 표시합니다.

![포털 개요](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>로고 지침

클라우드 파트너 포털에 업로드하는 모든 로고에 대해 이 지침을 따릅니다.

*   Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수를 제한합니다.
*   포털의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로 이러한 색을 사용하지 마십시오. 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다. *투명한 배경을 사용하는 경우 로고 및 텍스트는 흰색, 검정색 또는 파란색이 아니어야 합니다.*
*   로고의 배경에 그라데이션 효과를 사용하지 마십시오.
*   로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 마십시오. 로고의 모양과 느낌은 평면적이어야 하며 그라데이션은 사용하지 마십시오.
*   로고가 늘어나지 않았는지 확인합니다.

#### <a name="hero-logo"></a>대표 로고

대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. 대표 아이콘을 업로드한 후에 삭제할 수 없습니다. 이때 파트너는 대표 아이콘에 대한 Marketplace 지침을 따라야 합니다.

대표 로고 아이콘에 대한 이러한 지침을 따릅니다.

*   게시자 표시 이름, 계획 제목 및 제품의 자세한 요약은 흰색으로 표시됩니다. 따라서 대표 아이콘의 배경색으로 밝은 색을 사용하지 마십시오. 대표 아이콘에는 검은색, 흰색 또는 투명한 배경이 허용되지 않습니다.
*   제품이 나열되면 대표 로고 안에 게시자 표시 이름, 계획 제목, 자세한 제품 요약과 **만들기** 버튼이 프로그래밍 방식으로 포함됩니다. 따라서 대표 로고를 디자인하는 동안 텍스트를 입력하지 마십시오. 텍스트는 프로그래밍 방식으로 해당 공간에 포함되므로 오른쪽의 빈 공간을 남겨 둡니다. 오른쪽의 텍스트에 대한 빈 공간은 415x100픽셀이어야 합니다. 왼쪽에서 370픽셀로 오프셋됩니다.

    ![대표 로고 예제](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>지원 양식

회사의 지원 연락처로 **지원** 양식을 작성합니다. 이 정보는 엔지니어링 연락처와 고객 지원 연락처일 수 있습니다.

## <a name="publish-an-offer"></a>제품 게시

모든 섹션을 작성한 후 **게시**를 선택하여 고객에게 제품을 제공하는 과정을 시작합니다.

## <a name="next-steps"></a>다음 단계

* 관리되는 응용 프로그램에 대한 소개는 [관리되는 응용 프로그램 개요](managed-application-overview.md)를 참조하세요.
* Marketplace의 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [Marketplace의 Azure 관리되는 응용 프로그램 사용](managed-application-consume-marketplace.md)을 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 게시하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 만들기 및 게시](managed-application-publishing.md)를 참조하세요.
* 서비스 카탈로그 관리되는 응용 프로그램을 사용하는 방법에 대한 자세한 내용은 [서비스 카탈로그 관리되는 응용 프로그램 사용](managed-application-consumption.md)을 참조하세요.
