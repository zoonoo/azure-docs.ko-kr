---
title: 상용 마켓플레이스에서 새 Azure Apps 오퍼 만들기
description: Microsoft 파트너 센터의 상용 마켓플레이스 포털을 사용하여 Azure 마켓플레이스, AppSource 또는 CSP(클라우드 솔루션 공급자) 프로그램을 통해 나열 또는 판매하기 위한 새 Azure Apps 오퍼를 만드는 방법
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 6b7ca1f10744bf38137c57d94d796655946c31b3
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390356"
---
# <a name="create-an-azure-application-offer"></a>Azure 애플리케이션 제품 만들기

상용 마켓플레이스에서 Azure 응용 프로그램 오퍼를 게시하는 단계는 여기에 설명되어 있습니다.

## <a name="azure-application-offer-type"></a>Azure 응용 프로그램 오퍼 유형

이 항목에서는 Azure 응용 프로그램 제공에 대한 기본 사항의 개요를 설명합니다.  마켓플레이스에서 새 Azure 응용 프로그램 오퍼를 게시하는 프로세스를 시작하기 전에 이러한 개념을 잘 알고 있어야 합니다.

### <a name="publishing-overview"></a>게시 개요

Azure [Marketplace에 대한](https://channel9.msdn.com/Events/Build/2018/BRK3603) 비디오 빌드 솔루션 템플릿 및 관리되는 응용 프로그램은 Azure 응용 프로그램 제공 유형에 대한 소개입니다.

* 어떤 쿠폰 유형을 사용할 수 있습니까?
* 필요한 기술 적 자산;
* Azure 리소스 관리자 템플릿을 작성하는 방법;
* 앱 UI 개발 및 테스트;
* 응용 프로그램 제안을 게시하는 방법;
* 응용 프로그램 검토 프로세스입니다.

### <a name="types-of-azure-application-plans"></a>Azure 응용 프로그램 계획의 유형

Azure 응용 프로그램 계획에는 관리되는 응용 프로그램 및 솔루션 템플릿의 두 가지 종류가 있습니다.

* **솔루션 템플릿은** 마켓플레이스에 솔루션을 게시하는 주요 방법 중 하나입니다. 이 계획 유형은 솔루션에 VM(단일 가상 시스템) 이외의 추가 배포 및 구성 자동화가 필요한 경우에 사용됩니다.  솔루션 템플릿을 사용하면 VM, 네트워킹 및 스토리지 리소스를 포함하여 두 개 이상의 리소스를 자동화하여 복잡한 IaaS 솔루션을 제공할 수 있습니다.  솔루션 템플릿 빌드에 대한 자세한 내용은 [Azure 리소스 관리자](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 설명서를 참조하십시오.

* **관리되는 응용 프로그램은** 하나의 주요 차이점을 가진 솔루션 템플릿과 유사합니다. 관리되는 애플리케이션에서 리소스는 앱 게시자가 관리하는 리소스 그룹에 배포됩니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다. 게시자는 지속적인 솔루션 지원을 위한 비용을 지정합니다. 관리형 애플리케이션을 사용하여 고객에게 완벽하게 관리되는 턴키 애플리케이션을 쉽게 구축하고 제공할 수 있습니다.  관리되는 애플리케이션의 장점 및 유형에 대한 자세한 내용은 [Azure Managed Applications 개요](https://docs.microsoft.com/azure/managed-applications/overview)를 참조하세요.

모든 Azure 응용 프로그램에는 아카이브의 루트 폴더에 두 개 이상의 파일이 포함됩니다. `.zip`

* [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)이라는 리소스 관리자 템플릿 파일 .  고객의 Azure 구독에 배포할 리소스를 정의하는 템플릿입니다.  리소스 관리자 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/) 또는 해당 [GitHub: Azure 리소스 관리자 빠른 시작 템플릿 리포지토리를 참조하세요.](https://github.com/azure/azure-quickstart-templates)

* [createUiDefinition.json이라는](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)Azure 응용 프로그램 만들기 경험에 대한 사용자 인터페이스 정의 .  사용자 인터페이스에서 소비자가 매개 변수 값을 제공할 수 있도록 하는 요소를 지정합니다.

모든 새 Azure 응용 프로그램 제공에는 [Azure 파트너 고객 사용 기여 GUID가](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)포함되어야 합니다.

### <a name="before-you-begin"></a>시작하기 전에

빠른 시작, 자습서 및 샘플을 제공하는 다음 Azure 애플리케이션 설명서를 검토합니다.

* [Azure 리소스 관리자 템플릿 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* 빠른 시작:

    * [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates)
    * [애플리케이션 정의 게시](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [서비스 카탈로그 앱 배포](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* 자습서:

    * [정의 파일 만들기](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [마켓플레이스 애플리케이션 게시](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* 샘플:

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [관리되는 애플리케이션 솔루션](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>기술 지식의 기초

이러한 자산을 설계, 구축 및 테스트하려면 시간이 걸리고 Azure 플랫폼과 제안을 작성하는 데 사용되는 기술에 대한 기술적 지식이 모두 필요합니다.

엔지니어링 팀에는 다음 Microsoft 기술에 대한 지식이 있어야 합니다.

* [Azure 서비스에](https://azure.microsoft.com/services/)대한 기본 이해.
* Azure [응용 프로그램을 디자인하고 설계하는](https://azure.microsoft.com/solutions/architecture/)방법.
* [Azure 가상 컴퓨터,](https://azure.microsoft.com/services/virtual-machines/) [Azure 저장소](https://azure.microsoft.com/services/?filter=storage#storage)및 Azure [네트워킹에](https://azure.microsoft.com/services/?filter=networking#networking)대한 작업 지식입니다.
* [Azure 리소스 관리자에](https://azure.microsoft.com/features/resource-manager/)대한 작업 지식 .
* [JSON의](https://www.json.org/)작업 지식 .

### <a name="suggested-tools"></a>권장되는 도구

Azure 애플리케이션을 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

개발 환경에 다음 도구를 추가하는 것이 좋습니다.

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)(다음 확장 포함):
    * 확장: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

[Azure 개발자 도구](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 검토할 수 있습니다.  또한 비주얼 스튜디오를 사용하는 [경우, 비주얼 스튜디오 마켓 플레이스](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Azure 애플리케이션 제품 만들기

Azure 응용 프로그램 오퍼를 만들려면 먼저 [파트너 센터 계정을 만들고](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) **개요** 탭을 선택한 [상용 마켓플레이스 대시보드를](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)열어야 합니다.

>[!Note]
>오퍼가 게시되면 파트너 센터에서 만든 오퍼에 대한 편집은 다시 게시한 후에만 시스템 및 스토어프론트에서 업데이트됩니다.  변경이 이루어진 후 게시를 위해 제안을 제출해야 합니다.

### <a name="create-a-new-offer"></a>새 제안 만들기

+ **새 제공** 단추를 선택한 다음 **Azure 응용 프로그램** 메뉴 항목을 선택합니다. **새 오퍼** 대화 상자가 나타납니다.

### <a name="offer-id-and-alias"></a>제공 ID 및 별칭

* **오퍼 ID**: 계정의 각 오퍼에 대한 고유 식별자입니다. 이 ID는 마켓플레이스 오퍼및 Azure 리소스 관리자 템플릿(해당하는 경우)의 URL 주소에 고객에게 표시됩니다. <br> <br> 오퍼 ID는 소문자 영숫자 문자여야 합니다(하이픈과 밑줄 포함,공백 없음). 50자로 제한되며 만들기를 선택한 후에는 변경할 수 없습니다. <br> <br> 예를 들어 여기에 `test-offer-1` 입력하면 오퍼 URL이 됩니다. `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 

* **오퍼 별칭**: 파트너 센터 내의 오퍼를 참조하는 데 사용되는 이름입니다. 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 오퍼 이름 및 기타 값과 다릅니다. 이 값은 **만들기를**선택한 후에는 변경할 수 없습니다.

**쿠폰 ID** 및 쿠폰 **별칭을**입력한 후 **만들기를**선택합니다. 그러면 오퍼의 다른 모든 부분에서 작업할 수 있습니다.

## <a name="offer-setup"></a>오퍼 설정

**제안 설정** 페이지에서다음 정보를 요청합니다. 이러한 필드를 완료한 후 **저장을** 선택해야 합니다.

### <a name="test-drive"></a>시험 사용

테스트 드라이브는 '구매 하기 전에 시도' 옵션을 제공 하 여 잠재 고객에 게 귀하의 제안을 선보일 수 있는 좋은 방법, 증가 전환 및 높은 자격을 갖춘 리드의 생성. [테스트 드라이브에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

테스트 드라이브를 사용하려면 **테스트 드라이브 활성화** 상자를 선택합니다. 그런 다음 고객이 일정 기간 동안 제품을 시험해 볼 수 있도록 [테스트 드라이브 기술 구성](#types-of-azure-application-plans) 구성에서 데모 환경을 구성해야 합니다. 

>[!Note]
>모든 Azure 응용 프로그램은 Azure 리소스 관리자 템플릿을 사용하여 구현되므로 Azure 응용 프로그램에 대해 구성할 수 있는 유일한 테스트 드라이브 유형은 [Azure 리소스 관리자 기반 테스트 드라이브입니다.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)

#### <a name="additional-test-drive-resources"></a>추가 테스트 드라이브 리소스

- [테스트 드라이브 기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [테스트 드라이브 마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [테스트 드라이브 개요 한 호출기](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>잠재 고객 관리 연결

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [잠재 고객 관리 개요를](./commercial-marketplace-get-customer-leads.md)참조하십시오.

다음 섹션으로 이동하기 전에 **저장해야** 합니다!

## <a name="properties"></a>속성

**속성** 페이지에서는 마켓플레이스에서 오퍼를 그룹화하는 데 사용되는 카테고리 및 산업, 앱 버전 및 쿠폰을 지원하는 법적 계약을 정의할 수 있습니다. 이 페이지를 완료한 후 **저장을** 선택합니다.

### <a name="category"></a>범주

최소 1개 및 최대 3개의 카테고리를 선택하여 적절한 마켓플레이스 검색 영역에 쿠폰을 배치하는 데 사용됩니다. 쿠폰이 쿠폰 설명에서 이러한 카테고리를 지원하는 방법을 문의하세요. 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>마이크로소프트 상용 시장에 대 한 표준 계약

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>오퍼 리스팅

쿠폰 목록 페이지에는 쿠폰이 나열되는 언어가 표시됩니다. 현재 **영어(미국)가** 유일한 옵션입니다.

각 언어/시장에 대한 마켓플레이스 세부 정보(오퍼 이름, 설명, 이미지 등)를 정의해야 합니다. 이 정보를 제공하려면 언어/시장 이름을 선택합니다.

> [!NOTE]
> 오퍼 리스팅 콘텐츠(예: 설명, 문서, 스크린샷, 이용 약관 등)는 "이 응용 프로그램은 [영어 이외의 언어]에서만 사용할 수 있습니다"라는 문구로 시작되는 한 영어로 작성할 필요가 없습니다. 또한 오퍼 리스팅 콘텐츠에 사용된 언어 이외의 언어로 콘텐츠를 제공하는 *유용한 링크 URL을* 제공하는 것도 허용됩니다.

### <a name="name"></a>속성

여기에 입력한 이름은 고객에게 쿠폰 목록의 제목으로 표시됩니다. 이 필드는 **오퍼 별칭에** 대해 입력한 텍스트로 미리 채워지지만 이 값을 변경할 수 있습니다. 이 이름은 상표가 지정될 수 있으며 상표 또는 저작권 기호가 포함될 수 있습니다. 이름은 50자를 초과할 수 없으며 이모티콘을 포함할 수 없습니다.

### <a name="summary"></a>요약

마켓플레이스 검색 결과에 사용될 수 있는 제품(최대 100자)에 대한 간략한 설명을 제공합니다.

### <a name="long-summary"></a>긴 요약

오퍼에 대한 더 긴 설명을 제공합니다(최대 256자). 설명은 마켓플레이스 검색 결과에 사용될 수 있습니다.

### <a name="description"></a>Description

오퍼에 대한 더 긴 설명을 제공합니다(최대 3,000자). 이 설명은 마켓플레이스 목록 개요의 고객에게 표시됩니다. 오퍼의 가치 제안, 주요 혜택, 카테고리 및/또는 산업 협회, 인앱 구매 기회 및 필요한 공개를 포함합니다. 

설명을 작성하기 위한 몇 가지 팁:  

- 설명의 처음 몇 문장에서 제품의 가치 제안을 명확하게 설명합니다. 가치 제안서에 다음 항목을 포함하십시오.
  - 제품 설명
  - 제품의 이점을 누릴 수 있는 사용자 유형
  - 제품이 해결하는 고객의 요구 또는 고통
- 처음 몇 문장은 검색 엔진 결과에 표시될 수 있다는 점에 유의하세요.  
- 제품을 판매하는 특징과 기능에 의존하지 않습니다. 대신, 전달하는 가치에 중점을 둡니다.  
- 산업별 어휘 또는 혜택 기반 단어를 최대한 많이 사용합니다. 
- HTML 태그를 사용하여 설명의 서식을 지정하고 더 매력적으로 만드는 것이 좋습니다.

### <a name="search-keywords"></a>검색 키워드

선택적으로 최대 3개의 검색 키워드를 입력하여 고객이 마켓플레이스에서 쿠폰을 찾을 수 있도록 도와줍니다. 최상의 결과를 얻으려면 설명에 이러한 키워드를 사용해 보세요.

### <a name="support-urls"></a>지원 URL

이 섹션에서는 고객이 쿠폰에 대해 더 많이 이해할 수 있도록 링크를 제공할 수 있습니다.

#### <a name="privacy-policy-url"></a>개인정보 처리방침 URL

조직의 개인 정보 보호 정책에 대한 URL을 입력합니다. 귀하는 앱이 개인 정보 보호 법률 및 규정을 준수하는지 보장하고 유효한 개인 정보 보호 정책을 제공할 책임이 있습니다.

#### <a name="useful-links"></a>유용한 링크

솔루션에 대한 선택적 추가 온라인 문서를 제공합니다.  + 링크 추가를 클릭하여 유용한 **링크를 추가합니다.**

### <a name="contacts"></a>연락처

이 섹션에서는 **지원 연락처** 및 **엔지니어링 연락처의**이름, 전자 메일 및 전화 번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에게 제공될 수 있습니다.

지원 **연락처** 섹션에서 CSP 파트너가 오퍼에 대한 지원을 찾을 수 있는 **지원 URL도** 제공해야 합니다.

### <a name="marketplace-images"></a>마켓플레이스 이미지

이 섹션에서는 고객에게 제안을 표시할 때 사용할 로고와 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식이어야 합니다.

#### <a name="store-logos"></a>스토어 로고

**작은 (48 x 48)**, **중간 (90 X 90)** 및 대형 **(216 x 216)의**세 가지 크기로 쿠폰의 로고를 제공합니다.

#### <a name="hero"></a>Hero

영웅 이미지는 선택 사항입니다. 하나를 제공하는 경우 815 x 290 픽셀을 측정해야합니다.

#### <a name="screenshots"></a>스크린샷

쿠폰의 작동 방식을 보여 주는 스크린샷을 추가합니다. 최대 5개의 스크린샷을 추가할 수 있습니다. 모든 스크린샷은 1280 x 720 픽셀이어야 합니다.

#### <a name="videos"></a>동영상

선택적으로 쿠폰을 보여주는 동영상을 최대 5개까지 추가할 수 있습니다. 이 동영상은 YouTube 및/또는 Vimeo에서 호스팅되어야 합니다. 각 동영상에 대해 동영상 이름, URL 및 동영상의 축소판 이미지(1280 x 720픽셀)를 입력합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 리스팅 리소스

- [마켓플레이스 오퍼 리스팅에 대한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>미리 보기

**미리보기** 탭을 사용하면 더 넓은 마켓플레이스 **오디언스에게** 쿠폰을 라이브로 게시하기 전에 쿠폰의 유효성을 검증하기 위해 제한된 미리 보기 잠재고객을 정의할 수 있습니다.

> [!IMPORTANT]
> 미리보기에서 오퍼를 확인한 후 오퍼가 마켓플레이스 공개 대상에게 실시간 공개되기 전에 **라이브 로이동을** 선택해야 합니다.

미리 보기 대상은 Azure 구독 ID GUID로 식별되며 각 잠재 고객에 대한 선택적 설명과 함께 쌍을 이룹니다.  이러한 필드 중 어느 것도 고객에게 표시되지 않습니다.

CSV 파일을 업로드하는 경우 최대 10개의 Azure 구독 아이디를 수동으로 추가하거나 최대 100개까지 추가합니다.  이러한 구독을 추가하면 완전히 게시되기 전에 쿠폰에 대한 미리 보기 액세스가 허용되는 잠재고객을 정의할 수 있습니다.  쿠폰이 이미 진행 중인 경우에도 오퍼의 변경 내용이나 업데이트를 테스트하기 위해 미리 보기 대상을 정의할 수 있습니다.

>[!Note]
>미리 보기 대상은 비공개 대상과 다릅니다. 미리 보기 대상은 마켓플레이스에 실시간 *게시되기 전에* 오퍼에 액세스할 수 있습니다. 또한 계획을 만들고 개인 대상만 사용할 수 있도록 선택할 수도 있습니다(요금제 가용성 탭 사용).  미리보기 대상은 제안이 마켓플레이스에 완전히 게시된 후에만 비공개 잠재고객에게만 제공되는 계획을 포함하여 모든 계획을 보고 검증할 수 있습니다.

## <a name="plan-overview"></a>계획 개요

**계획 개요** 탭을 사용하면 동일한 오퍼 내에서 다양한 요금제 옵션을 제공할 수 있습니다. 이러한 계획(클라우드 파트너 포털의 SCO라고 함)은 계획 유형(솔루션 템플릿과 관리되는 응용 프로그램), 수익 창출 또는 잠재고객측면에서 다를 수 있습니다.  마켓플레이스에 쿠폰을 나열하려면 하나 이상의 요금제 구성을 구성합니다.

일단 생성되면 계획 이름, ID, 계획 유형, 가용성(공개 또는 비공개), 현재 게시 상태 및 이 탭에서 사용 가능한 작업이 표시됩니다.

**계획 개요에서** 사용할 수 있는 **작업은** 계획의 현재 상태에 따라 다르며 다음을 포함할 수 있습니다.

* 계획 상태가 **초안인** 경우 - 초안 삭제
* 계획 상태가 **실시간** 인 경우 - 판매 계획을 중지하거나 비공개 잠재 고객을 동기화합니다.

### <a name="create-new-plan"></a>새 계획 만들기

***계획 ID*** - 이 오퍼의 각 플랜에 대해 고유한 계획 ID를 만듭니다. 이 ID는 제품 URL에서 고객에게 표시됩니다.  소문자, 숫자 문자, 대시 또는 밑줄만 사용합니다. 이 플랜 ID에는 최대 50자만 허용됩니다. 만들기를 선택한 후에는 이 ID를 수정할 수 없습니다.

***요금제 이름*** - 고객은 오퍼 내에서 선택할 플랜을 결정할 때 이 이름을 볼 수 있습니다. 이 오퍼의 각 플랜에 대해 고유한 쿠폰 이름을 만듭니다. 계획 이름은 동일한 오퍼의 일부일 수 있는 소프트웨어 계획을 차별화하는 데 사용됩니다(예: 오퍼 이름: Windows 서버; 계획: 윈도우 서버 2016, 윈도우 서버 2019).

### <a name="plan-setup"></a>계획 설정

**계획 설정** 탭을 사용하면 계획 유형에 대한 상위 수준 구성, 다른 계획의 패키지를 다시 사용할지 여부 및 계획을 사용할 수 있는 클라우드를 설정할 수 있습니다.  이 탭의 답변은 동일한 계획의 다른 탭에 표시되는 필드에 영향을 줍니다.

#### <a name="plan-type"></a>플랜 유형

Azure 응용 [프로그램 계획의 유형에](#types-of-azure-application-plans)설명된 대로 계획에 솔루션 템플릿 또는 관리되는 응용 프로그램이 포함될지 여부를 선택합니다.

#### <a name="this-plan-reuses-packages"></a>이 계획은 패키지를 재사용

동일한 유형의 계획이 두 개 이상 있고 패키지가 동일한 경우 **다른 계획에서 패키지를 다시 사용하는 이 계획을**선택할 수 있습니다.  이 옵션을 선택하면 이 오퍼에서 패키지를 재사용할 수 있도록 동일한 유형의 다른 계획 중 하나를 선택할 수 있습니다. 

>[!Note]
>다른 계획에서 패키지를 다시 사용하면 전체 기술 구성 탭이 이 계획에서 사라집니다.  향후 업데이트를 포함하여 다른 계획의 기술 구성 세부 정보도 이 계획에 사용됩니다. <br> <br> 또한 이 계획이 게시된 후에는 이 설정을 변경할 수 없습니다.

#### <a name="cloud-availability"></a>클라우드 가용성

이 계획은 하나 이상의 클라우드에서 사용할 수 있어야 합니다. 

**마켓플레이스** 통합이 있는 모든 공용 Azure 지역의 고객에게 솔루션을 배포할 수 있도록 하려면 공용 Azure 옵션을 선택합니다.  [지리적 가용성에](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)대해 자세히 알아보십시오.

Azure **정부 클라우드** 옵션을 선택하여 이러한 엔터티를 제공할 자격이 있는 미국 연방, 주, 지역 또는 부족 및 파트너의 고객에 대한 제어된 액세스 권한이 있는 정부 커뮤니티 클라우드인 [Azure Government Cloud에서](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)솔루션을 배포할 수 있도록 합니다.  게시자는 이 클라우드 커뮤니티에 서비스를 제공하기 위해 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다.  Azure 정부는 물리적으로 격리된 데이터 센터 및 네트워크를 사용합니다(미국에만 있음).  [Azure 정부에](https://aka.ms/azuregovpublish)게시하기 전에 특정 끝점이 다를 수 있으므로 환경에서 솔루션을 테스트하고 유효성을 검사하는 것이 좋습니다. 솔루션을 단계및 테스트하려면 이 [링크에서](https://azure.microsoft.com/global-infrastructure/government/request/)평가판 계정을 요청하십시오.

>[!Note]
>계획이 특정 클라우드에서 사용 가능한 것으로 게시되면 해당 클라우드를 제거할 수 없습니다.

**Azure 정부 클라우드 인증**

이 옵션은 Azure **정부 클라우드가** **클라우드 가용성**에서 선택된 경우에만 표시됩니다.

Azure 정부 서비스는 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS와 같은 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다.  이러한 프로그램에 대한 인증을 인식하려면 인증을 설명하는 링크를 최대 100개까지 제공할 수 있습니다.  이러한 링크는 프로그램에 있는 리스팅에 대한 링크또는 자신의 웹 사이트에서 사용자의 준수에 대한 설명에 대한 링크일 수 있습니다.  이러한 링크는 Azure 정부 클라우드 고객에게만 표시됩니다.

## <a name="plan-listing"></a>계획 목록

**계획 목록** 탭에는 동일한 오퍼에 대해 서로 다른 계획 간에 다를 수 있는 계획별 목록 정보가 표시됩니다.

### <a name="name"></a>속성

계획을 만들 때 지정한 이름에 따라 미리 채워집니다.  이 이름은 마켓플레이스에 표시되는 이 "소프트웨어 계획"의 제목으로 표시됩니다.  최대 100자까지 포함될 수 있습니다.

### <a name="summary"></a>요약

소프트웨어 계획에 대한 간략한 요약을 제공합니다.  최대 100자까지 포함될 수 있습니다.

### <a name="description"></a>Description

이 설명은 이 소프트웨어 계획이 고유한 이유와 제품 내의 다른 소프트웨어 계획과 어떤 차이점을 설명하는 기회입니다. 최대 2,000자까지 포함될 수 있습니다.

이 필드를 완료한 후 **저장을** 선택합니다.

## <a name="availability"></a>가용성

**가용성** 탭은 솔루션 템플릿 계획에만 표시됩니다.  특정 고객(개인 대상)에게만 계획을 표시하고 다른 솔루션 템플릿 또는 관리되는 응용 프로그램에서만 사용할 수 있도록 계획을 숨길지 여부를 만들 수 있습니다.

### <a name="plan-audience"></a>잠재고객 계획

모든 사람이 볼 수 있도록 각 계획을 구성하거나 선택한 특정 대상만 구성할 수 있습니다. Azure 구독 아이디를 사용하여 이 제한된 대상에 멤버십을 할당할 수 있습니다.

**개인 정보 보호 / 개인 요금제** (선택 사항 확인란) - 이 확인란을 선택하여 선택한 제한된 대상에게만 계획을 비공개로 표시하고 표시하도록 합니다. 비공개 요금제로 게시되면 대상 을 업데이트하거나 모든 사람이 플랜을 사용할 수 있도록 선택할 수 있습니다. 계획이 모든 사람에게 표시되는 대로 게시되면 모든 사람이 볼 수 있어야 합니다. (계획을 다시 개인 계획으로 구성할 수 없습니다.)

**제한된 잠재고객(Azure 구독 아이디)** - 이 개인 요금제에 액세스할 수 있는 잠재고객을 할당합니다. 액세스는 할당된 각 Azure 구독 ID에 대한 설명을 포함하는 옵션과 함께 Azure 구독 ID를 사용하여 할당됩니다. .csv 스프레드시트 파일을 가져오는 경우 최대 10개의 구독 아이디를 추가하거나 20,000명의 고객 구독 아이디를 추가할 수 있습니다.  Azure 구독 아이디는 GUID로 표시되며 문자는 소문자여야 합니다.

>[!Note]
>비공개 대상(또는 제한된 잠재고객)은 [**미리 보기**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) 탭에서 정의한 미리 보기 대상과 다릅니다.  미리보기 잠재고객은 마켓플레이스에 실시간 게시된 오퍼보다 *더 많은* 오퍼에 액세스할 수 있습니다. 비공개 잠재고객 지정은 특정 계획에만 적용되지만 미리 보기 대상자는 유효성 검사를 위해 모든 계획(비공개 또는 그렇지 않음)을 볼 수 있습니다.

### <a name="hide-plan"></a>계획 숨기기

솔루션 템플릿이 다른 솔루션 템플릿 또는 관리되는 응용 프로그램을 참조할 때만 간접적으로 배포되는 경우 이 확인란을 선택하여 솔루션 템플릿을 게시하지만 직접 검색하고 검색하는 고객으로부터 숨기십시오.

## <a name="pricing-and-availability"></a>가격 책정 및 가용성

**가격 책정 및 가용성** 탭은 관리되는 응용 프로그램 계획에만 표시됩니다.  이 플랜을 사용할 수 있는 시장, 솔루션 관리 의 월별 가격 및 모든 사람이 또는 특정 고객(개인 대상)에게만 계획을 표시할지 여부를 구성할 수 있습니다.

### <a name="markets"></a>시장

모든 플랜은 적어도 하나의 시장에서 사용할 수 있어야 합니다. 이 플랜을 사용할 수 있도록 하려는 시장 위치에 대한 확인란을 선택합니다. Microsoft가 사용자를 대신하여 판매 및 사용세를 송금하는 "세금 환급" 국가를 선택하기 위한 검색 상자와 버튼이 포함되어 있습니다.

이미 미국 달러(USD)로 플랜 가격을 설정하고 다른 시장 위치를 추가한 경우 새 시장의 가격은 현재 환율에 따라 계산됩니다. 게시하기 전에 항상 각 시장의 가격을 검토하십시오. 변경 사항을 저장한 후 "내보내기 가격(xlsx)" 링크를 사용하여 가격을 검토할 수 있습니다.

### <a name="pricing"></a>가격 책정

이 플랜의 월별 가격을 제공합니다.  이 가격은 이 솔루션에서 배포한 리소스로 인해 발생하는 모든 Azure 인프라 또는 종량제 소프트웨어 비용에 추가됩니다.

현지 통화(USD = 미국 달러)로 설정된 가격은 설정 중에 사용할 수 있는 현재 환율을 사용하여 선택한 모든 시장의 현지 통화로 변환됩니다. 가격 스프레드시트를 내보내고 각 시장의 가격을 검토하여 게시하기 전에 이러한 가격의 유효성을 검사합니다. 개별 시장에서 사용자 지정 가격을 설정하려면 가격 스프레드시트를 수정하고 가져옵니다. 

>[!Note]
>가격 데이터를 내보내려면 먼저 가격 변경 사항을 저장해야 합니다.

계획이 게시된 후 변경할 수 있는 내용에 대한 몇 가지 제한 사항이 있으므로 게시하기 전에 가격을 신중하게 검토합니다.  

>[!Note]
>플랜의 시장 가격이 게시되면 나중에 변경할 수 없습니다.

### <a name="plan-audience"></a>잠재고객 계획

모든 사람이 볼 수 있도록 각 계획을 구성하거나 선택한 특정 대상만 구성할 수 있습니다. Azure 구독 아이디를 사용하여 이 제한된 대상에 멤버십을 할당할 수 있습니다.

**개인 정보 보호 / 개인 요금제** (선택 사항 확인란) - 이 확인란을 선택하여 선택한 제한된 대상에게만 계획을 비공개로 표시하고 표시하도록 합니다. 비공개 요금제로 게시되면 대상 을 업데이트하거나 모든 사람이 플랜을 사용할 수 있도록 선택할 수 있습니다. 계획이 모든 사람에게 표시되는 대로 게시되면 모든 사람이 볼 수 있어야 합니다. (계획을 다시 개인 계획으로 구성할 수 없습니다.)

**제한된 잠재고객(Azure 구독 아이디)** - 이 개인 요금제에 액세스할 수 있는 잠재고객을 할당합니다. 액세스는 할당된 각 Azure 구독 ID에 대한 설명을 포함하는 옵션과 함께 Azure 구독 ID를 사용하여 할당됩니다. .csv 스프레드시트 파일을 가져오는 경우 최대 10개의 구독 아이디를 추가하거나 20,000명의 고객 구독 아이디를 추가할 수 있습니다.  Azure 구독 아이디는 GUID로 표시되며 문자는 소문자여야 합니다.

>[!Note]
>비공개 대상(또는 제한된 잠재고객)은 [**미리 보기**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) 탭에서 정의한 미리 보기 대상과 다릅니다.  미리보기 잠재고객은 마켓플레이스에 실시간 게시된 오퍼보다 *더 많은* 오퍼에 액세스할 수 있습니다. 비공개 잠재고객 지정은 특정 계획에만 적용되지만 미리 보기 대상자는 유효성 검사를 위해 모든 계획(비공개 또는 그렇지 않음)을 볼 수 있습니다.

## <a name="technical-configuration"></a>기술 구성 

**기술 구성** 탭을 사용하면 고객이 계획을 배포할 수 있도록 배포 패키지를 업로드할 수 있습니다.

>[!Note]
>**계획 설정** 탭의 다른 계획에서 패키지를 다시 사용하도록 이 계획을 구성한 경우 이 탭이 표시되지 않습니다.

### <a name="package-details"></a>패키지 세부 정보

**패키지 세부 정보** 서브탭을 사용하면 기술 구성의 초안 버전을 편집할 수 있습니다.

***버전*** - 기술 구성의 현재 버전을 할당합니다.  이 페이지에 변경 을 게시할 때마다 이 버전을 증가시입니다. 버전은 형식이어야 `{integer}.{integer}.{integer}`합니다.

***패키지*** 파일`.zip`( ) - 이 패키지에는 이 계획에 필요한 모든 템플릿 파일과 파일로 패키지된 추가 리소스가 `.zip` 포함되어 있습니다.

모든 Azure 응용 프로그램 계획 패키지에는 `.zip` 아카이브의 루트 폴더에 다음 두 파일이 포함되어야 합니다.

* [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)이라는 리소스 관리자 템플릿 파일 .  이 템플릿은 고객 Azure 구독에 리소스 배포를 자동화합니다.  리소스 관리자 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/) 또는 해당 [GitHub: Azure 리소스 관리자 빠른 시작 템플릿 리포지토리를 참조하세요.](https://github.com/azure/azure-quickstart-templates)

* [createUiDefinition.json이라는](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)Azure 응용 프로그램 만들기 경험에 대한 사용자 인터페이스 정의 .

모든 새 Azure 응용 프로그램 제공에는 [Azure 파트너 고객 사용 기여](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) GUID도 포함되어야 합니다.

### <a name="previously-published-packages"></a>이전에 게시된 패키지 

**이전에 게시된 패키지 서브탭을** 사용하면 기술 구성의 게시된 모든 버전을 볼 수 있습니다.

## <a name="technical-configuration-managed-application-plans-only"></a>기술 구성(관리되는 응용 프로그램 계획만)

관리되는 응용 프로그램 계획은 위에서 설명한 **버전** 및 **패키지 파일** 필드 를 넘어 **기술 구성** 탭에 추가 복잡성이 있습니다. 

### <a name="enable-just-in-time-jit-access"></a>적시에(JIT) 액세스 활성화

이 계획에 대해 JIT(정시) 액세스를 활성화하려면 이 옵션을 선택합니다.  JIT 액세스를 사용하면 문제 해결 또는 유지 관리를 위해 관리되는 응용 프로그램의 리소스에 대한 높은 액세스 권한을 요청할 수 있습니다. 항상 리소스에 대한 읽기 전용 액세스 권한이 있지만 특정 기간 동안 더 많은 액세스 권한을 가질 수 있습니다.  자세한 내용은 [Azure 관리형 응용 프로그램에 대한 적시 액세스 및 요청을 참조하세요.](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)  관리되는 응용 프로그램의 소비자가 계정에 영구 액세스 권한을 부여하도록 요구하려면 이 옵션을 선택하지 않은 상태로 둡니다.

>[!Note]
>이 기능을 지원하려면 `createUiDefinition.json` 파일을 업데이트해야 합니다.  

### <a name="deployment-mode"></a>배포 모드

이 계획을 배포할 때 **전체** 배포 모드 또는 **증분 배포 모드를** 구성할지 여부를 선택합니다. 

* **전체 모드에서**고객이 응용 프로그램을 다시 배포하면 리소스가 `mainTemplate.json`에 정의되지 않은 경우 관리되는 리소스 그룹의 리소스가 제거됩니다. 
* **증분 모드에서**응용 프로그램을 다시 배포하면 기존 리소스가 변경되지 않습니다.

배포 모드에 대한 자세한 내용은 [Azure 리소스 관리자 배포 모드를](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)참조하십시오.

### <a name="notification-endpoint-url"></a>알림 끝점 URL

이 계획 버전의 관리되는 응용 프로그램 인스턴스에서 모든 CRUD 작업에 대한 알림을 받도록 HTTPS Webhook 끝점을 지정합니다.

### <a name="customize-allowed-customer-actions"></a>허용된 고객 작업 사용자 지정

기본적으로 사용할 수 있는 "작업`*/read`외에 고객이 관리되는 리소스에서 수행할 수 있는 작업을 지정하려면 이 옵션을 선택합니다. 

고객이 여기서 수행할 수 있도록 하려면 세미콜론으로 구분하여 수행할 추가 작업을 나열합니다.  자세한 내용은 [Azure 리소스에 대한 할당 거부 이해를](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)참조하십시오.  사용 가능한 작업에 대해서는 [Azure Resource Manager 리소스 공급자 작업](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)을 참조하세요. 예를 들어 소비자가 가상 머신을 다시 시작하도록 허용하려면 허용되는 작업에 `Microsoft.Compute/virtualMachines/restart/action`을 추가합니다.

### <a name="global-azure--azure-government-cloud"></a>글로벌 Azure / Azure 정부 클라우드

지원되는 각 클라우드에서 이 관리되는 응용 프로그램에 대한 관리 액세스 권한을 가진 사람을 나타냅니다.  관리되는 리소스 그룹에 대한 권한을 부여하려는 사용자, 그룹 또는 응용 프로그램은 AAD(Azure Active Directory) ID를 사용하여 식별됩니다.

***Azure Active Directory 테넌트 ID*** - 권한을 부여하려는 사용자, 그룹 또는 응용 프로그램의 ID를 포함하는 AAD 테넌트 ID(디렉터리 ID라고도 함).  Azure [Active Directory의 속성에서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)Azure 포털에서 AAD 테넌트 ID를 찾을 수 있습니다.

***권한 부여*** - 관리되는 리소스 그룹에 권한을 부여하려는 사용자, 그룹 또는 응용 프로그램의 Azure Active Directory 개체 ID를 추가합니다. [Azure 포털의 Azure Active Directory 사용자 블레이드에서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)찾을 수 있는 보안 주체 ID로 사용자를 식별합니다.

각 보안 주체에 대해 목록에서 Azure AD 기본 제공 역할 중 하나를 선택합니다(소유자 또는 참여자). 선택한 역할은 보안 주체가 고객 구독의 리소스에 대해 가질 수 있는 권한을 설명합니다. 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.  RBAC(역할 기반 액세스 제어)에 대한 자세한 내용은 [Azure 포털에서 RBAC로 시작하기 를](https://docs.microsoft.com/azure/role-based-access-control/overview)참조하십시오.

>[!Note]
>클라우드당 최대 100개의 권한 부여를 추가할 수 있지만 일반적으로 Active Directory 사용자 그룹을 만들고 "보안 주체 ID"에 ID를 지정하는 것이 더 쉽습니다.  이렇게 하면 계획을 배포한 후 관리 그룹에 사용자를 더 추가하고 권한 부여를 추가하기 위해 계획을 업데이트할 필요가 줄어듭니다.

### <a name="policy-settings"></a>정책 설정

관리되는 응용 프로그램에 [Azure 정책을](https://docs.microsoft.com/azure/governance/policy/overview) 적용하여 배포된 솔루션에 대한 규정 준수 요구 사항을 지정합니다.  매개 변수 값의 형식 및 정책 정의는 [Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/index)을 참조하세요.  최대 5개의 정책을 구성할 수 있으며 각 정책 옵션의 인스턴스는 하나만 구성할 수 있습니다.  일부 정책에는 추가 매개 변수가 필요합니다.  표준 SKU는 감사 정책에 필요합니다.  정책 이름은 50자로 제한됩니다.

## <a name="co-sell"></a>공동 판매

제안을 게시하는 것은 전적으로 선택 사항입니다. 공동 판매 준비 및 IP 공동 판매 준비 상태를 달성하는 데 필요합니다. 제공한 정보는 Microsoft 영업 팀에서 고객의 요구에 맞는 솔루션을 평가할 때 솔루션에 대해 자세히 알아보는 데 사용됩니다. 고객에게직접 사용할 수 없습니다.

이 탭을 완료하는 데 대한 자세한 내용은 [파트너 센터의 공동 판매 옵션을](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)참조하십시오.

## <a name="test-drive"></a>시험 사용

**테스트 드라이브** 탭을 사용하면 데모(또는 "테스트 드라이브")를 설정하여 고객이 제품을 구매하기 전에 제품을 시험해 볼 수 있습니다. 이 기사에서 자세히 알아보기 [테스트 드라이브란 무엇입니까?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  오퍼에 대한 테스트 드라이브를 더 이상 제공하지 않으려면 **제안 설정** 페이지로 돌아가서 **테스트 드라이브 사용 활성화를**선택 취소합니다.

### <a name="technical-configuration"></a>기술 구성

Azure 응용 프로그램은 기본적으로 Azure 리소스 관리자 테스트 드라이브 유형을 사용합니다.  자세한 내용은 [Azure 리소스 관리자 테스트 드라이브에 대한 기술 구성을](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) 참조하십시오.

### <a name="deployment-subscription-details"></a>배포 구독 세부 정보

대신 테스트 드라이브를 배포하려면 별도의 고유한 Azure 구독을 만들고 제공하십시오. (Power BI 테스트 드라이브는 필요하지 않습니다).

- **Azure 구독 ID(Azure** 리소스 관리자 및 논리 앱에 필요): 구독 ID를 입력하여 리소스 사용량 보고 및 청구에 대한 Azure 계정 서비스에 대한 액세스 권한을 부여합니다. 아직 테스트 드라이브에 사용할 [별도의 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 것이 좋습니다. [Azure 포털에](https://portal.azure.com/) 로그인하고 왼쪽 메뉴의 구독 탭으로 이동하여 Azure 구독 **ID를** 찾을 수 있습니다. 탭을 선택하면 구독 ID가 표시됩니다(예: "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD 테넌트** ID(필수): Azure Active Directory(AD) [테넌트 ID를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)입력합니다. 이 ID를 찾으려면 [Azure 포털에](https://portal.azure.com/)로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택하고 **속성을 선택한 다음 나열된 **디렉토리 ID** 번호(예: 50c464d3-4930-493c-963c-1e951d15360e)를 찾습니다. 도메인 이름 URL을 사용하여 조직의 테넌트 ID를 [https://www.whatismytenantid.com](https://www.whatismytenantid.com)조회할 수도 있습니다.

- **Azure AD 테넌트 이름(동적** 365에 필요): Azure Active Directory(AD) 이름을 입력합니다. 이 이름을 찾으려면 오른쪽 상단 모서리에 있는 [Azure Portal에](https://portal.azure.com/)로그인하면 테넌트 이름이 계정 이름 아래에 나열됩니다.

- **Azure AD 앱** ID(필수): Azure Active Directory(AD) [응용 프로그램 ID를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)입력합니다. 이 ID를 찾으려면 [Azure 포털에](https://portal.azure.com/)로그인하고 왼쪽 메뉴에서 Active Directory 탭을 선택하고 **앱 등록을**선택한 다음 나열된 **응용 프로그램 ID** 번호(예: 50c464d3-4930-493c-963c-1e951d15360e)를 찾습니다.

- **Azure AD 앱 클라이언트** 보안(필수): Azure AD 응용 프로그램 [클라이언트 보안](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)을 입력합니다. 이 값을 찾으려면 Azure [포털에](https://portal.azure.com/)로그인합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택하고 **앱 등록을**선택한 다음 테스트 드라이브 앱을 선택합니다. 그런 다음 **인증서 및 비밀을**선택하고 새 클라이언트 **비밀을**선택하고 설명을 입력하고 **만료**되지 **않는 것을** 선택한 다음 **추가를**선택합니다. 값을 복사해야 합니다. (값을 복사하기 전에 페이지에서 멀리 이동하지 마십시오, 그렇지 않으면 값에 대한 액세스 권한이 없습니다.)

다음 섹션으로 이동하기 전에 **저장해야** 합니다!

### <a name="test-drive-listings-optional"></a>테스트 드라이브 목록(선택 사항)

**테스트 드라이브** 탭아래에 있는 테스트 **드라이브 목록** 옵션에는 테스트 드라이브를 사용할 수 있는 언어(및 시장)가 표시되며, 현재 영어(미국)가 사용할 수 있는 유일한 위치입니다. 또한 이 페이지에는 언어별 목록의 상태와 추가된 날짜/시간이 표시됩니다. 각 언어/시장에 대한 테스트 드라이브 세부 정보(설명, 사용자 설명, 비디오 등)를 정의해야 합니다.

- **설명(필수):** 테스트 드라이브, 시연할 목표, 사용자가 실험할 목표, 탐색할 기능 및 사용자가 오퍼를 획득할지 여부를 결정하는 데 도움이 되는 관련 정보를 설명합니다. 이 필드에는 최대 3,000자의 텍스트를 입력할 수 있습니다. 

- **액세스 정보(Azure** 리소스 관리자 및 논리 테스트 드라이브에 필요): 이 테스트 드라이브에 액세스하고 사용하기 위해 고객이 알아야 할 사항을 설명합니다. 오퍼를 사용하기 위한 시나리오와 고객이 테스트 드라이브 전체에서 기능에 액세스하기 위해 알아야 할 사항을 정확히 알아보십시오. 이 필드에는 최대 10,000자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서(필수):** 테스트 드라이브 환경을 심층분석해 보십시오. 사용자 설명서는 고객이 테스트 드라이브를 경험하면서 얻을 수 있는 것을 정확히 다루어야 하며, 고객이 가질 수 있는 질문에 대한 참조 역할을 해야 합니다. 파일은 PDF 형식으로 되어 있어야 하며 업로드 후 최대 255자(최대 255자)의 이름을 지정해야 합니다.

- **동영상 추가(선택** 사항): 동영상은 YouTube 또는 Vimeo에 업로드하고 여기에 링크 및 썸네일 이미지(533 x 324 픽셀)를 참조하여 고객이 테스트 드라이브를 더 잘 이해할 수 있도록 하여 제품의 기능을 성공적으로 사용하고 혜택을 강조하는 시나리오를 이해하는 방법을 포함하여 테스트 드라이브를 더 잘 이해할 수 있도록 할 수 있습니다.
  - **이름(필수)**
  - **URL(유튜브 또는 비메오만 해당)** (필수)
  - **썸네일 (533 x 324 px)**: 이미지 파일은 PNG 형식이어야 합니다.

이 필드를 완료한 후 **저장을** 선택합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기에 제안 제출

오퍼의 모든 필수 섹션을 완료한 후 포털의 오른쪽 상단 모서리에 **게시를** 선택합니다. **검토 및 게시** 페이지로 리디렉션됩니다. 

이 제안을 처음 게시하는 경우 다음을 수행할 수 있습니다.

- 오퍼의 각 섹션에 대한 완료 상태를 참조하십시오.
    - *시작되지 않음* - 섹션이 만지지 않았으며 완료해야 한다는 의미입니다.
    - *불완전* - 섹션에 수정해야 하거나 더 많은 정보를 제공해야 하는 오류가 있음을 의미합니다. 섹션으로 돌아가서 업데이트하십시오.
    - *완료* - 섹션이 완료되고 필요한 모든 데이터가 제공되었으며 오류가 없음을 의미합니다. 오퍼의 모든 섹션은 제안을 제출하기 전에 완전한 상태여야 합니다.
- 인증 팀에 테스트 지침을 제공하여 앱을 이해하는 데 도움이 되는 추가 메모 외에 앱이 올바르게 테스트되었는지 확인합니다.
- 제출을 선택하여 게시 제안을 **제출합니다.** 제안의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일을 보내드립니다. 파트너 센터로 돌아가서 쿠폰을 공개(또는 비공개 오퍼의 경우 개인 청중에게 게시할 수 있도록 **Go-live)를** 선택합니다.

### <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

게시 프로세스의 **수동 유효성 검사** 단계는 오퍼및 관련 기술 자산(특히 Azure Resource Manager 템플릿)에 대한 광범위한 검토를 나타내며, 문제는 일반적으로 PR(끌어오기 요청) 링크로 표시됩니다. 이러한 PR을 보고 응답하는 방법에 대한 설명은 [검토 피드백 처리](./azure-apps-review-feedback.md)를 참조하세요.

하나 이상의 게시 단계에서 오류가 발생하면 해당 오류를 수정하고 제안을 다시 게시해야 합니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
