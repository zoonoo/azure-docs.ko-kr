---
title: Azure Marketplace에서 Azure virtual machine 제품을 만듭니다.
description: 필요한 SKU를 사용 하 여 Azure Marketplace에서 가상 컴퓨터 제품을 만드는 방법을 알아봅니다.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 31b8960f5617566a72545510cf03771f7a3bfcbd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82745018"
---
# <a name="create-an-azure-virtual-machine-offer-in-the-azure-marketplace"></a>Azure Marketplace에서 Azure virtual machine 제품을 만듭니다.

> [!IMPORTANT]
> Azure VM 제품의 관리를 Cloud 파트너 포털에서 파트너 센터로 전환 하 고 있습니다. 제품이 마이그레이션될 때까지 Cloud 파트너 포털에서 [Virtual Machine 제품 만들기](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) 의 지침에 따라 제품을 관리 하세요.

이 문서에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에 대 한 Azure 가상 머신 제품을 만들고 게시 하는 방법을 설명 합니다. 운영 체제, VHD (가상 하드 디스크) 및 최대 16 개의 데이터 디스크를 포함 하는 Windows 및 Linux 기반 가상 컴퓨터를 모두 처리 합니다. 아직 수행 하지 않은 경우 시작 하기 전에 [파트너 센터에서 상업적 Marketplace 계정을 만듭니다](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) . 계정이 상업적 marketplace 프로그램에 등록 되어 있는지 확인 합니다.

## <a name="introduction"></a>소개

### <a name="publishing-benefits"></a>게시 이점

Azure Marketplace에 게시 하면 다음과 같은 이점이 있습니다.

- Microsoft 브랜드를 사용 하 여 회사 홍보
- Azure Marketplace를 통해 1억 대의 Office 365 및 Dynamics 365 사용자와 20만 이상의 조직에 도달
- 이러한 마켓플레이스에서 고품질 잠재 고객 확보
- Microsoft 필드 및 전화 영업 팀이 서비스를 승격 합니다.

### <a name="before-you-begin"></a>시작하기 전에

아직 수행 하지 않은 경우 [Virtual machine 제품 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) 및이 Azure virtual machine 자료를 검토 합니다.

- 빠른 시작 가이드
  - [Azure Quickstart 템플릿](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates)
- 자습서
  - [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- 샘플
  - [Linux Vm에 대 한 Azure CLI 샘플](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Linux Vm에 대 한 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Windows Vm에 대 한 Azure CLI 샘플](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Windows Vm에 대 한 Azure PowerShell](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>기술 기술 자료의 기본 사항

이러한 자산의 디자인, 빌드 및 테스트는 시간이 걸리고 제품을 빌드하는 데 사용 되는 Azure 플랫폼 및 기술에 대 한 기술 지식이 필요 합니다.

엔지니어링 팀은 다음과 같은 Microsoft 기술에 대해 이해 해야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)및 [azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking) 에 대 한 작업 지식

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인 합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택 합니다.
3. 개요 페이지에서 **+ 새로 만들기 제품** > **Azure Virtual Machine**을 선택 합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-azure-vm.png)

> [!NOTE]
> 제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 상점에 표시 됩니다. 변경을 수행한 후 항상 다시 게시 해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력 합니다. 계정의 각 제품에 대 한 고유 식별자입니다.

- 이 ID는 marketplace 제품의 웹 주소에 있는 고객 및 Azure PowerShell 및 Azure CLI (해당 하는 경우)에 표시 됩니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50 자로 제한 됩니다. 예를 들어 여기에 **테스트-1** 을 입력 하는 경우 제품 웹 주소는 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`입니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력 합니다. 파트너 센터의 제안에 사용 되는 이름입니다.

- 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다.

**만들기** 를 선택 하 여 제품을 생성 하 고 계속 합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="test-drive"></a>시험 사용

시험 (시험) 드라이브는 "구매 전 시도" 옵션을 제공 하 여 잠재 고객에 게 제품을 보여 줄 수 있는 좋은 방법입니다 .이를 통해 변환 및 높은 우량 잠재 고객의 생성이 증가 합니다. [테스트 드라이브에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

테스트 드라이브를 고정 된 시간 동안 사용 하도록 설정 하려면 **테스트 드라이브 사용** 확인란을 선택 합니다. 제품에서 테스트 드라이브를 제거 하려면이 확인란의 선택을 취소 합니다.

추가 테스트 드라이브 리소스:

- [기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [테스트 드라이브 개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (팝업 차단이 해제 되어 있는지 확인)

### <a name="lead-management"></a>잠재 고객 관리

파트너 센터를 사용 하 여 상업적 marketplace에 제품을 게시할 때 CRM (고객 관계 관리) 시스템에 연결 합니다. 이를 통해 사용자가에 관심을 표시 하거나 제품을 사용 하는 즉시 고객 연락처 정보를 받을 수 있습니다. **시험** 사용 (이전 섹션 참조)을 사용 하는 경우 CRM에 연결 해야 합니다. 그렇지 않으면 선택 사항입니다.

1. 잠재 고객을 보내려는 잠재 고객 대상을 선택합니다. 파트너 센터는 다음과 같은 CRM 시스템을 지원 합니다.
    - 고객 engagement의 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > CRM 시스템이 위에 나열 되지 않은 경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https 끝점](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 을 사용 하 여 고객 리드 데이터를 저장 합니다. 그런 다음 CRM 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시할 때 제품을 잠재 고객 대상에 연결 합니다.
3. 잠재 고객 대상에 대 한 연결이 제대로 구성 되어 있는지 확인 합니다. 파트너 센터에서 게시 한 후에는 연결의 유효성을 검사 하 고 테스트 리드를 전송 합니다. 제품을 라이브 상태로 전환 하는 동안 미리 보기 환경에서 제품을 직접 배포 하 여 리드 연결을 테스트할 수도 있습니다.
4. 잠재 고객이 손실 되지 않도록 잠재 고객 대상에 대 한 연결이 업데이트 된 상태로 유지 되는지 확인 합니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="properties"></a>속성

이 페이지에서는 marketplace에서 제품을 그룹화 하는 데 사용 되는 범주와 산업, 앱 버전 및 제품을 지 원하는 법적 계약을 정의할 수 있습니다.

### <a name="categories"></a>범주

최소 1 자에서 최대 5 개의 범주를 선택 합니다. 이러한 범주는 제품을 적절 한 마켓플레이스 검색 영역에 저장 하는 데 사용 됩니다. 제품 설명에서 제품이 이러한 범주를 어떻게 지원 하는지 설명 합니다. 가상 컴퓨터 제품은 Azure Marketplace의 **계산** 범주 아래에 나타납니다.

### <a name="legal"></a>법적 정보

제품에 대 한 사용 약관을 제공 해야 합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- 사용자 고유의 사용 약관 사용
- Microsoft 상업적 marketplace에 대 한 표준 계약 사용

#### <a name="use-your-own-terms-and-conditions"></a>사용자 고유의 사용 약관 사용

사용자 고유의 사용자 지정 사용 약관을 제공 하려면 사용 **약관** 상자에 최대 1만 자의 텍스트를 입력 합니다. 더 긴 설명이 필요한 경우 사용 약관을 찾을 수 있는 위치를 가리키는 단일 웹 주소를 입력 합니다. 고객이 활성 링크로 표시 됩니다.

고객은 이러한 약관에 동의 해야 제품을 사용해 볼 수 있습니다.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace에 대 한 표준 계약 사용

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft는 상업적 marketplace에 대 한 표준 계약을 제공 합니다. 표준 계약에 따라 소프트웨어를 제공 하는 경우 고객은 한 번만 읽고 동의 하면 되며 사용자 지정 사용 약관을 만들 필요가 없습니다.

**Microsoft의 상용 marketplace에 표준 계약 사용** 확인란을 선택 하 여 표준 계약을 사용 하 고 팝업 창에서 **동의** 합니다 (표시 하려면 위로 스크롤해야 할 수 있음).

![새 제품 단추와 컨설팅 서비스 제품이 선택 된 파트너 센터의 개요 페이지를 보여 줍니다.](media/use-standard-contract.png)

> [!NOTE]
> 상용 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 한 후에는 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. 표준 계약 **또는** 자신의 사용 약관에 따라 솔루션을 제공 합니다.

표준 계약에 대 한 자세한 내용은 Microsoft [상업적 marketplace](https://docs.microsoft.com/azure/marketplace/standard-contract)에 대 한 표준 계약을 참조 하세요. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) 을 PDF로 다운로드할 수 있습니다 (팝업 차단이 해제 되어 있는지 확인).

##### <a name="standard-contract-amendments"></a>표준 계약 개정

표준 계약 개정을 사용 하면 간단 하 게 표준 계약 용어를 선택 하 고 제품 또는 비즈니스에 대 한 용어를 만들 수 있습니다. 고객은 이미 Microsoft 표준 계약을 검토 하 고 동의한 경우 계약에 대 한 개정 검토 하기만 하면 됩니다. 사용할 수 있는 개정는 유니버설 및 사용자 지정의 두 가지 종류가 있습니다.

**Universal 개정** – 모든 고객의 표준 계약에 전체적으로 적용 됩니다. 구매 흐름에서 제품의 모든 고객에 게 표시 됩니다. 고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다. 제품 당 단일 범용 수정 기능을 제공할 수 있습니다. 이 상자에는 개수에 제한 없이 문자를 입력할 수 있습니다. 이러한 용어는 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에 게 표시 됩니다.

**Custom 개정** – Azure 테 넌 트 id를 통해 특정 고객을 대상으로 하는 표준 계약의 특수 한 개정입니다. 대상으로 지정할 테 넌 트를 선택할 수 있습니다. 테 넌 트의 고객만 제안의 구매 흐름에 사용자 지정 수정 용어가 표시 됩니다. 고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.

**사용자 지정 수정 용어 추가 (최대 10 개)** 를 선택 하 여 시작 합니다. 제품 당 최대 10 개의 사용자 지정 수정 단어를 제공할 수 있습니다.

- **사용자 지정 수정** 용어 – 사용자 지정 수정 약관 상자에 고유한 수정 용어를 입력 합니다. 문자 수를 제한 없이 입력할 수 있습니다. 이러한 사용자 지정 약관에 대해 지정한 테 넌 트 Id의 고객만 Azure Portal의 제품 구매 흐름에서이를 볼 수 있습니다.
- **테 넌 트 id** (필수) – 각 사용자 지정 수정은 최대 20 개의 테 넌 트 id를 대상으로 지정할 수 있습니다. 사용자 지정 수정 사항을 추가 하는 경우 Azure에서 고객을 식별 하는 테 넌 트 ID를 하나 이상 제공 해야 합니다. 고객은 Azure에서 다음 속성을 찾을 수 있습니다. 디렉터리 ID 값은 테 넌 트 ID (예: 50c464d3-4930-494c-963c-1e951d15360e)입니다. 도메인 이름 웹 주소를 사용 하 여 고객의 테 넌 트 ID를 찾을 수도 있습니다. [이는 내 Microsoft Azure 및 Office 365 테 넌 트 id?](https://www.whatismytenantid.com/)입니다.
- **설명** (선택 사항) – 수정으로 대상으로 지정 하는 고객을 식별 하는 데 도움이 되는 테 넌 트 ID에 대 한 친숙 한 설명을 제공 합니다.

> [!NOTE]
> 이러한 두 가지 유형의 개정 stack이 서로 위에 있습니다. 사용자 지정 개정를 대상으로 하는 고객은 구매 중에 표준 계약에 대 한 범용 수정도 받게 됩니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="offer-listing"></a>제품 목록

이 페이지에서는 제품 이름, 설명, 링크, 연락처 등의 제안 세부 정보를 정의할 수 있습니다.

> [!NOTE]
> 제품 설명 (예: 설명, 문서, 스크린샷 및 사용 약관)은 영어로 시작 해야 합니다. "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다." 라고 표시 됩니다. 또한 제품 목록 내용에 사용 된 것과 다른 언어로 콘텐츠를 제공 하는 _유용한 링크 웹 주소_ 를 제공할 수 있습니다.

### <a name="marketplace-details"></a>Marketplace 세부 정보

#### <a name="name"></a>Name

여기에 입력 하는 이름은 고객에 게 제품 목록의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 상자에 입력 한 텍스트로 미리 채워집니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표 수 있으며 상표 및 저작권 기호를 포함할 수 있습니다.
- 50 자를 초과할 수 없습니다.
- Emojis를 포함할 수 없습니다.

#### <a name="search-results-summary"></a>검색 결과 요약

제안에 대 한 간단한 설명입니다. 이 길이는 최대 100 자까지 가능 하며 marketplace 검색 결과에 사용 됩니다.

#### <a name="long-summary"></a>긴 요약

제품에 대 한 더 긴 설명을 제공 합니다. 이 길이는 최대 256 자까지 가능 하며 marketplace 검색 결과에 사용 됩니다.

#### <a name="description"></a>Description

제품에 대 한 자세한 설명은 최대 3000 자까지 제공 합니다. 이는 상업적 marketplace 목록 개요의 고객에 게 표시 됩니다.

설명에 다음 중 하나 이상을 포함 합니다.

- 제품의 가치 및 주요 이점
- 범주 또는 업계 연결 또는 둘 다
- 앱 내 구매 기회
- 필요한 모든 공개

설명 작성에 대 한 몇 가지 팁은 다음과 같습니다.

- 설명의 처음 몇 문장에서 제안의 가치 제안을 명확하게 설명합니다. 다음 항목을 포함 합니다.
  - 제안에 대 한 설명입니다.
  - 제품을 활용 하는 사용자의 유형입니다.
  - 고객이 요구 하거나 제공 하는 문제를 해결 합니다.
- 검색 엔진 결과에는 처음 몇 개의 문장이 표시 될 수 있습니다.
- 제품을 판매 하는 기능과 기능을 사용 하지 마세요. 대신 제품에서 제공 하는 가치에 집중 하세요.
- 산업별 또는 혜택 기반 단어를 사용 합니다.

제품 설명을 보다 유용 하 게 사용 하려면 서식 있는 텍스트 편집기를 사용 하 여 서식 지정을 적용 합니다.

![서식 있는 텍스트 편집기 사용](./media/rich-text-editor.png)

| <center>텍스트 형식 변경 | <center>글머리 기호 또는 번호 매기기 추가 | <center>텍스트 들여쓰기 추가 또는 제거 |
| --- | --- | --- |
| <center>![서식 있는 텍스트 편집기를 사용 하 여 텍스트 형식 변경](./media/text-editor3.png) |  <center>![서식 있는 텍스트 편집기를 사용 하 여 목록 추가](./media/text-editor4.png) |  <center>![서식 있는 텍스트 편집기를 사용 하 여 들여쓰기](./media/text-editor5.png) |

#### <a name="privacy-policy-link"></a>개인 정보 취급 방침 링크

조직의 개인 정보 취급 방침에 대 한 웹 주소 (URL)를 입력 합니다. 제품이 개인 정보 법률 및 규정을 준수 하는지 확인 합니다. 또한 웹 사이트에 유효한 개인 정보 취급 방침을 게시 해야 합니다.

### <a name="useful-links"></a>유용한 링크

제품에 대 한 추가 온라인 문서를 제공 합니다. 링크를 추가 하려면 **+ 링크 추가** 를 선택 하 고 다음 필드를 완료 합니다.

- **이름** – 고객의 세부 정보 페이지에 이름이 표시 됩니다.
- **링크 (URL)** – 고객이 온라인 문서를 볼 수 있는 링크를 입력 합니다.

### <a name="customer-support-links"></a>고객 지원 링크

고객이 지원 팀에 연락할 수 있는 지원 웹 사이트를 제공 합니다.

- Azure Global 지원 웹 사이트
- Azure Government 지원 웹 사이트

### <a name="partner-support-contact"></a>파트너 지원 연락처

고객이 지원 티켓을 열 때 사용할 Microsoft 파트너에 대 한 연락처 정보를 제공 합니다. Marketplace에는이 내용이 나열 되지 않습니다.

- Name
- 메일
- Phone

### <a name="engineering-contact"></a>엔지니어링 연락처

인증 관련 문제를 포함 하 여 제품에 문제가 있을 때 사용할 Microsoft의 연락처 정보를 제공 합니다. Marketplace에는이 내용이 나열 되지 않습니다.

- Name
- 메일
- Phone

### <a name="marketplace-media"></a>Marketplace 미디어

제품에 사용할 로고 및 이미지를 제공 합니다. 모든 이미지는 PNG 형식 이어야 합니다. 이미지가 흐리게 발생 하면 제출이 거부 됩니다.

>[!Note]
>파일을 업로드 하는 동안 문제가 발생 하는 경우 로컬 네트워크가 파트너 센터에서 https://upload.xboxlive.com 사용 하는 서비스를 차단 하지 않는지 확인 합니다.

#### <a name="marketplace-logos"></a>마켓플레이스 로고

다음 네 픽셀 크기의 제품 로고에 대 한 PNG 파일을 제공 합니다.

- **작음** (48 x 48)
- **보통** (90 x 90)
- **큼** (216 x 216)
- **넓게** (255 x 115)

4 개의 로고가 모두 필요 하며 marketplace 목록의 다른 위치에서 사용 됩니다.

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 최대 5 개의 스크린샷을 추가 합니다. 각 스크린샷은 1280 x 720 픽셀 크기 및 PNG 형식 이어야 합니다. 각 스크린샷은 캡션을 포함 해야 합니다.

#### <a name="videos"></a>동영상

제품을 설명 하는 비디오를 최대 5 개까지 추가 합니다. 이러한 서비스는 외부 비디오 서비스에서 호스팅되어야 합니다. 각 비디오의 이름, 웹 주소 및 비디오의 미리 보기 PNG 이미지를 1280 x 720 픽셀에 입력 합니다.

리소스를 나열 하는 추가 marketplace는 [marketplace 제품 목록에 대 한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)를 참조 하세요.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="preview"></a>미리 보기

미리 보기 탭에서 제품의 유효성을 검사 하기 위한 제한 된 **미리 보기 대상 그룹** 을 선택 하 여 더 광범위 한 marketplace 대상에 게시 합니다.

> [!IMPORTANT]
> 미리 보기에서 제품을 확인 한 후 **Live로 이동** 을 선택 하 여 제품을 상업적 marketplace 공개 대상에 게시 합니다.

미리 보기 대상 사용자는 각 항목에 대 한 선택적 설명과 함께 Azure 구독 ID Guid로 식별 됩니다. 이러한 필드는 고객에 게 표시 되지 않습니다. Azure Portal의 **구독** 페이지에서 AZURE 구독 ID를 찾을 수 있습니다.

하나 이상의 Azure 구독 ID를 개별적으로 (최대 10 개) 또는 CSV 파일을 업로드 하 여 추가 합니다 (최대 100). 이러한 구독 Id를 추가 하 여 live를 게시 하기 전에 제품을 미리 볼 수 있는 사람을 정의 합니다. 제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용 또는 업데이트를 제공 하기 위해 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 사용자는 개인 대상과 다릅니다. 미리 보기 대상은 마켓플레이스에서 live 게시 _되기 전에_ 제품에 액세스할 수 있습니다. 귀하는 제품이 marketplace에 완전히 게시 된 후 개인 사용자 에게만 제공 되는 계획을 포함 하 여 모든 계획을 확인 하 고 유효성을 검사할 수 있습니다. 요금제 **가격 책정 및 가용성** 탭에 정의 된 개인 사용자는 특정 요금제에 단독으로 액세스할 수 있습니다.

다음 섹션인 계획 개요로 진행 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="plan-overview"></a>계획 개요

파트너 센터에서 동일한 제품 내에 다른 계획 옵션을 제공할 수 있습니다. 이러한 계획은 이전에 Sku 라고 합니다. 제품에는 하나 이상의 계획이 필요 하며,이는 수익 화 대상 그룹, Azure 지역, 기능 또는 VM 이미지 측면에서 다를 수 있습니다.

계획을 만든 후 **계획 개요** 탭에 다음이 표시 됩니다.

- 계획 이름
- 라이선스 모델
- 대상 (공용 또는 개인)
- 현재 게시 상태
- 사용 가능한 작업

계획 개요에서 사용할 수 있는 작업은 계획의 현재 상태에 따라 달라 집니다. 해당 기능은 아래와 같습니다.

- **초안 삭제** – 계획 상태가 초안 인 경우
- 요금제 **판매를 중지** 하거나 **비공개 사용자를 동기화** – 계획 상태가 실시간으로 게시 된 경우

### <a name="create-new-plan"></a>새 계획 만들기

위쪽에서 **+ 새 계획 만들기** 를 선택 합니다. **새 계획** 대화 상자가 나타납니다.

**계획 id** 상자에서이 제안의 각 계획에 대 한 고유한 계획 id를 만듭니다. 이 ID는 제품 웹 주소에 있는 고객에 게 표시 됩니다. 소문자와 숫자, 대시 또는 밑줄만 사용할 수 있으며 최대 50 자만 사용할 수 있습니다.

> [!NOTE]
> **만들기**를 선택한 후에는 계획 ID를 변경할 수 없습니다.

**계획 이름** 상자에이 계획의 이름을 입력 합니다. 제품 내에서 선택할 계획을 결정할 때 고객에 게이 이름이 표시 됩니다. 각 계획의 차이를 명확 하 게 가리키는 고유한 이름을 만듭니다. 예를 들어 요금제 **종 량**제, **byol**, **Advanced**및 **Enterprise**와 함께 **Windows Server** 를 사용할 수 있습니다.

**만들기**를 선택합니다.

### <a name="plan-setup"></a>설정 계획

계획의 유형에 대 한 상위 수준 구성을 설정 하 고, 다른 계획의 기술 구성을 재사용 하 고, 계획을 사용할 수 있는 Azure 지역을 설정 합니다. 선택 항목에 따라 동일한 계획에 대 한 다른 탭에 표시 되는 필드가 결정 됩니다.

#### <a name="re-use-technical-configuration"></a>기술 구성 다시 사용

동일한 유형의 계획이 두 개 이상 있고 패키지가 둘 사이에 동일한 경우 **이 계획에서 다른 계획의 기술 구성**다시 사용할 계획을 선택할 수 있습니다. 이 옵션을 사용 하면이 제품에 대해 동일한 유형의 다른 계획 중 하나를 선택 하 고 해당 기술 구성을 다시 사용할 수 있습니다.

> [!NOTE]
> 다른 계획에서 기술 구성을 다시 사용 하는 경우이 계획에서 전체 **기술 구성** 탭이 사라집니다. 미래의 모든 업데이트를 포함 하 여 다른 계획의 기술 구성 세부 정보는이 계획에도 사용 됩니다. 이 계획을 게시 한 후에는이 설정을 변경할 수 없습니다.

#### <a name="azure-regions"></a>Azure 지역

계획은 하나 이상의 Azure 지역에서 사용할 수 있어야 합니다.

**Azure global** 옵션을 선택 하 여 상업적 marketplace 통합이 있는 모든 azure 글로벌 지역에서 고객이 요금제를 사용할 수 있도록 합니다. 자세한 내용은 [지리적 가용성 및 통화 지원](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)을 참조 하세요.

**Azure Government** 옵션을 선택 하 여 [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) 지역에서 계획을 사용할 수 있도록 합니다. 이 지역은 미국 연방, 주, 지방 또는 부족의 고객에 대 한 제어 된 액세스와 이러한 엔터티를 제공 하는 파트너를 제공 합니다. 게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당 합니다. Azure Government는 미국에만 있는 물리적으로 격리 된 데이터 센터와 네트워크를 사용 합니다.

[Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)에 게시 하기 전에 특정 끝점이 다를 수 있으므로 환경에서 계획을 테스트 하 고 유효성을 검사 합니다. 계획을 설정 하 고 테스트 하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청 합니다.

> [!NOTE]
> 계획을 게시 하 고 특정 Azure 지역에서 사용할 수 있게 되 면 해당 지역을 제거할 수 없습니다.

#### <a name="azure-government-certifications"></a>Azure Government 인증

이 옵션은 **Azure Government**를 선택한 경우에만 표시 됩니다.

Azure Government services는 특정 정부 규정 및 요구 사항이 적용 되는 데이터를 처리 합니다. 예를 들어 FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 이러한 프로그램에 대 한 인증을 인식 하기 위해 이러한 프로그램을 설명 하는 최대 100 링크를 제공할 수 있습니다. 이러한 링크는 프로그램의 목록에 대 한 링크 이거나 사용자의 웹 사이트에서 준수의 설명에 대 한 링크 일 수 있습니다. 이러한 링크는 Azure Government 고객만 볼 수 있습니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

### <a name="plan-listing"></a>계획 목록

여기서 계획의 세부 정보를 구성 합니다. 이 탭에는 동일한 제품의 계획 간에 다를 수 있는 특정 정보가 표시 됩니다.

#### <a name="plan-name"></a>계획 이름

이는 계획을 만들 때 지정한 이름으로 미리 채워져 있습니다. 이 이름은 marketplace에이 계획의 제목으로 표시 되며 100 자로 제한 됩니다.

#### <a name="plan-summary"></a>계획 요약

요금제에 대 한 간략 한 요약을 제공 합니다 (제안 아님). 이 요약은 100 자로 제한 됩니다.

#### <a name="plan-description"></a>계획 설명

이 소프트웨어 계획을 고유 하 게 만드는 방법 및 제품 내 계획 간의 차이점을 설명 합니다. 제품을 설명 하지 않고 요금제만 제공 합니다. 계획 설명에는 최대 2000 자까지 사용할 수 있습니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

### <a name="pricing-and-availability"></a>가격 책정 및 가용성

이 탭에서 다음을 구성 합니다.

- 이 요금제를 사용할 수 있는 시장
- 시간당 가격
- 요금제를 모든 사용자에 게 표시할지 아니면 특정 고객 에게만 표시할지 여부 (개인 사용자)

#### <a name="markets"></a>시장

모든 요금제는 하나 이상의 시장에서 사용할 수 있어야 합니다. 이 요금제를 구매할 수 있는 모든 시장 위치에 대 한 확인란을 선택 합니다. 이러한 시장의 사용자는 **[계획 설정](#plan-setup)** 에서 선택한 모든 Azure 지역에 제품을 계속 배포할 수 있습니다. **세금 송금** 단추는 Microsoft에서 판매를 송금 하 고 세금을 사용 하는 국가를 표시 합니다. 중국에 게시 하는 것은 무료 또는 byol (사용자 **라이선스** **필요** )의 요금제로 제한 됩니다.

요금제에 대 한 가격을 이미 미국 달러 (USD)로 설정 하 고 다른 시장 위치를 추가 하는 경우 새 시장의 가격은 현재 환율에 따라 계산 됩니다. 게시 하기 전에 항상 각 시장의 가격을 확인 하세요. 변경 내용을 저장 한 후에는 사용 된 **가격 (.xlsx)** 링크를 사용 하 여 가격을 검토 합니다.

시장을 제거 하면 활성 배포를 사용 하는 시장의 고객이 새 배포를 만들거나 기존 배포를 확장할 수 없습니다. 기존 배포에는 영향을 주지 않습니다.

#### <a name="pricing"></a>가격 책정

**라이선스 모델** –이 요금제에 대 한 가격 책정을 구성 하거나 고객이 기존 라이선스를 사용 하 여이 요금제를 사용할 수 있도록 **사용자 라이선스를 가져오도록** **사용량 기반 월별 청구 계획** 을 선택 합니다.

사용량 기반 월별 청구 계획의 경우 다음 세 가지 가격 책정 항목 옵션 중 하나를 사용 합니다.

- **코어 당** – 미국 달러 (USD)에서 코어 당 가격을 제공 합니다. 현재 환율을 사용 하 여 코어 크기 당 가격을 계산 하 고 현지 통화로 변환 합니다.
- **코어 크기 당** – USD에서 코어 크기 당 가격을 제공 합니다. 현재 환율을 사용 하 여 가격을 현지 통화로 변환 합니다.
- **시장 및 핵심 규모** – 모든 시장에서 각 코어 크기에 대 한 가격을 제공 합니다. 스프레드시트에서 가격을 가져올 수 있습니다.

> [!NOTE]
> 가격 책정 데이터를 내보낼 수 있도록 가격 책정 변경 내용을 저장 합니다. 요금제의 시장 가격은 게시 된 후에는 나중에 변경할 수 없습니다. 가격 책정 스프레드시트를 내보내고 각 시장에서 가격을 검토 하 여 이러한 가격은 게시 전에 적절 한지 확인 합니다.

#### <a name="free-trial"></a>평가판

한 달 또는 3 개월 무료 평가판을 고객에 게 제공할 수 있습니다.

#### <a name="visibility"></a>표시 유형

모든 사용자 또는 미리 선택 된 대상 그룹에 게 표시 되도록 각 계획을 디자인할 수 있습니다. Azure 구독 Id를 사용 하 여이 제한 된 사용자의 멤버 자격을 할당 합니다.

**공개** -모든 사용자가 계획을 볼 수 있습니다.

**비공개** 사용자-계획이 미리 선택 된 대상 에게만 표시 되도록 합니다. 비공개 계획으로 게시 된 후에는 대상 그룹을 업데이트 하거나 공용으로 변경할 수 있습니다. 계획을 공개 한 후에는 공용으로 유지 해야 합니다. 다시 개인으로 변경할 수는 없습니다.

**제한 된 대상 그룹 (azure 구독 id)** – Azure 구독 id를 사용 하 여이 비공개 요금제에 액세스할 수 있는 대상 그룹을 할당 합니다. 필요에 따라 할당 한 각 Azure 구독 ID에 대 한 설명을 포함 합니다. CSV 스프레드시트를 가져오는 경우 최대 10 개의 구독 Id를 수동으로 추가 하거나 2만 합니다. Azure 구독 Id는 Guid로 표시 되며 문자는 소문자 여야 합니다.

> [!NOTE]
> 비공개 또는 제한 된 대상은 **미리 보기** 탭에서 정의한 미리 보기 대상과 다릅니다. 미리 보기 대상 사용자는 marketplace에서 게시 된 라이브 _전에_ 제품에 액세스할 수 있습니다. 개인 대상 그룹은 특정 계획에만 적용 되지만 미리 보기 대상은 유효성 검사를 위해 모든 요금제 (비공개 또는 not)를 볼 수 있습니다.

#### <a name="hide-plan"></a>계획 숨기기

다른 솔루션 템플릿 또는 관리 되는 응용 프로그램을 통해 참조 하는 경우에만 가상 컴퓨터를 간접적으로 사용 하려면이 상자를 선택 하 여 가상 컴퓨터를 게시 하 고 고객을 검색 하 여 직접 검색 하는 컴퓨터에서 숨깁니다.

> [!NOTE]
> 숨겨진 요금제는 미리 보기 링크를 지원 하지 않습니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

### <a name="technical-configuration"></a>기술 구성

이 계획과 관련 된 이미지 및 기타 기술 속성을 제공 합니다. 자세한 내용은 [AZURE VM 기술 자산 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)를 참조 하세요.

> [!NOTE]
> 이 탭은 **계획 설정** 탭에서 다른 계획의 패키지를 다시 사용 하도록 구성한 경우에는 표시 되지 않습니다.

#### <a name="operating-system"></a>운영 체제

- **운영 체제 제품군** – **Windows** 또는 **Linux** 운영 체제에서 선택
- **릴리스** 또는 **공급 업체** – Windows 릴리스 또는 Linux 공급 업체를 선택 합니다.
- **OS 이름** – 친숙 한 운영 체제 이름을 선택 합니다. 이 이름은 고객에 게 표시 됩니다.

#### <a name="recommended-vm-sizes"></a>권장되는 VM 크기

Azure Marketplace에 표시할 권장 가상 머신 크기를 최대 6 개까지 선택 합니다.

#### <a name="open-ports"></a>포트 열기

배포 된 가상 머신에서 공용 또는 개인 포트를 엽니다.

#### <a name="storage-option-for-deployment"></a>배포용 저장소 옵션

**디스크 배포 옵션** – 가상 컴퓨터를 사용할 때 사용자가 사용할 수 있는 디스크 배포 종류를 선택 합니다. 배포를 관리 디스크 배포로만 제한 하는 것이 좋습니다.

#### <a name="properties"></a>속성

**가속 네트워킹 지원** – VM에서 [가속 네트워킹](https://go.microsoft.com/fwlink/?linkid=2124513)을 지 원하는 경우 선택 합니다.

#### <a name="vm-images"></a>VM 이미지

가상 컴퓨터 이미지에 대 한 디스크 버전 및 SAS URI를 제공 합니다. 각 VM 이미지에 대해 최대 16 개의 데이터 디스크를 추가 합니다. 지정 된 제출에서 요금제 당 새 이미지 버전을 하나만 제공 합니다. 이미지를 게시 한 후에는 편집할 수 없지만 삭제할 수는 있습니다. 버전을 삭제 하면 새 사용자와 기존 사용자가 모두 삭제 된 버전의 새 인스턴스를 배포할 수 없습니다.

- **디스크 버전** 은 사용자가 제공 하는 이미지의 버전입니다.
- **SAS URI** 는 운영 체제 VHD를 저장 한 Azure Storage의 위치입니다.
- 데이터 디스크 이미지는 Azure storage에 저장 된 VHD SAS Uri 이기도 합니다.
- 계획에서 제출 당 이미지를 하나만 추가 합니다.

사용 중인 운영 체제에 관계 없이 솔루션에 필요한 최소 개수의 데이터 디스크만 추가 합니다. 고객은 배포 시 이미지의 일부인 디스크를 제거할 수 없지만, 배포 중에 또는 배포 후에 항상 디스크를 추가할 수 있습니다.

계속 하기 전에 **초안 저장** 을 선택 하 고 **계획 개요**로 돌아갑니다.

## <a name="resell-through-csps"></a>Csp를 통한 재판매

CSP ( [클라우드 솔루션 공급자](https://azure.microsoft.com/offers/ms-azr-0145p/) ) 프로그램에서 파트너에 게 제공 되는 제품의 도달 범위를 확장 합니다. BYOL (사용자 라이선스 보유) 계획은 자동으로 옵트인 됩니다. 비 BYOL 계획을 옵트인 (opt in) 하도록 선택할 수 있습니다.

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="test-drive"></a>시험 사용

고객이 제품을 구매할 수 있게 해 주는 데모 (테스트 드라이브)를 설정 합니다. 고객이 고정 기간 동안 제품을 사용해 볼 수 있도록 하는 데모 환경을 만들려면 [상용 marketplace에서 제품 시험](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)사용을 참조 하세요.

테스트 드라이브를 사용 하도록 설정 하려면 [제품 설정](#test-drive) 탭에서 테스트 드라이브 사용 확인란을 선택 합니다. 제품에서 테스트 드라이브를 제거 하려면이 확인란의 선택을 취소 합니다.

추가 테스트 드라이브 리소스:

- [마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF, 팝업 차단이 해제 되어 있는지 확인)

계속 하기 전에 **초안 저장** 을 선택 합니다.

## <a name="review-and-publish"></a>검토 및 게시

제품의 필수 섹션을 모두 완료 한 후에 제출 하 여 검토 하 고 게시할 수 있습니다.

포털의 오른쪽 위 모서리에서 **검토 및 게시**를 선택 합니다.

이 페이지에서는

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다.
  - **시작 되지 않음** -섹션이 시작 되지 않아 완료 해야 합니다.
  - **불완전** -섹션에 수정 해야 하는 오류가 있거나 추가 정보를 제공 해야 합니다. 이 섹션을 업데이트 하는 방법에 대 한 지침은이 문서의 앞부분에 나오는 섹션을 참조 하세요.
  - **완료** – 섹션이 완료 되 고 오류가 발생 하지 않습니다. 제품을 제출 하려면 먼저 제품의 모든 섹션을 완료 해야 합니다.
- **인증에 대 한 참고 사항** -인증 팀에 테스트 지침을 제공 하 여 앱이 제대로 테스트 되었는지 확인 합니다. 앱을 이해 하는 데 도움이 되는 보조 노트를 제공 합니다.

게시를 위해 제품을 제출 하려면 **검토 및 게시**를 선택 합니다.

제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 시기를 알려 주는 전자 메일을 보내 드리겠습니다. 공개에 제품을 게시 하려면 (또는 개인 제품이 개인 사용자에 게 제공 하는 경우) 파트너 센터로 이동 하 여 제품의 **개요** 페이지를 찾아 **라이브**를 선택 합니다. 게시가 진행 중일 때 제품의 상태가 페이지 맨 위에 표시 됩니다.

### <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

게시 프로세스의 **수동 유효성 검사** 단계는 제품 및 이와 관련 된 기술 자산의 광범위 한 검토를 나타냅니다. 이 프로세스에서 제공 하는 오류를 발견 하면 문제를 자세히 설명 하는 인증 보고서를 받게 됩니다. 필요한 수정 작업을 수행 하 고 제품을 다시 게시 하기만 하면 됩니다.

## <a name="offer-overview"></a>제품 개요

**제품 개요** 페이지에는이 제품을 게시 하는 데 필요한 단계를 시각적으로 표시 하 고 (완료 및 진행 중) 각 단계를 완료 하는 데 소요 되는 시간이 표시 됩니다.

이 페이지에는 사용자가 선택한 항목에 따라이 제품에 대 한 작업을 수행할 수 있는 링크가 포함 되어 있습니다. 예를 들면 다음과 같습니다.

- 제안이 초안 [삭제 제안](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) 인 경우
- 제품이 라이브 인 경우 [제품 판매를 중지](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) 합니다.
- 제품이 미리 보기 상태 이면 [라이브로 전환](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval) 합니다.
- 게시자 로그 오프를 완료 하지 않은 경우- [게시 취소](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Marketplace 예

이러한 예제에서는 제품이 Azure Marketplace 표시 되는 방식을 보여 줍니다.

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace 제품 세부 정보

![Azure Marketplace 제품 세부 정보 화면 예제](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure Marketplace 검색 결과

![Azure Marketplace 검색 세부 정보 화면 예제](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure Marketplace 계획 세부 정보

![Azure Marketplace 계획 정보 화면 예제](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure Portal 제품 세부 정보

![Azure Portal 제품 세부 정보 화면 예제](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure Portal 검색 결과

![Azure Portal 검색 결과 화면 예제](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure Portal 계획 세부 정보

![Azure Portal 계획 정보 화면 예제](media/avm-create6.png)

## <a name="next-step"></a>다음 단계

- [상업적 marketplace에서 기존 제품 업데이트](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
