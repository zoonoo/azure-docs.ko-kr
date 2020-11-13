---
title: 상업적 marketplace에 대 한 Azure 애플리케이션 제품 계획
description: Microsoft 파트너 센터에서 상용 Marketplace 포털을 사용 하 여 Azure Marketplace를 나열 하거나 판매 하는 새 Azure 응용 프로그램 제품을 계획 하는 방법 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 계획 하는 방법에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 4cb707896aa7874aa2bf287723e8a53d7d6d974c
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577790"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>상업적 marketplace에 대 한 Azure 애플리케이션 제품 계획

이 문서에서는 Microsoft 상업적 marketplace에 Azure 애플리케이션 제품을 게시 하기 위한 다양 한 옵션 및 요구 사항을 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 애플리케이션 제품을 디자인, 빌드 및 테스트하려면 Azure 플랫폼 및 제품을 빌드하는 데 사용되는 기술에 관한 기술적 지식이 둘 다 필요합니다. 엔지니어링 팀에는 다음 Microsoft 기술에 대한 지식이 있어야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 관한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking)에 관한 실무 지식
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 관한 실무 지식
- [JSON](https://www.json.org/)에 관한 실무 지식

### <a name="technical-documentation-and-resources"></a>기술 문서 및 리소스

상업적 marketplace에 대 한 Azure 응용 프로그램 제품을 계획할 때 다음 리소스를 검토 합니다.

- [Azure Resource Manager 템플릿 이해](/azure/azure-resource-manager/templates/template-syntax)
- 빠른 시작:
    - [Azure Quickstart 템플릿](https://azure.microsoft.com/documentation/templates/)
    - [Azure 템플릿 모범 사례 가이드](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [애플리케이션 정의 게시](/azure/managed-applications/publish-service-catalog-app)
    - [서비스 카탈로그 앱 배포](/azure/managed-applications/deploy-service-catalog-quickstart)
- 자습서:
    - [정의 파일 만들기](/azure/managed-applications/publish-service-catalog-app)
- 샘플:
    - [Azure CLI](/azure/managed-applications/cli-samples)
    - [Azure PowerShell](/azure/managed-applications/powershell-samples)
    - [관리되는 애플리케이션 솔루션](/azure/managed-applications/sample-projects)

[Azure Marketplace용 관리형 애플리케이션 및 솔루션 템플릿 빌드](https://channel9.msdn.com/Events/Build/2018/BRK3603) 동영상에서는 Azure 애플리케이션 제품 유형을 포괄적으로 소개합니다.

- 제공 되는 제품 유형
- 필요한 기술 자산
- Azure Resource Manager 템플릿을 제작 하는 방법
- 앱 UI 개발 및 테스트
- 앱 제공을 게시 하는 방법
- 응용 프로그램 검토 프로세스

### <a name="suggested-tools"></a>권장되는 도구

Azure 애플리케이션을 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

개발 환경에 다음 도구를 추가하는 것이 좋습니다.

- [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)(다음 확장 포함):
    - 확장: [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - 확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

[Azure 개발자 도구](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 확인할 수 있습니다. Visual Studio를 사용 하는 경우 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)를 참조 하세요.

## <a name="listing-options"></a>나열 옵션

제품이 게시 되 면 제품의 목록 옵션이 제품 목록 페이지의 왼쪽 위 모서리에 단추로 표시 됩니다. 예를 들어 다음 스크린샷에서는 _지금 가져오기_ 단추를 사용 하 여 Azure Marketplace의 제품 목록 페이지를 보여 줍니다. 테스트 드라이브를 제공 하도록 선택한 경우에는 _테스트 드라이브_ 단추도 표시 됩니다.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Azure Marketplace에 대 한 목록 페이지를 보여 줍니다.":::

## <a name="test-drive"></a>시험 사용

고객이 제품을 구입 하기 전에 제품을 사용해 볼 수 있도록 하는 Azure 애플리케이션 제품에 대 한 시험 사용을 사용 하도록 선택할 수 있습니다. 테스트 드라이브에 대 한 자세한 내용은 [test drive 란?](what-is-test-drive.md)을 참조 하세요. 다양 한 종류의 테스트 드라이브를 구성 하는 방법에 대 한 자세한 내용은 [테스트 드라이브 기술 구성](test-drive-technical-configuration.md)을 참조 하세요.

[시험 사용 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) 를 읽고 [테스트 드라이브 개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF를 다운로드할 수도 있습니다 (팝업 차단이 해제 되어 있는지 확인).

> [!NOTE]
> Azure Resource Manager 템플릿을 사용 하 여 모든 Azure 응용 프로그램을 구현 하는 경우에도 사용자가 확인 해야 하는 정보입니다. Azure 애플리케이션에 사용할 수 있는 유일한 테스트 드라이브 유형은 [Azure Resource Manager 기반 테스트 드라이브](azure-resource-manager-test-drive.md)입니다.

## <a name="customer-leads"></a>잠재 고객

제품을 CRM (고객 관계 관리) 시스템에 연결 하 여 고객 정보를 수집 해야 합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 제품을 찾은 제품 이름, ID 및 온라인 스토어와 함께 이러한 고객 세부 정보는 구성 된 CRM 시스템으로 전송 됩니다. 상업적 marketplace는 Azure 테이블을 사용 하거나 전원 자동화를 사용 하 여 HTTPS 끝점을 구성 하는 옵션과 함께 다양 한 CRM 시스템을 지원 합니다.

제공을 만든 후 또는 후에 언제 든 지 CRM 연결을 추가 하거나 수정할 수 있습니다. 자세한 지침은 [상용 marketplace 제품의 고객 리드](partner-center-portal/commercial-marketplace-get-customer-leads.md)를 참조 하세요.

## <a name="categories-and-subcategories"></a>범주 및 하위 범주

제품을 적절 한 상업적 marketplace 검색 영역으로 그룹화 하기 위해 하나 이상의 범주를 선택할 수 있습니다. 각 기본 및 보조 범주에 대해 최대 2 개의 하위 범주를 선택할 수 있습니다. 범주 및 하위 범주의 전체 목록은 [제품 목록 모범 사례](gtm-offer-listing-best-practices.md#categories)를 참조 하세요.

## <a name="legal-contracts"></a>법적 계약

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft는 상업적 marketplace에서 제품에 사용할 수 있는 표준 계약을 제공 합니다. 표준 계약에 따라 소프트웨어를 제공 하는 경우 고객은 한 번만 읽고 동의 하면 되며 사용자 지정 사용 약관을 만들 필요가 없습니다.

표준 계약을 사용 하도록 선택 하는 경우 표준 계약에 유니버설 수정 용어 및 최대 10 개의 사용자 지정 개정를 추가할 수 있습니다. 표준 계약 대신 사용자 고유의 사용 조건을 사용할 수도 있습니다. 이러한 세부 정보는 **속성** 페이지에서 관리 합니다. 자세한 내용은 [Microsoft 상업적 marketplace에 대 한 표준 계약](standard-contract.md)을 참조 하세요.

> [!NOTE]
> 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 한 후에는 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. 이는 "또는" 시나리오입니다. 표준 계약이 나 사용자 고유의 사용 약관에 따라 솔루션을 제공 합니다. 표준 계약의 조건을 수정 하려면 표준 계약 개정을 통해이 작업을 수행할 수 있습니다.

## <a name="offer-listing-details"></a>제안 목록 세부 정보

파트너 센터에서 새 Azure 애플리케이션 제품을 만들 때 제품 목록 페이지에 텍스트, 이미지, 선택적 비디오 및 기타 세부 정보를 입력 합니다. 다음 예에 표시 된 것 처럼 Azure Marketplace에서 제품 목록을 검색할 때 표시 되는 정보입니다.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="이 제품이 Azure Marketplace 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 로고
2. 범주
3. 지원 주소 (링크)
4. 사용 약관
5. 개인 정보 취급 방침 주소 (링크)
6. Offer name
7. 요약
8. Description
9. 스크린샷/비디오

다음 스크린샷은 Azure Portal에서 제공 정보를 표시 하는 방법을 보여 줍니다.

[![이 제품이 Azure Portal 표시 되는 방식을 보여 줍니다.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>호출 설명

1. 제목
2. Description
3. 유용한 링크
4. 스크린샷

> [!NOTE]
> 제품 설명이 "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다."로 시작 하는 경우 제품 목록 콘텐츠가 영어로 표시 되지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 이러한 항목 중 일부를 미리 준비 합니다. 별도로 언급 하지 않는 한 다음 항목이 필요 합니다.

- **이름** :이 이름은 상용 marketplace에서 제품의 제목으로 표시 됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약** : 100 자 이하로 줄 바꿈이 없는 일반 텍스트로 제공 되는 제품의 목적 또는 기능을 단일 문장으로 설명 합니다. 이 요약은 상용 marketplace 목록 검색 결과에 사용 됩니다.
- **간단한 설명** : 일반 텍스트를 최대 256 자까지 제공 합니다. 이 요약은 제품의 세부 정보 페이지에 표시 됩니다.
- **설명** :이 설명은 Azure Marketplace 목록 개요에 표시 됩니다. 가치 제안, 주요 이점, 의도 된 사용자 기반, 범주 또는 업계 연결, 앱 내 구매 기회, 고객이 요구 하거나 제품에 대 한 요구 사항, 필요한 공개 및 자세한 내용을 볼 수 있는 링크를 포함 하는 것이 좋습니다.

    이 텍스트 상자에는 설명이 더 유용 하 게 만드는 데 사용할 수 있는 다양 한 텍스트 편집기 컨트롤이 있습니다. HTML 태그를 사용 하 여 설명 서식을 지정할 수도 있습니다. 이 상자에는 HTML 태그와 공백을 포함 하 여 최대 3000 자의 텍스트를 입력할 수 있습니다. 추가 팁 [은 상용 marketplace 제품 설명에서 지원 되](supported-html-tags.md) [는 멋진 앱 설명](/windows/uwp/publish/write-a-great-app-description) 및 HTML 태그 작성을 참조 하세요.

- **검색 키워드** (선택 사항): 고객이 온라인 스토어에서 제품을 찾는 데 사용할 수 있는 검색 키워드를 최대 3 개까지 제공 합니다. 최상의 결과를 얻기 위해 이 키워드를 설명에도 사용합니다. 제품 **이름** 및 **설명을** 포함할 필요가 없습니다. 이 텍스트는 검색에 자동으로 포함 됩니다.
- **개인 정보 취급 방침 링크** : 회사의 개인 정보 취급 방침에 대 한 URL입니다. 유효한 개인 정보 취급 방침을 제공 해야 하며, 앱이 개인 정보 법률 및 규정을 준수 하는지 확인 해야 합니다.
- **유용한 링크** (선택 사항): 제품의 사용자에 대 한 다양 한 리소스에 대 한 링크를 제공할 수 있습니다. 예를 들어 포럼, Faq, 릴리스 정보 등이 있습니다.
- **연락처 정보** : 조직에서 다음 연락처를 지정 해야 합니다.
  - **지원 연락처** : 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 전자 메일을 제공 합니다. 또한 지원 웹 사이트에 대 한 URL을 포함 해야 합니다.
  - **엔지니어링 연락처** : 제품에 문제가 있는 경우 직접 사용할 수 있는 이름, 전화 번호 및 전자 메일을 Microsoft에 제공 합니다. 이 연락처 정보는 상업적 marketplace에 나열 되지 않습니다.
  - **Csp 프로그램 연락처** (선택 사항): 클라우드 솔루션 공급자 (CSP) 프로그램을 옵트인 (opt in) 하는 경우 이름, 전화 번호 및 전자 메일을 제공 합니다. 그러면 이러한 파트너가 질문에 대해 연락할 수 있습니다. 마케팅 자료에 대 한 URL을 포함할 수도 있습니다.
- **미디어 – 로고** : **큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작고** **중간** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로 바꿀 수 있습니다.
  - 큼 (216 x 216에서 350 x 350 px, required)
  - 중간 (90 x 90 px, 선택 사항)
  - 작음 (48 x 48 px, 선택 사항)

  이러한 로고는 온라인 상점에서 다른 위치에 사용 됩니다.
  - Azure Marketplace 검색 결과에 작은 로고가 표시 됩니다.
  - Microsoft Azure에서 새 리소스를 만들 때 Medium 로고가 표시 됩니다.
  - Azure Marketplace의 제품 목록 페이지에 많은 로고가 표시 됩니다.

  로고에 대해서는 다음 지침을 따르세요.

  - Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수를 제한합니다.
  - 포털의 테마 색은 흰색과 검은색입니다. 로고의 배경색으로 이러한 색을 사용하지 마십시오. 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다.
  - 투명한 배경을 사용하는 경우 로고 및 텍스트는 흰색, 검은색 또는 파란색이 아니어야 합니다.
  - 로고의 모양과 느낌은 평평 하 고 로고 또는 배경의 그라데이션을 사용 하지 않아야 합니다. 로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 마십시오. 이미지가 흐리면 제출이 거부됩니다.
  - 로고가 늘어나지 않았는지 확인합니다.

- **미디어 스크린샷** (선택 사항): 제품의 작동 방식을 보여 주는 스크린샷 추가 하는 것이 좋습니다. 다음 요구 사항을 충족 하는 최대 5 개의 스크린샷을 추가할 수 있으며,이는 제품의 작동 방식을 보여 줍니다.
  - 1280 x 720 픽셀
  - .png 파일
  - 캡션을 포함 해야 합니다.
- **미디어 – 비디오** (선택 사항): 제품을 설명 하는 다음과 같은 요구 사항을 사용 하 여 최대 5 개의 비디오를 추가할 수 있습니다.
  - Name
  - URL: YouTube 또는 Vimeo에만 호스팅되어야 합니다.
  - 축소판 그림: 1280 x 720 .png 파일

> [!NOTE]
> 제품은 상용 marketplace에 게시 되는 일반 [상용 marketplace 인증 정책을](/legal/marketplace/certification-policies#100-general.md) 충족 해야 합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

미리 보기 대상 사용자는 온라인 상점에 게시 하기 전에 제품에 액세스 하 여 종단 간 기능을 실시간으로 게시할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 개인 계획과 다릅니다. 비공개 요금제는 사용자가 선택 하는 특정 대상에만 사용할 수 있도록 하는 계획입니다. 이렇게 하면 사용자 지정 요금제를 특정 고객과 협상할 수 있습니다.

Azure 구독 Id를 사용 하 여 미리 보기 대상 그룹을 정의 하 고 각각에 대 한 선택적 설명을 정의 합니다. 해당 필드는 고객에게 표시되지 않습니다.

## <a name="technical-configuration"></a>기술 구성

[Marketplace 계량 서비스 api](partner-center-portal/marketplace-metering-service-apis.md)를 사용 하 여 계량 이벤트를 내보내는 관리 되는 응용 프로그램의 경우 계량 이벤트를 내보낼 때 서비스에서 사용할 id를 제공 해야 합니다.

이 구성은 [일괄 처리 사용량 이벤트](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event)를 사용하려는 경우에 필요합니다. [사용 이벤트](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)를 제출 하려는 경우 [인스턴스 메타 데이터 서비스](/azure/active-directory/managed-identities-azure-resources/overview) 를 사용 하 여 [JWT (JSON web token) 전달자 토큰](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)을 가져올 수도 있습니다.

- **Azure Active Directory 테 넌 트 ID** (필수): Azure Portal 내에서 두 서비스 간의 연결이 인증 된 통신 뒤에 있는지 확인할 수 있도록 [AD (Azure Active Directory) 앱을 만들어야](/azure/active-directory/develop/howto-create-service-principal-portal) 합니다. Azure Active Directory (Azure AD) 앱에 대 한 [테 넌 트 ID](/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-and-app-id-values-for-signing-in) 를 찾으려면 Azure Active Directory의 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드에 있습니다. **표시 이름** 열에서 앱을 선택 합니다. 그런 다음 **속성** 을 찾아 **디렉터리 (테 넌 트) ID** (예:)를 찾습니다 `50c464d3-4930-494c-963c-1e951d15360e` .
- **Azure Active Directory 응용 프로그램 id** (필수): [응용 프로그램 id](/azure/active-directory/develop/howto-create-service-principal-portal#get-tenant-and-app-id-values-for-signing-in.md) 및 인증 키도 필요 합니다. 응용 프로그램 ID를 찾으려면 Azure Active Directory [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 블레이드로 이동 합니다. **표시 이름** 열에서 앱을 선택 하 고 **응용 프로그램 (클라이언트) ID** (예:)를 찾습니다 `50c464d3-4930-494c-963c-1e951d15360e` . 인증 키를 찾으려면 **설정** 으로 이동하고 **키** 를 선택합니다. 설명 및 기간을 입력해야 숫자 값이 제공됩니다.

> [!NOTE]
> Azure 응용 프로그램 ID는 게시자 ID와 연결 되며이 게시자 계정 내 에서만 다시 사용할 수 있습니다.

## <a name="additional-sales-opportunities"></a>추가 판매 기회

Microsoft에서 지 원하는 마케팅 및 판매 채널을 옵트인 (opt in) 하도록 선택할 수 있습니다. 파트너 센터에서 제품을 만들 때 프로세스의 끝에 두 개의 탭이 표시 됩니다.

- **Csp를 통한 재판매** :이 옵션을 사용 하면 csp (Microsoft 클라우드 솔루션 공급자) 파트너가 번들 제안의 일부로 솔루션을 재판매 수 있습니다. 자세한 내용은 [클라우드 솔루션 공급자 프로그램](/azure/marketplace/cloud-solution-providers) 을 참조 하세요.
- **Microsoft와 공동 판매** :이 옵션을 사용 하면 microsoft 영업 팀에서 고객의 요구를 평가할 때 IP 공동 판매에 적합 한 솔루션을 고려할 수 있습니다. 평가를 위해 제품을 준비 하는 방법에 대 한 자세한 내용은 [파트너 센터의 공동 판매 옵션](partner-center-portal/commercial-marketplace-co-sell.md) 을 참조 하세요. Microsoft CSP 파트너 채널을 통한 제품 마케팅에 대 한 자세한 내용은 [클라우드 솔루션 공급자](cloud-solution-providers.md)를 참조 하세요.

자세히 알아보려면 [Azure Marketplace를 사용 하 여 클라우드 비즈니스 성장](https://azuremarketplace.microsoft.com/sell)을 참조 하세요.

## <a name="plans"></a>계획

Azure 애플리케이션 제품에는 하나 이상의 계획이 필요 합니다. 계획은 솔루션 범위와 제한 및 관련 된 가격 책정 (해당 하는 경우)을 정의 합니다. 제품에 대 한 여러 요금제를 만들어 고객에 게 다른 기술 및 가격 옵션을 제공할 수 있습니다.

가격 책정 모델 및 비공개 요금제를 비롯 한 계획에 대 한 일반적인 지침은 [상용 marketplace 제품에 대 한 계획 및 가격 책정](plans-pricing.md)을 참조 하세요. 다음 섹션에서는 Azure 애플리케이션 계획과 관련 된 추가 정보에 대해 설명 합니다.

### <a name="types-of-plans"></a>계획 유형

Azure 응용 프로그램 계획에는 _솔루션 템플릿_ 및 _관리 되는 응용_ 프로그램의 두 가지 종류가 있습니다. 두 계획 유형 모두 단일 VM (가상 머신) 이외의 솔루션 배포 및 구성을 자동화 하는 기능을 지원 합니다. Vm, 네트워킹 및 저장소 리소스를 비롯 한 여러 리소스를 제공 하 여 IaaS 솔루션과 같은 복잡 한 솔루션을 제공 하는 프로세스를 자동화할 수 있습니다. 두 계획 유형 모두 Vm에 대 한 제한 없이 여러 종류의 Azure 리소스를 사용할 수 있습니다.

- **솔루션 템플릿** 계획은 상용 marketplace에 솔루션을 게시 하는 주요 방법 중 하나입니다. 솔루션 템플릿 요금제는 상업적 marketplace에서 불가능 되지 않지만 상업적 marketplace를 통해 청구 되는 유료 VM 제품을 배포 하는 데 사용할 수 있습니다. 고객이 솔루션을 관리 하 고 트랜잭션이 다른 계획을 통해 청구 되는 경우 솔루션 템플릿 계획 유형을 사용 합니다. 솔루션 템플릿을 빌드하는 방법에 대 한 자세한 내용은 [Azure Resource Manager?](/azure/azure-resource-manager/resource-group-overview) 을 참조 하세요.
- **관리 되는 응용 프로그램** 계획을 통해 고객에 게 완전히 관리 되는 턴키 응용 프로그램을 쉽게 빌드하고 제공할 수 있습니다. 몇 가지 주요 차이점을 포함 하 여 솔루션 템플릿 계획과 동일한 기능을 제공 합니다.
    - 리소스는 리소스 그룹에 배포 되 고 앱 게시자가 관리 합니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다. 
    - 게시자로 서 솔루션의 지속적인 지원 비용을 지정 하 고 상업적 marketplace를 통해 트랜잭션을 지원 합니다.
 
    사용자 또는 고객이 솔루션을 파트너에서 관리 하거나 구독 기반 솔루션을 배포 해야 하는 경우 관리 되는 응용 프로그램 계획 유형을 사용 합니다. 관리 되는 응용 프로그램의 장점 및 유형에 대 한 자세한 내용은 [Azure 관리 되는 응용 프로그램 개요](/azure/managed-applications/overview)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 솔루션 템플릿을 계획 하려면 [Azure 응용 프로그램 제품에 대 한 솔루션 템플릿 계획](plan-azure-app-solution-template.md)을 참조 하세요.
- Azure 관리 되는 응용 프로그램을 계획 하려면 azure [응용 프로그램 제품에 대 한 azure 관리 응용 프로그램 계획](plan-azure-app-managed-app.md)을 참조 하세요.
