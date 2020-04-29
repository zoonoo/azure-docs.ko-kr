---
title: 파트너 센터를 사용 하 여 Azure IoT Edge 모듈 제품 만들기-Azure Marketplace
description: 파트너 센터를 사용 하 여 Azure Marketplace에서 IoT Edge 모듈 제품을 만드는 방법을 알아봅니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: cca54e4e456fe766b190f64657cd1aca1d9520e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869139"
---
# <a name="create-an-iot-edge-module-offer"></a>IoT Edge 모듈 제품 만들기

> [!IMPORTANT]
> IoT Edge 모듈 제품의 관리를 Cloud 파트너 포털에서 파트너 센터로 전환 하 고 있습니다. 제품을 마이그레이션할 때까지 Cloud 파트너 포털에 대 한 [IoT Edge 모듈 제공 게시 개요](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) 의 지침에 따라 제품을 관리 하세요.

이 문서에서는 Azure Marketplace에 대 한 IoT (사물 인터넷) Edge 모듈 제품을 만들고 게시 하는 방법을 설명 합니다.

IoT Edge module 제품을 만들려면 먼저 파트너 센터에 상용 marketplace 계정이 있어야 합니다. 아직 만들지 않은 경우 [파트너 센터에서 상용 마켓플레이스 계정 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)를 참조 하세요.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. 파트너 센터에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택 합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/cs-menu-overview.png)

3. **+ 새 제품** > **IoT Edge 모듈**을 선택 합니다. **새 제품** 대화 상자가 나타납니다.

> [!IMPORTANT]
> 제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 상점에 표시 됩니다. 변경을 수행한 후 항상 다시 게시 해야 합니다.

### <a name="offer-id-and-alias"></a>제품 ID 및 별칭

**제품 ID**를 입력 합니다. 계정의 각 제품에 대 한 고유 식별자입니다.

- 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿에 대 한 웹 주소의 고객에 게 표시 됩니다 (해당 하는 경우).
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50 자로 제한 됩니다. 예를 들어 **테스트-제품-1**을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`입니다.
- 만들기를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력 합니다. 파트너 센터에서 제품을 참조 하는 데 사용 되는 이름입니다.

- 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다.
- **만들기**를 선택한 후에는 변경할 수 없습니다.

이러한 두 값을 입력 한 후에는 다음 페이지인 개요를 제공 하기 전에 **만들기** 를 선택 합니다.

## <a name="offer-overview"></a>제품 개요

**제품 개요** 페이지에는이 제품을 게시 하는 데 필요한 단계를 시각적으로 표시 하 고 (완료 된 시간과 예정 된) 각 단계를 완료 하는 데 소요 되는 시간이 표시 됩니다.

이 페이지에는 사용자가 선택한 항목에 따라이 제품에 대 한 작업을 수행할 수 있는 링크가 포함 되어 있습니다. 다음은 그 예입니다.

- 제안이 초안 [삭제 제안](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) 인 경우
- 제품이 라이브 인 경우 [제품 판매를 중지](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) 합니다.
- 제품이 미리 보기 상태 이면 [라이브로 전환](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval) 합니다.
- 게시자 로그 아웃을 완료 하지 않은 경우 [게시를 취소 합니다.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>제품 설정

제품을 설정 하려면 다음 단계를 수행 합니다.

### <a name="connect-lead-management"></a>리드 관리 연결

파트너 센터를 사용 하 여 marketplace에 제품을 게시 하는 경우 필요에 따라 CRM (고객 관계 관리) 시스템에 연결할 수 있습니다. 이를 통해 사용자가에 관심을 표시 하거나 제품을 사용 하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. 잠재 고객을 보내려는 잠재 고객 대상을 선택합니다. 파트너 센터는 다음과 같은 CRM 시스템을 지원 합니다.

    - 고객 Engagement의 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > CRM 시스템이 위에 나열 되지 않은 경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https 끝점](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 을 사용 하 여 고객 리드 데이터를 저장 한 다음 crm 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시할 때 제품을 잠재 고객 대상에 연결 합니다.
3. 잠재 고객 대상에 대 한 연결이 제대로 구성 되어 있는지 확인 합니다. 파트너 센터에서 게시 한 후에는 연결의 유효성을 검사 하 고 테스트 리드를 전송 합니다. 제품을 라이브 상태로 전환 하는 동안 미리 보기 환경에서 제품을 직접 구매 하 여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객이 손실 되지 않도록 잠재 고객 대상에 대 한 연결이 업데이트 된 상태로 유지 되는지 확인 합니다.

몇 가지 추가 리드 관리 리소스는 다음과 같습니다.

- [리드 관리 개요](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [잠재 고객 관리 FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [리드 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (팝업 차단이 해제 되어 있는지 확인).

다음 섹션인 속성을 계속 진행 하기 전에 **초안 저장** 을 선택 합니다.

### <a name="properties"></a>속성

이 페이지에서는 marketplace에서 제품을 그룹화 하는 데 사용 되는 범주와 제품을 지 원하는 법적 계약을 정의할 수 있습니다.

#### <a name="category"></a>범주

최소 1 자에서 최대 5 개의 범주를 선택 합니다. 이러한 범주는 제품을 적절 한 마켓플레이스 검색 영역에 저장 하는 데 사용 되며 제품 세부 정보 페이지에 표시 됩니다. 제품 설명에서 제품이 이러한 범주를 어떻게 지원 하는지 설명 합니다. 찾아보기 페이지에서 모든 IoT Edge 모듈은 **사물 인터넷 > IoT Edge 모듈** 범주에 표시 됩니다.

#### <a name="legal"></a>법적 정보

제품에 대 한 사용 약관을 제공 해야 합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- Microsoft 상업적 Marketplace에 대 한 표준 계약을 사용 합니다.
- 사용자 고유의 사용 약관을 제공 합니다.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 Marketplace에 대 한 표준 계약

상업적 marketplace에서 트랜잭션을 용이 하 게 하는 데 도움이 되는 표준 계약 템플릿을 제공 합니다. 사용자가 한 번만 확인 하 고 수락 해야 하는 표준 계약에 따라 솔루션을 제공 하도록 선택할 수 있습니다. 사용자 지정 사용 약관을 만들지 않으려는 경우이 옵션을 사용 하는 것이 좋습니다.

표준 계약에 대 한 자세한 내용은 [Microsoft 상업적 Marketplace에 대 한 표준 계약](https://docs.microsoft.com/azure/marketplace/standard-contract)을 참조 하세요. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) PDF를 다운로드할 수도 있습니다 (팝업 차단이 해제 되어 있는지 확인).

표준 계약을 사용 하려면 **Microsoft의 상용 marketplace에 대 한 표준 계약 사용** 확인란을 선택 하 고 **동의**를 클릭 합니다.

> [!NOTE]
> Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 한 후에는 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. 표준 계약 또는 자신의 사용 약관에 따라 솔루션을 제공 합니다.

![Microsoft의 상용 marketplace에 대 한 표준 계약 사용 확인란을 보여 줍니다.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>사용자 고유의 사용 약관

사용자 고유의 사용자 지정 사용 약관을 제공 하려면 사용 **약관** 상자에 해당 조건을 입력 합니다. 이 상자에는 크기에 제한이 없는 텍스트 문자만 입력할 수 있습니다. 고객은 이러한 약관에 동의 해야 제품을 사용해 볼 수 있습니다.

다음 섹션인 제품 목록으로 계속 진행 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="offer-listing"></a>제품 목록

여기에서 marketplace에 표시 되는 제품 세부 정보를 정의 합니다. 여기에는 제품 이름, 설명, 이미지 등이 포함 됩니다. 이 제품을 구성 하는 동안 Microsoft의 정책 페이지에 자세히 설명 된 정책을 따라야 합니다.

> [!NOTE]
> 제품 설명이 "영어가 아닌 언어 에서만 사용할 수 있습니다."로 시작 하는 경우 제품 세부 정보는 영어로 지정할 필요가 없습니다. 또한 제품 목록 세부 정보에 사용 된 것과 다른 언어로 콘텐츠를 제공 하는 유용한 링크도 제공 합니다.

### <a name="name"></a>속성

여기에 입력 하는 이름은 제품의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 상자에 입력 한 텍스트로 미리 채워집니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다.
- 50 자를 초과할 수 없습니다.
- Emojis를 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

제품에 대 한 간단한 설명을 제공 합니다. 이 길이는 최대 100 자까지 가능 하며 marketplace 검색 결과에 사용 됩니다.

### <a name="long-summary"></a>긴 요약

제품에 대 한 자세한 설명을 제공 합니다. 이 길이는 최대 256 자까지 가능 하며 marketplace 검색 결과에 사용 됩니다.

### <a name="description"></a>Description

제품에 대 한 더 긴 설명을 최대 3000 자까지 제공 합니다. 이는 marketplace 목록 개요에서 고객에 게 표시 됩니다.

설명에 다음 중 하나 이상을 포함 합니다.

- 제품에서 제공 하는 가치 및 주요 이점
- 범주 또는 업계 연결 또는 둘 다
- 앱 내 구매 기회
- 필요한 모든 공개

IoT Edge 모듈 제공에는 설명의 맨 아래에 최소 하드웨어 요구 사항 단락이 포함 되어 있어야 합니다. 다음은 그 예입니다.

*최소 하드웨어 요구 사항: Linux x64 및 arm32 OS, 1gb RAM, 500 Mb의 저장소*

설명 작성에 대 한 몇 가지 팁은 다음과 같습니다.

- 설명의 처음 몇 문장에서 제품의 가치를 명확 하 게 설명 합니다. 다음 항목을 포함 합니다.
    - 제품에 대한 설명입니다.
    - 제품을 활용 하는 사용자의 유형입니다.
    - 고객이 요구 하거나 제품 주소를 발급 합니다.
- 검색 결과에는 처음 몇 개의 문장이 표시 될 수 있습니다.
- 제품을 판매 하기 위한 기능과 기능을 사용 하지 마세요. 대신 제품에서 제공 하는 가치에 집중 하세요.
- 산업별 어휘 또는 혜택 기반 표현을 사용해 보세요.

제품 **설명을** 보다 유용 하 게 사용 하려면 서식 있는 텍스트 편집기를 사용 하 여 설명의 서식을 지정 합니다. 서식 있는 텍스트 편집기를 사용 하면 숫자, 글머리 기호, 굵게, 기울임꼴 및 들여쓰기를 추가 하 여 설명을 더 쉽게 읽을 수 있습니다.

:::image type="content" source="media/text-editor2.png" alt-text="서식 있는 텍스트 편집기를 보여 줍니다." border="false":::

- 콘텐츠 형식을 변경 하려면 다음 스크린샷에 표시 된 것 처럼 서식을 지정할 텍스트를 강조 표시 하 고 텍스트 스타일을 선택 합니다.

     :::image type="content" source="media/text-editor3.png" alt-text="서식 있는 텍스트 편집기의 텍스트 스타일 컨트롤을 보여 줍니다." border="false":::

- 텍스트에 글머리 기호 또는 번호 매기기 목록을 추가 하려면 다음 스크린샷에 표시 된 옵션을 사용 합니다.
  
    :::image type="content" source="media/text-editor4.png" alt-text="서식 있는 텍스트 편집기의 글머리 기호 및 숫자 목록 컨트롤을 보여 줍니다." border="false":::

- 텍스트에 들여쓰기를 추가 하거나 제거 하려면 다음 스크린샷에 표시 된 옵션을 사용 합니다.

    :::image type="content" source="media/text-editor5.png" alt-text="서식 있는 텍스트 편집기의 들여쓰기 컨트롤을 보여 줍니다." border="false":::

#### <a name="privacy-policy-url"></a>개인 정보 취급 방침 URL

조직의 개인 정보 보호 정책에 대 한 웹 주소를 입력 합니다. 귀하는 귀하의 제품이 개인 정보 법률 및 규정을 준수 하는지 확인 해야 합니다. 웹 사이트에 유효한 개인 정보 취급 방침을 게시 하는 작업도 담당 합니다.

#### <a name="useful-links"></a>유용한 링크

제품에 대 한 추가 온라인 문서를 제공 합니다. 최대 25 개의 링크를 추가할 수 있습니다. 링크를 추가 하려면 **+ 링크 추가** 를 선택 하 고 다음 필드를 완료 합니다.

- **제목** -고객은 제품의 세부 정보 페이지에 제목을 표시 합니다.
- **링크 (URL)** -고객의 온라인 문서를 볼 수 있는 링크를 입력 합니다. 이 링크는 http://또는 https://로 시작 해야 합니다.

설명서에 대 한 링크를 하나 이상 추가 하 고 [Azure IoT 장치 카탈로그](https://catalog.azureiotsolutions.com/)에서 호환 되는 IoT Edge 장치에 대 한 링크를 하나 이상 추가 해야 합니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처** 와 **엔지니어링 연락처** 의 이름, 전자 메일 및 전화 번호를 제공 해야 합니다. 이 정보는 고객에 게 표시 되지 않습니다. Microsoft에서 사용할 수 있으며, CSP (클라우드 솔루션 공급자) 파트너에 게 제공 될 수 있습니다.

- 지원 담당자 (필수): 일반 지원 질문의 경우
- 엔지니어링 연락처 (필수): 기술 관련 질문 및 인증 문제.
- CSP 프로그램 연락처 (선택 사항): CSP 프로그램과 관련 된 재판매인 질문의 경우

**지원 연락처** 섹션에서 파트너가 글로벌 Azure, Azure Government 또는 둘 다에서 제품을 사용할 수 있는지 여부에 따라 제품에 대 한 지원을 찾을 수 있는 **지원 웹 사이트** 의 웹 주소를 제공 합니다.

Csp **프로그램 연락처** 섹션에서 csp 파트너가 제품에 대 한 마케팅 자료를 찾을 수 있는 링크 (**Csp 프로그램 마케팅 자료**)를 제공 합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열 하는 추가 마켓플레이스

제품 목록을 만드는 방법에 대 한 자세한 내용은 [제품 목록 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)를 참조 하세요.

### <a name="marketplace-images"></a>Marketplace 이미지

제품에 사용할 로고 및 이미지를 제공 합니다. 모든 이미지는 .png 형식 이어야 합니다. 흐린 이미지는 거부 됩니다.

>[!Note]
>파일을 업로드 하는 데 문제가 있는 경우 로컬 네트워크가 파트너 센터에서 사용 하는 https://upload.xboxlive.com 서비스를 차단 하지 않는지 확인 합니다.

#### <a name="store-logos"></a>매장 로고

다음 네 픽셀 크기 각각에 제품 로고의 .png 파일을 제공 합니다.

- **작음 (48 x 48)**
- **보통 (90 x 90)**
- **큼 (216 x 216)**
- **넓게 (255 x 115)**

4 개의 로고가 모두 필요 하며 marketplace 목록의 다른 위치에서 사용 됩니다.

#### <a name="screenshots-optional"></a>스크린샷 (선택 사항)

제품의 작동 방식을 보여 주는 최대 5 개의 스크린샷을 추가 합니다. 각각 1280 x 720 픽셀 크기와 .png 형식 이어야 합니다.

#### <a name="videos-optional"></a>비디오 (선택 사항)

제품을 설명 하는 비디오를 최대 5 개까지 추가 합니다. 비디오의 이름, 웹 주소 및 비디오의 미리 보기 .png 이미지를 1280 x 720 픽셀 크기로 입력 합니다.

#### <a name="offer-examples"></a>제안 예제

다음 예에서는 제품 목록 필드가 제품의 다른 위치에 표시 되는 방법을 보여 줍니다.

이 스크린샷은 Azure Marketplace의 **제품 목록** 페이지를 보여 줍니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Azure Marketplace의 제품 목록 페이지를 보여 줍니다.":::

이 스크린샷은 Azure Marketplace의 검색 결과를 보여 줍니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Azure Marketplace의 검색 결과를 보여 줍니다.":::

이 스크린샷은 Azure Portal의 **제품 목록** 페이지를 보여 줍니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Azure Portal의 제품 목록 페이지를 보여 줍니다.":::

이 스크린샷은 Azure Portal의 검색 결과를 보여 줍니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Azure Portal의 제품 목록 페이지를 보여 줍니다.":::

다음 섹션인 Preview로 진행 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="preview"></a>미리 보기

**미리 보기 탭**에서 제품의 유효성을 검사 하기 위해 제한 된 **미리 보기 대상 그룹** 을 선택 하 여 더 광범위 한 marketplace 대상에 게시할 수 있습니다.

> [!IMPORTANT]
> 미리 보기에서 제품을 본 후에는 live로 **전환** 을 선택 하 여 제안을 공개로 게시 해야 합니다.

Azure 구독 ID Guid를 사용 하 여 미리 보기 대상 그룹을 지정 하 고 각각에 대 한 선택적 설명을 지정 합니다. 이러한 필드는 고객에 게 표시 되지 않습니다.

> [!NOTE]
> Azure Portal의 구독 페이지에서 Azure 구독 ID를 찾을 수 있습니다.

하나 이상의 Azure 구독 ID를 개별적으로 (최대 10 개) 또는 CSV 파일을 업로드 하 여 추가 합니다 (최대 100). 이러한 구독 Id를 추가 하 여 live 게시 하기 전에 제품을 미리 볼 수 있는 사용자를 정의 합니다. 제품이 이미 라이브 상태인 경우에는 제품에 대 한 변경 내용 또는 업데이트를 테스트 하는 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 사용자는 개인 대상과 다릅니다. **미리 보기** 대상 그룹 **은 사용자가** marketplace에 게시 하기 전에 모든 제품을 보고 확인할 수 있습니다 (가용성 탭에서 설정).

다음 섹션인 계획 개요로 진행 하기 전에 **초안 저장** 을 선택 합니다.

### <a name="plan-overview"></a>계획 개요

이 탭에서 파트너 센터의 동일한 제품 내에서 다른 계획 옵션을 제공할 수 있습니다. 이러한 계획은 이전에 Sku 또는 재고 유지 단위 라고 합니다. 요금제는 글로벌 클라우드, 정부 클라우드 및 계획에서 참조 하는 이미지와 같은 사용 가능한 클라우드 측면에서 다를 수 있습니다. Marketplace에서 제품을 나열 하려면 계획을 하나 이상 설정 해야 합니다.

계획을 만든 후 **계획 개요** 탭에 다음이 표시 됩니다.

- 계획 이름
- 가격 책정 모델
- 클라우드 가용성 (글로벌 또는 정부)
- 현재 게시 상태
- 사용 가능한 모든 작업

계획 개요에서 사용할 수 있는 작업은 계획의 현재 상태에 따라 달라 집니다. 해당 기능은 아래와 같습니다.

- **초안 삭제**: 계획 상태가 초안 인 경우입니다.
- **판매 계획 중지**: 계획 상태가 실시간으로 게시 된 경우입니다.

#### <a name="create-new-plan"></a>새 계획 만들기

**새 계획 만들기**를 선택 합니다. **새 계획** 대화 상자가 나타납니다.

**계획 id** 상자에서이 제안의 각 계획에 대 한 고유한 계획 id를 만듭니다. 이 ID는 제품 웹 주소에 있는 고객에 게 표시 됩니다. 소문자와 숫자, 대시 또는 밑줄만 사용할 수 있으며 최대 50 자만 사용할 수 있습니다.

**계획 이름** 상자에이 계획의 이름을 입력 합니다. 제품 내에서 선택할 계획을 결정할 때 고객에 게이 이름이 표시 됩니다. 이 제안의 각 계획에 대 한 고유한 이름을 만듭니다. 예를 들어 windows server **2016** 및 **windows server 2019**계획을 포함 하는 **windows server** 의 제품 이름을 사용할 수 있습니다.

> [!NOTE]
> **만들기**를 선택한 후에는 계획 ID를 변경할 수 없습니다.

**만들기**를 선택합니다.

### <a name="plan-setup"></a>설정 계획

이 탭에서 계획을 사용할 수 있는 클라우드를 구성할 수 있습니다. 이 탭에 대 한 답변은 다른 탭에 표시 되는 필드에 영향을 줍니다.

#### <a name="cloud-availability"></a>클라우드 가용성

Azure IoT Hub를 사용 하 여 하나 이상의 클라우드에서 요금제를 사용할 수 있어야 합니다.

Marketplace를 사용 하는 모든 글로벌 Azure 지역에서 고객이 요금제를 사용할 수 있도록 **Azure Global** 옵션을 선택 합니다. 자세한 내용은 [지리적 가용성 및 통화 지원](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)을 참조 하세요.

[Azure Government 클라우드](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) 옵션을 선택 하 여 솔루션을 여기에 표시 합니다. 이는 미국 연방, 주, 지방 또는 부족 정부 기관의 고객에 대 한 액세스를 제어 하는 정부 커뮤니티 클라우드로,이를 제공할 수 있는 파트너도 있습니다. 게시자는이 클라우드 커뮤니티에 대 한 규정 준수 제어, 보안 조치 및 모범 사례를 담당 하 고 있습니다. Azure Government는 미국에만 있는 물리적으로 격리 된 데이터 센터와 네트워크를 사용 합니다. Azure Government에 [게시](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) 하기 전에 결과와 다를 수 있으므로 해당 영역 내에서 솔루션을 테스트 하 고 확인 합니다. 솔루션을 준비 하 고 테스트 하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청 합니다.

> [!NOTE]
> 계획을 게시 하 고 특정 클라우드에서 사용할 수 있게 되 면 해당 클라우드를 제거할 수 없습니다.

#### <a name="azure-government-cloud-certifications"></a>Azure Government 클라우드 인증

이 옵션은 클라우드 **가용성**에서 **Azure Government 클라우드** 를 선택한 경우에만 표시 됩니다.

Azure Government services는 특정 정부 규정 및 요구 사항이 적용 되는 데이터를 처리 합니다. 예를 들어 FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 이러한 프로그램에 대 한 인증을 인식 하려면 인증을 설명 하는 최대 100 링크를 제공할 수 있습니다. 이러한 링크는 프로그램의 목록에 직접 또는 자신의 웹 사이트에 연결할 수 있습니다. 이러한 링크는 Azure Government 고객만 볼 수 있습니다.

## <a name="plan-listing"></a>계획 목록

이 탭에는 동일한 제품 내의 서로 다른 각 계획에 대 한 특정 정보가 표시 됩니다.

### <a name="plan-name"></a>계획 이름

이는 계획을 만들 때 지정한 이름으로 미리 채워집니다. 필요에 따라이 이름을 변경할 수 있습니다. 최대 50 자까지 입력할 수 있습니다. 이 이름은 Azure Marketplace 및 Azure Portal에서이 계획의 제목으로 표시 됩니다. 계획을 사용할 준비가 되 면 기본 모듈 이름으로 사용 됩니다.

### <a name="plan-summary"></a>계획 요약

요금제에 대 한 간략 한 요약을 제공 합니다 (제안 아님). 이 요약은 Azure Marketplace 검색 결과에 표시 되며 최대 100 자까지 포함할 수 있습니다.

### <a name="plan-description"></a>계획 설명

이 계획을 고유 하 게 만드는 방법 및 제품 내 계획 간의 차이점을 설명 합니다. 제품을 설명 하지 않고 요금제만 제공 합니다. 이 설명은 Azure Marketplace 및 제품 목록 페이지의 Azure Portal에 표시 됩니다. 계획 요약에 입력 한 것과 동일한 콘텐츠 이며 최대 2000 자까지 포함할 수 있습니다.

이러한 필드를 완료 한 후 **초안 저장** 을 선택 합니다.

#### <a name="plan-examples"></a>계획 예제

다음 예에서는 계획 나열 필드가 서로 다른 뷰에 표시 되는 방법을 보여 줍니다.

계획 세부 정보를 볼 때 Azure Marketplace의 필드는 다음과 같습니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Azure Marketplace에서 계획 세부 정보를 볼 때 표시 되는 필드를 보여 줍니다.":::

Azure Portal에 대 한 계획 정보는 다음과 같습니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Azure Portal에 대 한 계획 정보를 보여 줍니다.":::

## <a name="availability"></a>가용성

고객이 marketplace에서 검색 하거나 찾아보거나 구매할 수 없도록 게시 된 제품을 숨기려면 가용성 탭에서 **계획 숨기기** 확인란을 선택 합니다.

이 필드는 다음과 같은 경우에 일반적으로 사용 됩니다.

- 이 제품은 다른 응용 프로그램에서 참조 하는 경우에만 간접적으로 사용할 수 있습니다.
- 제품은 개별적으로 구매 해서는 안 됩니다.
- 이 계획은 초기 테스트에 사용 되었으며 더 이상 관련이 없습니다.
- 임시 또는 계절 제안에 요금제를 사용 했으며 더 이상 제공 되지 않습니다.

## <a name="technical-configuration"></a>기술 구성

**IoT Edge 모듈** 제공 형식은 IoT Edge 장치에서 실행 되는 컨테이너의 특정 유형입니다. **기술 구성** 탭에서는 고객이 모듈을 쉽게 사용할 수 있도록 하는 구성 설정과 함께 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)내의 컨테이너 이미지 리포지토리에 대 한 참조 정보를 제공 합니다.

제품이 게시 된 후에는 IoT Edge 컨테이너 이미지가 특정 공용 컨테이너 레지스트리에서 Azure Marketplace 복사 됩니다. Azure 사용자의 모듈 사용에 대 한 모든 요청은 개인 컨테이너 레지스트리가 아닌 Azure Marketplace 공용 컨테이너 레지스트리에서 제공 됩니다.

여러 플랫폼을 대상으로 지정 하 고 태그를 사용 하 여 여러 버전의 모듈 컨테이너 이미지를 제공할 수 있습니다. 태그 및 버전 관리에 대 한 자세한 내용은 [IoT Edge 모듈 기술 자산 준비](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-iot-edge-module-asset)를 참조 하세요.

### <a name="image-repository-details"></a>이미지 리포지토리 세부 정보

**이미지 리포지토리 세부 정보** 탭에서 다음 정보를 제공 합니다.

**이미지 원본 선택**: **Azure Container Registry** 옵션을 선택 합니다.

**Azure 구독 id**: 리소스 사용량이 보고 되 고 컨테이너 이미지를 포함 하는 Azure Container Registry에 대 한 서비스 요금이 청구 되는 구독 id를 제공 합니다. 이 ID는 Azure Portal의 [구독 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 에서 찾을 수 있습니다.

**Azure 리소스 그룹 이름**: 컨테이너 이미지와 Azure Container Registry 포함 된 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) 이름을 제공 합니다. 리소스 그룹은 구독 ID (위)에서 액세스할 수 있어야 합니다. Azure Portal의 [리소스 그룹](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) 페이지에서 이름을 찾을 수 있습니다.

**Azure container registry name**: 컨테이너 이미지를 포함 하는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) 의 이름을 제공 합니다. 컨테이너 레지스트리는 앞에서 제공한 Azure 리소스 그룹에 있어야 합니다. 전체 로그인 서버 이름이 아니라 레지스트리 이름만 제공 합니다. 이름에서 **azurecr.io** 를 생략 해야 합니다. Azure Portal의 [컨테이너 레지스트리 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) 에서 레지스트리 이름을 찾을 수 있습니다.

**Azure Container Registry에 대 한 관리자 사용자 이름**: 컨테이너 이미지를 포함 하는 Azure Container Registry 연결 된 [관리자 사용자 이름을](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) 제공 합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호가 필요 합니다. 관리자 사용자 이름 및 암호를 가져오려면 CLI (Azure 명령줄 인터페이스)를 사용 하 여 **관리 사용** 속성을 **True** 로 설정 합니다. 필요에 따라 Azure Portal에서 **사용** 하도록 **관리 사용자** 를 설정할 수 있습니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="컨테이너 레지스트리 업데이트 대화 상자를 보여 줍니다.":::

**Azure Container Registry 암호**: Azure Container Registry와 연결 되어 있고 컨테이너 이미지를 포함 하는 관리자 사용자 이름의 암호를 제공 합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호가 필요 합니다. [표시 명령을](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show) 사용 하 여 **Container Registry** > **액세스 키** 또는 Azure CLI으로 이동 하 여 Azure Portal에서 암호를 가져올 수 있습니다.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Azure Portal의 선택 키 화면을 보여 줍니다.":::

**Azure Container Registry 내의 리포지토리 이름**입니다. 이미지를 포함 하는 Azure Container Registry 리포지토리의 이름을 제공 합니다. 레지스트리에 이미지를 푸시할 때 리포지토리의 이름을 지정 합니다. [Container Registry](https://azure.microsoft.com/services/container-registry/) > 리포지토리**페이지로**이동 하 여 리포지토리의 이름을 찾을 수 있습니다. 자세한 내용은 [Azure Portal의 컨테이너 레지스트리 리포지토리 보기](https://docs.microsoft.com/azure/container-registry/container-registry-repositories)를 참조 하십시오. 이름을 설정한 후에는 변경할 수 없습니다. 계정의 각 제품에 고유한 이름을 사용 합니다.

### <a name="image-tags-for-new-versions-of-your-offer"></a>제품의 새 버전에 대 한 이미지 태그

고객은 업데이트를 게시할 때 Azure Marketplace에서 자동으로 업데이트를 받을 수 있어야 합니다. 업데이트 하지 않으려면 특정 버전의 이미지를 유지할 수 있어야 합니다. 이미지를 업데이트할 때마다 새 이미지 태그를 추가 하 여이 작업을 수행할 수 있습니다.

**이미지 태그**입니다. 이 필드는 지원 되는 모든 플랫폼에서 최신 버전의 이미지를 가리키는 **최신** 태그를 포함 해야 합니다. 또한 버전 태그를 포함 해야 합니다 (예: xx. xx. 여기서 xx는 숫자). 고객은 [매니페스트 태그](https://github.com/estesp/manifest-tool) 를 사용 하 여 여러 플랫폼을 대상으로 해야 합니다. 매니페스트 태그에서 참조하는 모든 태그는 업로드할 수 있도록 추가되어야 합니다. 모든 매니페스트 태그 (최신 태그 제외)는 X-y 또는 X. y로 시작 해야 합니다. 여기서 X, Y 및 Z는 정수입니다. 예를 들어 최신 태그가 1.0.1, 1.0.1, arm32 및 1.0.1를 가리키는 경우 이러한 6 개의 태그를이 필드에 추가 해야 합니다. 태그 및 버전 관리에 대 한 자세한 내용은 [IoT Edge 모듈 기술 자산 준비](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) 를 참조 하세요.

### <a name="default-deployment-settings-optional"></a>기본 배포 설정 (선택 사항)

IoT Edge 모듈을 배포하는 가장 일반적인 설정을 정의합니다. 이러한 기본 설정을 사용 하 여 IoT Edge 모듈을 즉시 시작할 수 있도록 하 여 고객 배포를 최적화 합니다.

**기본 경로**입니다. IoT Edge 허브는 모듈, IoT Hub 및 장치 간의 통신을 관리 합니다. 모듈 및 IoT Hub 간에 데이터 입력 및 출력에 대 한 경로를 설정할 수 있습니다 .이를 통해 메시지를 처리 하거나 추가 코드를 작성 하기 위해 추가 서비스를 요구 하지 않고 메시지를 보낼 수 있는 유연성을 제공 합니다. 경로는 이름/값 쌍을 사용 하 여 생성 됩니다. 최대 512 자까지 기본 경로 이름을 5 개까지 정의할 수 있습니다.

경로 값에 올바른 [경로 구문을](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) 사용 해야 합니다 (일반적으로 $upstream에서/message/*로 정의 됨). 즉, 모듈에서 전송 하는 모든 메시지가 IoT Hub으로 이동 합니다. 모듈을 참조 하려면 기본 모듈 이름 (공백이 나 특수 문자 없이 **제품 이름**)을 사용 합니다. 아직 알려지지 않은 다른 모듈을 참조 하려면 <FROM_MODULE_NAME> 규칙을 사용 하 여 고객에 게이 정보를 업데이트 해야 한다고 알립니다. IoT Edge 경로에 대 한 자세한 내용은 [선언 경로](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)를 참조 하세요.

예를 들어 모듈 ContosoModule에서 ContosoInput에 대 한 입력을 수신 하 고 ContosoOutput에서 데이터를 출력 하는 경우 다음과 같은 두 가지 기본 경로를 정의 하는 것이 좋습니다.

- 이름 #1: ToContosoModule
- 값 #1: FROM/messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- 이름 #2: FromContosoModuleToCloud
- 값 #2:/messages/modules/ContonsoModule/outputs/ContosoOutput에서 $upstream로

**기본 모듈 쌍 desired 속성**입니다. 모듈 쌍은 desired 속성을 비롯 하 여 모듈 인스턴스에 대 한 상태 정보를 저장 하는 IoT Hub의 JSON 문서입니다. Desired 속성은 보고 된 속성과 함께 모듈 구성 또는 조건을 동기화 하는 데 사용 됩니다. 솔루션 백 엔드는 desired 속성을 설정할 수 있으며 모듈은이를 읽을 수 있습니다. 모듈은 원하는 속성에서 변경 알림을 받을 수도 있습니다. Desired 속성은 최대 5 개의 이름/값 쌍을 사용 하 여 생성 되며 각 기본값은 512 자 미만 이어야 합니다. 최대 5 개의 이름/값 쌍 desired 속성을 정의할 수 있습니다. 쌍 desired 속성의 값은 최대 중첩 계층 4 개를 포함 하는 배열이 없는 유효한 JSON, 이스케이프 되지 않은 유효한 JSON 이어야 합니다. 기본값에 필요한 매개 변수 (예: 고객 서버의 IP 주소)가 적절 하지 않은 경우에는 매개 변수를 기본값으로 추가할 수 있습니다. 쌍 desired 속성에 대해 자세히 알아보려면 [desired 속성 정의 또는 업데이트](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)를 참조 하세요.

예를 들어 모듈에서 쌍 desired 속성을 사용 하 여 동적으로 구성할 수 있는 새로 고침 빈도를 지 원하는 경우 다음과 같은 기본 쌍 desired 속성을 정의 하는 것이 좋습니다.

- 이름 #1: RefreshRate
- 값 #1:60

**기본 환경 변수**입니다. 환경 변수는 구성 프로세스에 도움이 되는 모듈에 대 한 추가 정보를 제공 합니다. 환경 변수는 이름/값 쌍을 사용 하 여 생성 됩니다. 각 기본 환경 변수 이름 및 값은 512 자 미만 이어야 하며 최대 5 개까지 정의할 수 있습니다. 기본값에 필요한 매개 변수가 적절 하지 않은 경우 (예: 고객 서버의 IP 주소) 매개 변수를 기본값으로 추가할 수 있습니다.

예를 들어 모듈을 시작하기 전에 사용 약관에 동의해야 하는 경우 다음 환경 변수를 정의할 수 있습니다.

- 이름 #1: ACCEPT_EULA
- 값 #1: Y

**기본 컨테이너 만들기 옵션**입니다. 컨테이너 생성 옵션은 IoT Edge 모듈 Docker 컨테이너를 만들도록 지시 합니다. IoT Edge는 Docker 엔진 API 만들기 컨테이너 옵션을 지원 합니다. 컨테이너 목록에서 모든 옵션을 확인 [합니다.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) 만들기 옵션 필드는 유효한 JSON, 이스케이프 되지 않음 및 512 자 미만 이어야 합니다.

예를 들어 모듈에 포트 바인딩이 필요한 경우 다음 만들기 옵션을 정의 합니다.

"HostConfig": {"PortBindings": {"5012/tcp": [{"Hostconfig": "5012"}]}

## <a name="review-and-publish"></a>검토 및 게시

제품의 필수 섹션을 모두 완료 한 후에 제출 하 여 검토 하 고 게시할 수 있습니다.

포털의 오른쪽 위 모서리에서 **검토 및 게시**를 선택 합니다.

검토 페이지에서 게시 상태를 확인할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다. 제품의 모든 섹션이 완료로 표시 될 때까지 게시할 수 없습니다.
    - **시작 되지 않음** -섹션이 시작 되지 않았으며 완료 해야 합니다.
    - **불완전** -섹션에 수정 해야 하는 오류가 있거나 추가 정보를 제공 해야 합니다. 지침은이 문서의 앞부분에 나오는 섹션을 참조 하세요.
    - **완료** -섹션에 필요한 모든 데이터가 있고 오류가 없습니다. 제품을 제출 하려면 먼저 제품의 모든 섹션을 완료 해야 합니다.
- 제품이 올바르게 테스트 되었는지 확인 하기 위해 인증 팀에 테스트 지침을 제공 합니다. 또한 제품을 이해 하는 데 유용한 보충 정보를 제공 합니다.

게시를 위해 제품을 제출 하려면 **게시**를 선택 합니다.

제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 시기를 알려 주는 전자 메일을 보내 드리겠습니다. 공개에 제품을 게시 하려면 (또는 개인 제품이 개인 사용자에 게 제공 하는 경우) 파트너 센터로 이동 하 여 [ **라이브]** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [상업적 marketplace에서 기존 제품 업데이트](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)