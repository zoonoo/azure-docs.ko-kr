---
title: Azure 가상 시스템 오퍼 만들기 - Azure 마켓플레이스
description: 상용 시장에서 가상 머신 오퍼를 만드는 방법을 알아봅니다.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d598f741c5add58a89aa2b7aa01802a7e35f9a19
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869052"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Azure 가상 시스템 오퍼 만들기

> [!IMPORTANT]
> Azure VM 오퍼의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 포털의 [가상 컴퓨터 생성 제안의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) 지침을 계속 따라 오퍼를 관리하십시오.

이 문서에서는 Azure Virtual 컴퓨터 를 만들고 게시하는 방법을 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에 게시합니다. 운영 체제, VHD(가상 하드 디스크) 및 최대 16개의 데이터 디스크를 포함하는 Windows 및 Linux 기반 가상 머신을 모두 처리합니다.

## <a name="introduction"></a>소개

### <a name="publishing-benefits"></a>게시 이점

Azure 마켓플레이스에 게시하는 경우 다음과 같은 이점이 있습니다.

- Microsoft 브랜드를 사용하여 회사 홍보
- Azure Marketplace를 통해 1억 개 이상의 Office 365 및 Dynamics 365 사용자 및 200,000개 이상의 조직에 도달
- 이러한 마켓플레이스에서 고품질 잠재 고객 얻기
- Microsoft 현장 및 원격 판매 팀에서 서비스 홍보

### <a name="before-you-begin"></a>시작하기 전에

아직 수행하지 않은 경우 [가상 컴퓨터 제공 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) 및 이 Azure 가상 시스템 자료를 검토하십시오.

- 빠른 시작 가이드
  - [Azure Quickstart 템플릿](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates)
- 자습서
  - [Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- 샘플
  - [리눅스 VM에 대 한 Azure CLI 샘플](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [리눅스 VM에 대 한 Azure 파워 쉘](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Windows VM용 Azure CLI 샘플](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [윈도우 VM용 Azure 파워쉘](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>기술 지식의 기초

이러한 자산을 디자인, 빌드 및 테스트하려면 시간이 걸리며 Azure 플랫폼과 오퍼를 빌드하는 데 사용되는 기술에 대한 기술 지식이 필요합니다.

엔지니어링 팀은 다음 Microsoft 기술을 이해해야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)에 대한 기본적 이해
- [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
- [Azure 가상 컴퓨터,](https://azure.microsoft.com/services/virtual-machines/) [Azure 저장소](https://azure.microsoft.com/services/?filter=storage#storage)및 Azure [네트워킹에](https://azure.microsoft.com/services/?filter=networking#networking) 대한 작업 지식

## <a name="create-an-azure-virtual-machine-offer"></a>Azure 가상 시스템 오퍼 만들기

Azure 가상 시스템 오퍼를 만들려면 먼저 파트너 센터에 상용 마켓플레이스 계정이 있어야 합니다. 아직 만들지 않은 경우 파트너 [센터에서 상업용 마켓플레이스 계정 만들기를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)참조하세요.

1. [파트너 센터에](https://partner.microsoft.com/dashboard/home)로그인한 다음 상단 메뉴에서 **대시보드**를 선택합니다.
2. 왼쪽 탐색 막대에서 **상업용 마켓플레이스를**선택한 다음 **개요를 선택합니다.**
3. **개요** 페이지에서 **+ 새 제안을**선택한 다음 Azure 가상 **컴퓨터를**선택합니다. **새 오퍼** 대화 상자가 나타납니다.

    ![파트너 센터의 개요 페이지를 새 오퍼 단추 및 Azure 가상 컴퓨터 제품 선택으로 보여 줍니다.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>제공 ID 및 별칭

**오퍼 ID를**입력합니다. 이 식별자는 계정의 각 오퍼에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 오퍼의 웹 주소와 해당하는 경우 Azure PowerShell 및 Azure CLI의 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈과 밑줄이 포함될 수 있지만 공백은 포함되지 않으며 50자로 제한됩니다. 예를 들어 여기에 **테스트 제안-1을** 입력하면 제안 웹 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`주소가 됩니다.
- 을 **선택한**후에는 오퍼 ID를 변경할 수 없습니다.

**오퍼 별칭을**입력합니다. 파트너 센터에서 오퍼에 사용된 이름입니다. 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 오퍼 이름 및 기타 값과 다릅니다.

오퍼를 생성하고 계속하려면 **만들기를** 선택합니다.

## <a name="offer-setup"></a>오퍼 설정

### <a name="test-drive"></a>시험 사용

고객이 제품을 구매하기 전에 제품을 시험해 볼 수 있는 데모(테스트 드라이브)를 설정합니다. 고객이 일정 기간 동안 쿠폰을 시도할 수 있는 데모 환경을 만들려면 [상업용 시장에서 제품을 테스트 드라이브를 참조하세요.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

테스트 드라이브를 사용하려면 **테스트 드라이브 활성화 확인란을** 선택합니다. 오퍼에서 테스트 드라이브를 제거하려면 이 확인란을 선택 취소하십시오.

추가 테스트 드라이브 리소스:

- [기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [테스트 드라이브 개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF(팝업 차단이 꺼져 있는지 확인).

### <a name="lead-management"></a>잠재 고객 관리

파트너 센터를 통해 상용 마켓플레이스에 오퍼를 게시할 때 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 다른 사람이 귀사에 관심을 표명하거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. **테스트 드라이브를** 사용하도록 설정하려면 CRM에 연결해야 합니다(이전 섹션 참조).

1. 잠재 고객을 보내려는 잠재 고객 대상을 선택합니다. 파트너 센터는 다음 CRM 시스템을 지원합니다.
    - 고객 참여를 위한 [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics)
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > CRM 시스템이 위에 나열되지 않은 경우 [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) 또는 [Https Endpoint를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) 사용하여 고객 잠재 고객 데이터를 저장합니다. 그런 다음 데이터를 CRM 시스템으로 내보냅니다.

2. 파트너 센터에 게시할 때 잠재 고객 대상에 쿠폰을 연결합니다.
3. 잠재 고객 대상에 대한 연결이 제대로 구성되어 있는지 확인합니다. 파트너 센터에 게시한 후 연결의 유효성을 검사하고 테스트 리드를 보냅니다. 오퍼가 시작되기 전에 쿠폰을 미리 볼 때 미리 보기 환경에서 직접 쿠폰을 배포하여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객을 잃지 않도록 잠재 고객 대상에 대한 연결이 업데이트된 상태로 유지되도록 합니다.

계속하기 전에 **초안 저장을** 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 마켓플레이스에서 쿠폰을 그룹화하는 데 사용되는 카테고리 및 산업, 앱 버전 및 쿠폰을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="categories"></a>범주

최소 1개 및 최대 5개 범주를 선택합니다. 이러한 카테고리는 적절한 마켓플레이스 검색 영역에 쿠폰을 배치하는 데 사용됩니다. 쿠폰 설명에서 쿠폰이 이러한 범주를 지원하는 방법을 설명합니다. 가상 시스템 오퍼는 Azure 마켓플레이스의 **계산** 범주 아래에 표시됩니다.

### <a name="legal"></a>법적 정보

오퍼에 대한 이용 약관을 제공해야 합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- 사용자 고유의 이용 약관 사용
- Microsoft 상용 마켓플레이스에 대한 표준 계약 사용

#### <a name="use-your-own-terms-and-conditions"></a>사용자 고유의 이용 약관 사용

사용자 지정 이용 약관을 제공하려면 **이용 약관** 상자에 최대 10,000자의 텍스트를 입력합니다. 더 긴 설명이 필요한 경우 이용 약관을 찾을 수 있는 위치를 가리키는 단일 웹 주소를 입력합니다. 고객에게 활성 링크로 표시됩니다.

고객은 제품을 사용해 본 전에 이 약관에 동의해야 합니다.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상용 마켓플레이스에 대한 표준 계약 사용

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상용 시장에 대한 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 한 번만 읽고 수락하면 되며 사용자 지정 이용 약관을 만들 필요가 없습니다.

**Microsoft의 상용 마켓플레이스에 대한 표준 계약 사용** 확인란을 선택한 다음 팝업 창에서 **수락을** 선택하여 표준 계약을 사용합니다(보기 위해 위로 스크롤해야 할 수 있음).

![파트너 센터의 개요 페이지를 선택한 새로운 오퍼 버튼 및 컨설팅 서비스 제공을 보여 줍니다.](media/use-standard-contract.png)

> [!NOTE]
> 상업용 마켓플레이스에 대한 표준 계약을 사용하여 오퍼를 게시한 후에는 사용자 지정 이용 약관을 사용할 수 없습니다. 표준 계약에 따라 **또는** 자체 이용 약관에 따라 솔루션을 제공합니다.

표준 계약에 대한 자세한 내용은 Microsoft 상용 [시장에](https://docs.microsoft.com/azure/marketplace/standard-contract)대한 표준 계약을 참조하십시오. [표준 계약을](https://go.microsoft.com/fwlink/?linkid=2041178) PDF로 다운로드할 수 있습니다(팝업 차단이 꺼져 있는지 확인).

##### <a name="standard-contract-amendments"></a>표준 계약 개정

표준 계약 개정을 통해 단순함을 위해 표준 계약 조건을 선택하고 제품 또는 비즈니스에 대한 조건을 만들 수 있습니다. 고객은 Microsoft 표준 계약을 이미 검토하고 수락한 경우에만 계약 수정을 검토하면 됩니다. 보편적 인 관습 : 사용할 수있는 개정의 두 가지 종류가 있습니다.

**보편적 개정** - 이들은 모든 고객을위한 표준 계약에 보편적으로 적용됩니다. 구매 흐름에서 오퍼의 모든 고객에게 표시됩니다. 고객은 귀하의 제안을 사용하기 전에 표준 계약 및 개정안의 약관에 동의해야 합니다. 오퍼당 단일 보편적 수정을 제공할 수 있습니다. 이 상자에 문자를 무제한으로 입력할 수 있습니다. 이러한 용어는 검색 및 구매 흐름 중에 AppSource, Azure 마켓플레이스 및/또는 Azure 포털의 고객에게 표시됩니다.

**사용자 지정 수정** - Azure 테넌트 아이디를 통해 특정 고객을 대상으로 하는 표준 계약에 대한 특별 개정입니다. 대상으로 지정할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 오퍼의 구매 흐름에서 사용자 지정 수정 약관이 제공됩니다. 고객은 귀하의 제안을 사용하기 전에 표준 계약 및 개정안의 약관에 동의해야 합니다.

먼저 **사용자 지정 수정 조건 추가(최대 10)를**선택합니다. 오퍼당 최대 10개의 맞춤 수정 조건을 제공할 수 있습니다.

- **사용자 지정 개정 약관** - 사용자 지정 개정 약관 상자에 자신의 수정 약관을 입력합니다. 문자를 무제한으로 입력할 수 있습니다. 이러한 사용자 지정 용어에 대해 지정한 테넌트 아이디의 고객만 Azure Portal의 오퍼의 구매 흐름에 이러한 용어가 표시됩니다.
- **테넌트 아이디(필수)** – 각 사용자 지정 수정안은 최대 20개의 테넌트 아이디를 대상으로 지정할 수 있습니다. 사용자 지정 수정 본인을 추가하는 경우 Azure에서 고객을 식별하는 테넌트 ID를 하나 이상 제공해야 합니다. 고객은 아래의 Azure에서 당신을 찾을 수 있습니다. 디렉터리 ID 값은 테넌트 ID(예: 50c464d3-4930-493c-963c-963c-1e951d15360e)입니다. 또한 [내 Microsoft Azure 및 Office 365 테넌트](https://www.whatismytenantid.com/)ID는 무엇입니까에서 도메인 이름 웹 주소를 사용하여 고객의 조직의 테넌트 ID를 찾을 수 있습니다.
- **설명(선택** 사항) - 개정을 통해 타겟팅하는 고객을 식별하는 데 도움이 되는 테넌트 ID에 대한 친숙한 설명을 제공합니다.

> [!NOTE]
> 이 두 가지 유형의 개정은 서로 겹치게 됩니다. 사용자 지정 개정 대상 고객은 구매 시 표준 계약에 대한 보편적 인 개정을 받게됩니다.

계속하기 전에 **초안 저장을** 선택합니다.

## <a name="offer-listing"></a>오퍼 리스팅

이 페이지에서는 제안 이름, 설명, 링크 및 연락처와 같은 제안 세부 정보를 정의할 수 있습니다.

> [!NOTE]
> 오퍼 리스팅 콘텐츠(예: 설명, 문서, 스크린샷 및 이용 약관)는 "이 응용 프로그램은 [영어 이외의 언어]에서만 사용할 수 있습니다"라는 문구로 시작되는 한 영어로 작성할 필요가 없습니다. 또한 _유용한 링크 웹 주소를_ 제공하여 쿠폰 목록 콘텐츠에 사용된 언어이외의 언어로 콘텐츠를 제공할 수도 있습니다.

### <a name="marketplace-details"></a>마켓플레이스 세부 정보

#### <a name="name"></a>이름

여기에 입력한 이름은 고객에게 쿠폰 목록의 제목으로 표시됩니다. 이 필드는 **오퍼를** 만들 때 쿠폰 별칭 상자에 입력한 텍스트로 미리 채워져 있습니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표권(상표 및 저작권 기호 포함).
- 50자 이상일 수 없습니다.
- 이모티콘을 포함할 수 없습니다.

#### <a name="search-results-summary"></a>검색 결과 요약

오퍼에 대한 간략한 설명입니다. 최대 100자까지 사용할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

#### <a name="long-summary"></a>긴 요약

오퍼에 대한 더 긴 설명을 제공합니다. 최대 256자까지 사용할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

#### <a name="description"></a>설명

최대 3,000자까지 오퍼에 대한 자세한 설명을 제공합니다. 이는 상업용 마켓플레이스 목록 개요의 고객에게 표시됩니다.

설명에 다음 중 하나 이상을 포함하십시오.

- 오퍼의 가치와 주요 이점
- 범주 또는 산업 협회 또는 둘 다
- 인앱 구매 기회
- 필요한 모든 공개

다음은 설명을 작성하기 위한 몇 가지 팁입니다.

- 설명의 처음 몇 문장에서 제안의 가치 제안을 명확하게 설명합니다. 다음 항목을 포함합니다.
  - 제안에 대한 설명입니다.
  - 쿠폰의 혜택을 누릴 수 있는 사용자 유형입니다.
  - 고객의 요구 또는 오퍼가 해결하는 문제.
- 처음 몇 문장은 검색 엔진 결과에 표시될 수 있습니다.
- 쿠폰을 판매하는 기능과 기능에 의존하지 마세요. 대신 오퍼가 제공하는 가치에 초점을 맞춥니다.
- 산업별 또는 혜택 기반 단어를 사용합니다.

쿠폰 설명을 더욱 매력적으로 만들려면 고급 텍스트 편집기를 사용하여 설명의 서식을 지정합니다. 리치 텍스트 편집기를 사용하면 숫자, 글머리 기호, 굵은 글꼴, 기울임꼴 및 들여쓰기를 추가하여 설명을 더 읽기 쉽게 만들 수 있습니다.

![파트너 센터의 개요 페이지를 선택한 새로운 오퍼 버튼 및 컨설팅 서비스 제공을 보여 줍니다.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>개인정보 처리방침 링크

조직의 개인 정보 보호 정책에 웹 주소(URL)를 입력합니다. 귀하의 제안이 개인 정보 보호 법률 및 규정을 준수하는지 확인하십시오. 또한 웹사이트에 유효한 개인 정보 보호 정책을 게시해야 합니다.

### <a name="useful-links"></a>유용한 링크

오퍼에 대한 추가 온라인 문서를 제공합니다. 링크를 추가하려면 **+ 링크 추가를** 선택한 다음 다음 필드를 완료합니다.

- **이름** - 고객은 세부 정보 페이지에 이름을 볼 수 있습니다.
- **링크(URL)** – 고객이 온라인 문서를 볼 수 있도록 링크를 입력합니다.

### <a name="customer-support-links"></a>고객 지원 링크

고객이 지원 팀에 연락할 수 있는 지원 웹 사이트를 제공합니다.

- Azure 글로벌 지원 웹 사이트
- Azure 정부 지원 웹 사이트

### <a name="partner-support-contact"></a>파트너 지원 연락처

고객이 지원 티켓을 열 때 사용할 Microsoft 파트너에 대한 연락처 정보를 제공합니다. 이 목록에 는 나열되지 않습니다.

- 이름
- 메일
- Phone

### <a name="engineering-contact"></a>엔지니어링 연락처

인증 문제를 포함하여 오퍼에 문제가 있는 경우 Microsoft에서 사용할 연락처 정보를 제공합니다. 이 목록에 는 나열되지 않습니다.

- 이름
- 메일
- Phone

### <a name="marketplace-media"></a>마켓플레이스 미디어

오퍼와 함께 사용할 로고와 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다. 이미지가 흐리면 제출이 거부됩니다.

>[!Note]
>파일을 업로드하는 데 문제가 있는 경우 로컬 네트워크가 파트너 https://upload.xboxlive.com 센터에서 사용하는 서비스를 차단하지 않는지 확인합니다.

#### <a name="marketplace-logos"></a>마켓플레이스 로고

다음 네 픽셀 크기로 오퍼 로고의 PNG 파일을 제공합니다.

- **소형(48** x 48)
- **미디엄(90** x 90)
- **라지** (216 x 216)
- **Wide** 와이드(255 x 115)

네 개의 로고가 모두 필요하며 마켓플레이스 목록의 다른 위치에서 사용됩니다.

#### <a name="screenshots"></a>스크린샷

쿠폰의 작동 방식을 보여주는 스크린샷을 최대 5개까지 추가합니다. 각 스크린샷의 크기와 PNG 형식은 1280 x 720 픽셀이어야 합니다. 스크린샷을 설명하기 위해 캡션을 추가해야 합니다.

#### <a name="videos"></a>동영상

쿠폰을 보여주는 동영상을 최대 5개까지 추가할 수 있습니다. 이러한 서비스는 외부 비디오 서비스에서 호스팅되어야 합니다. 1280 x 720 픽셀 크기로 각 비디오의 이름, 웹 주소 및 동영상의 축소판 PNG 이미지를 입력합니다.

추가 마켓플레이스 목록 리소스는 [마켓플레이스 오퍼 목록에 대한 모범 사례를 참조하세요.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

계속하기 전에 **초안 저장을** 선택합니다.

## <a name="preview"></a>미리 보기

미리 보기 탭에서 더 넓은 마켓플레이스 오디언스에게 라이브로 게시하기 전에 쿠폰의 유효성을 검증하기 위해 제한된 **미리보기 잠재고객을** 선택합니다.

> [!IMPORTANT]
> 미리 보기에서 쿠폰을 확인한 후 **실시간 이동을** 선택하여 상업용 마켓플레이스 공개 대상에게 쿠폰을 게시합니다.

미리 보기 대상은 Azure 구독 ID GUID로 식별되며 각 잠재 고객에 대한 선택적 설명이 함께 표시됩니다. 이러한 필드 중 어느 것도 고객이 볼 수 없습니다. Azure 포털의 구독 페이지에서 Azure 구독 ID를 찾을 수 **있습니다.**

개별적으로(최대 10개) 또는 CSV 파일(최대 100개)을 업로드하여 하나 이상의 Azure 구독 ID를 추가합니다. 이러한 구독 아이디를 추가하면 쿠폰이 라이브로 게시되기 전에 미리 볼 수 있는 사람을 정의할 수 있습니다. 쿠폰이 이미 라이브 인 경우 제안 변경 또는 오퍼에 대한 업데이트를 테스트하기 위해 미리 보기 대상을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상은 비공개 대상과 다릅니다. 미리 보기 대상은 마켓플레이스에 실시간 _게시되기 전에_ 쿠폰에 액세스할 수 있습니다. 쿠폰이 마켓플레이스에 완전히 게시된 후 비공개 잠재고객만 이용할 수 있는 플랜을 포함하여 모든 요금제가 보고 유효성을 검사할 수 있습니다. 개인 대상(요금제 **가격 및 가용성** 탭에 정의)은 특정 요금제에 단독으로 액세스할 수 있습니다.

다음 섹션, 계획 개요로 진행하기 전에 **초안 저장을** 선택합니다.

## <a name="plan-overview"></a>계획 개요

파트너 센터에서 동일한 혜택 내에서 다양한 플랜 옵션을 제공할 수 있습니다. 이러한 계획은 이전에 SCO라고 합니다. 오퍼에는 수익 창출 대상, Azure 지역, 기능 또는 VM 이미지 측면에서 다를 수 있는 하나 이상의 요금제가 필요합니다.

계획을 만든 후 **계획 개요** 탭에 다음이 표시됩니다.

- 계획 이름
- 라이센스 모델
- 잠재고객(공개 또는 비공개)
- 현재 게시 상태
- 사용 가능한 작업

계획 개요에서 사용할 수 있는 작업은 계획의 현재 상태에 따라 다릅니다. 해당 기능은 아래와 같습니다.

- **초안 삭제** – 계획 상태가 초안인 경우
- **판매 계획** 중지 또는 **비공개 잠재고객 동기화** - 계획 상태가 실시간 게시된 경우

### <a name="create-new-plan"></a>새 계획 만들기

+ 상단에 **새 평면도 만들기를** 선택합니다. **새 계획** 대화 상자가 나타납니다.

계획 **ID** 상자에서 이 제안의 각 계획에 대해 고유한 계획 ID를 만듭니다. 이 ID는 제품 웹 주소의 고객에게 표시됩니다. 소문자와 숫자, 대시 또는 밑줄만 사용하고 최대 50자만 사용합니다.

> [!NOTE]
> 계획 ID **만들기를**선택한 후에는 변경할 수 없습니다.

계획 **이름** 상자에 이 계획의 이름을 입력합니다. 고객은 오퍼 내에서 선택할 플랜을 결정할 때 이 이름을 볼 수 있습니다. 각 계획의 차이점을 명확하게 가리키는 고유한 이름을 만듭니다. 예를 들어 Windows **Server를** 사용 하 여 요금제 **종량제**, **BYOL**, **고급**및 **엔터프라이즈**.

**만들기**를 선택합니다.

### <a name="plan-setup"></a>계획 설정

계획 유형에 대한 상위 수준 구성, 다른 계획에서 기술 구성을 다시 사용하는지 여부 및 계획을 사용할 수 있어야 하는 Azure 지역을 설정합니다. 여기에서 선택한 항목은 동일한 계획에 대해 다른 탭에 표시되는 필드를 결정합니다.

#### <a name="reuse-technical-configuration"></a>기술 구성 재사용

동일한 유형의 계획이 두 개 이상 있고 패키지가 동일한 경우 이 **계획에서 다른 계획에서 기술 구성을 다시 사용할**수 있습니다. 이 옵션을 사용하면 이 오퍼에 대해 동일한 유형의 다른 계획 중 하나를 선택하고 기술 구성을 다시 사용할 수 있습니다.

> [!NOTE]
> 다른 계획에서 기술 구성을 다시 사용하면 전체 **기술 구성** 탭이 이 계획에서 사라집니다. 향후 업데이트를 포함하여 다른 계획의 기술 구성 세부 정보도 이 계획에 사용됩니다. 이 계획은 게시된 후에는 이 설정을 변경할 수 없습니다.

#### <a name="azure-regions"></a>Azure 지역

계획은 하나 이상의 Azure 리전에서 사용할 수 있어야 합니다.

Azure **Global** 옵션을 선택하여 상용 마켓플레이스 통합이 있는 모든 공용 Azure 지역의 고객이 플랜을 사용할 수 있도록 합니다. 자세한 내용은 [지리적 가용성 및 통화 지원을](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)참조하십시오.

Azure **정부** 지역에서 플랜을 사용할 수 있도록 하려면 [Azure 정부](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) 옵션을 선택합니다. 이 리전은 미국 연방, 주, 지역 또는 부족 기관의 고객뿐만 아니라 서비스를 제공할 자격이 있는 파트너에 대한 제어된 액세스를 제공합니다. 게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure 정부는 물리적으로 격리된 데이터 센터 및 네트워크를 사용합니다(미국에만 있음).

[Azure 정부에](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)게시하기 전에 특정 끝점이 다를 수 있으므로 환경에서 계획을 테스트하고 유효성을 검사합니다. 계획을 설정하고 테스트하려면 Microsoft Azure 정부 [평가판에서](https://azure.microsoft.com/global-infrastructure/government/request/)평가판 계정을 요청하십시오.

> [!NOTE]
> 계획이 게시되고 특정 Azure 지역에서 사용할 수 있게 되면 해당 지역을 제거할 수 없습니다.

#### <a name="azure-government-certifications"></a>Azure 정부 인증

이 옵션은 **Azure 지역에서** **Azure 정부를** 선택한 경우에만 표시됩니다.

Azure 정부 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 및 CJIS. 이러한 프로그램에 대한 인증을 인식하려면 해당 프로그램을 설명하는 링크를 최대 100개까지 제공할 수 있습니다. 이러한 링크는 프로그램에 직접 리스팅에 대한 링크이거나 자신의 웹 사이트에서 해당 목록에 대한 사용자 준수에 대한 설명으로 연결되는 링크일 수 있습니다. 이러한 링크는 Azure 정부 고객에게만 표시됩니다.

계속하기 전에 **초안 저장을** 선택합니다.

### <a name="plan-listing"></a>계획 목록

여기서 계획의 목록 세부 정보를 구성합니다. 이 탭에는 동일한 오퍼의 요금제 간에 다를 수 있는 특정 정보가 표시됩니다.

#### <a name="plan-name"></a>계획 이름

이 이름은 계획을 만들 때 제공한 이름으로 미리 채워져 있습니다. 이 이름은 이 계획의 제목으로 마켓플레이스에 표시되며 100자로 제한됩니다.

#### <a name="plan-summary"></a>계획 요약

제안이 아닌 플랜에 대한 간략한 요약을 제공합니다. 이 요약은 100자로 제한됩니다.

#### <a name="plan-description"></a>계획 설명

이 소프트웨어 계획이 고유한 이유와 제품 내 의 계획 간의 차이점에 대해 설명합니다. 제안을 설명하지 말고 계획만 설명하십시오. 계획 설명에는 최대 2,000자까지 포함될 수 있습니다.

계속하기 전에 **초안 저장을** 선택합니다.

### <a name="pricing-and-availability"></a>가격 책정 및 가용성

이 탭에서는 다음을 구성합니다.

- 이 플랜을 시장에서 사용할 수 있습니다.
- 시간당 가격
- 모든 사람이 든 특정 고객(비공개 잠재고객)에게만 계획을 표시할지 여부

#### <a name="markets"></a>시장

모든 플랜은 적어도 하나의 시장에서 사용할 수 있어야 합니다. 이 플랜을 구매할 수 있어야 하는 모든 시장 위치에 대한 확인란을 선택합니다(이러한 시장의 사용자는 **[계획 설정에서](#plan-setup)** 선택한 모든 Azure 지역에 여전히 쿠폰을 배포할 수 있음). **세금 송금** 단추는 Microsoft가 판매를 송금하고 대신 세금을 사용하는 국가를 표시합니다. 중국에 게시하는 것은 **무료** 또는 **BYOL(자체 라이선스** 가져오기)인 계획으로 제한됩니다.

이미 미국 달러(USD)로 플랜 가격을 설정하고 다른 시장 위치를 추가한 경우 새 시장의 가격은 현재 환율에 따라 계산됩니다. 게시하기 전에 항상 각 시장의 가격을 검토하십시오. 변경 사항을 저장한 후 **내보내기 가격(xlsx)** 링크를 사용하여 가격을 검토합니다.

시장을 제거하면 활성 배포를 사용하는 해당 시장의 고객은 새 배포를 만들거나 기존 배포를 확장할 수 없습니다. 기존 배포는 영향을 받지 않습니다.

#### <a name="pricing"></a>가격 책정

**라이선스 모델** - **사용량 기반 월별 청구 계획을** 선택하여 이 요금제의 가격을 구성하거나 고객이 기존 라이선스와 함께 이 요금제사용을 사용할 수 있도록 자체 **라이선스를 가져오십시오.**

사용량 기반 월별 청구 요금제의 경우 다음 세 가지 가격 책정 항목 옵션 중 하나를 사용합니다.

- **코어당** – 코어당 가격을 미국 달러(USD)로 제공합니다. 코어 크기당 가격을 계산하고 현재 환율을 사용하여 현지 통화로 변환합니다.
- **코어 크기당** – 코어 크기당 USD가격을 제공합니다. 현재 환율을 사용하여 가격을 현지 통화로 변환합니다.
- **시장 및 핵심 크기별** – 모든 시장의 각 핵심 크기에 대한 가격을 제공합니다. 스프레드시트에서 가격을 가져올 수 있습니다.

> [!NOTE]
> 가격 변경 내용을 저장하여 가격 데이터를 내보낼 수 있습니다. 플랜의 시장 가격이 게시된 후에는 나중에 변경할 수 없습니다. 가격 스프레드시트를 내보내고 각 시장의 가격을 검토하여 게시하기 전에 이러한 가격이 올바른지 확인합니다.

#### <a name="free-trial"></a>평가판

고객에게 1개월 또는 3개월 무료 평가판을 제공할 수 있습니다.

#### <a name="visibility"></a>표시 유형

모든 사람이 볼 수 있도록 각 계획을 디자인하거나 미리 선택한 대상만 디자인할 수 있습니다. Azure 구독 아이디를 사용하여 제한된 대상에 멤버십을 할당합니다.

**공개** - 당신의 계획은 모든 사람이 볼 수 있습니다.

**비공개 잠재고객** – 미리 선택된 잠재고객에게만 계획을 표시합니다. 비공개 계획으로 게시된 후에는 공개 대상을 업데이트하거나 공개로 변경할 수 있습니다. 계획을 공개한 후에는 공개 상태로 유지되어야 합니다. 비공개로 변경할 수 없습니다.

**제한된 잠재고객(Azure 구독 아이디)** – Azure 구독 아이디를 사용하여 이 개인 요금제에 액세스할 수 있는 잠재고객을 할당합니다. 선택적으로 할당된 각 Azure 구독 ID에 대한 설명을 포함합니다. CSV 스프레드시트를 가져오는 경우 구독 아이디를 수동으로 최대 10개 또는 20,000개까지 추가합니다. Azure 구독 아이디는 GUID로 표시되고 문자는 소문자여야 합니다.

> [!NOTE]
> 비공개 또는 제한된 잠재고객은 **미리 보기** 탭에서 정의한 미리 보기 대상과 다릅니다. 미리 보기 대상은 마켓플레이스에 게시된 라이브 _전에_ 오퍼에 액세스할 수 있습니다. 비공개 잠재고객 선택은 특정 계획에만 적용되지만 미리 보기 대상자는 유효성 검사를 위해 모든 계획(비공개 또는 그렇지 않음)을 볼 수 있습니다.

#### <a name="hide-plan"></a>계획 숨기기

가상 시스템이 다른 솔루션 템플릿 또는 관리되는 응용 프로그램을 통해 참조될 때만 간접적으로 사용되어야 하는 경우 이 상자를 선택하여 가상 컴퓨터를 게시하지만 직접 검색하고 검색하는 고객으로부터 가상 컴퓨터를 숨깁니다.

> [!NOTE]
> 숨겨진 계획은 미리 보기 링크를 지원하지 않습니다.

계속하기 전에 **초안 저장을** 선택합니다.

### <a name="technical-configuration"></a>기술 구성

이 계획과 관련된 이미지 및 기타 기술적 속성을 제공합니다. 자세한 내용은 [Azure VM 기술 자산 만들기를](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)참조하십시오.

> [!NOTE]
> **계획 설정** 탭의 다른 계획에서 패키지를 재사용하도록 이 계획을 구성한 경우 이 탭이 표시되지 않습니다.

#### <a name="operating-system"></a>운영 체제

- **운영 체제 제품군** - **윈도우** 또는 **리눅스** 운영 체제에서 선택
- **릴리스** 또는 **공급 업체** - 윈도우 릴리스 또는 리눅스 공급 업체를 선택
- **OS 친화적 인 이름** - 친숙한 운영 체제 이름을 선택합니다. 이 이름은 고객에게 표시됩니다.

#### <a name="recommended-vm-sizes"></a>권장되는 VM 크기

Azure Marketplace에 표시할 권장 가상 컴퓨터 크기를 최대 6개까지 선택합니다.

#### <a name="open-ports"></a>포트 열기

배포된 가상 컴퓨터에서 공용 또는 개인 포트를 엽니다.

#### <a name="storage-option-for-deployment"></a>배포를 위한 저장소 옵션

**디스크 배포 옵션** - 가상 컴퓨터를 사용할 때 사용자가 사용할 수 있는 디스크 배포 종류를 선택합니다. 배포를 관리디스크 배포로만 제한하는 것이 좋습니다.

#### <a name="properties"></a>속성

**가속 네트워킹 지원** - VM이 가속 네트워킹을 지원하는지 [선택합니다.](https://go.microsoft.com/fwlink/?linkid=2124513)

#### <a name="vm-images"></a>VM 이미지

가상 시스템 이미지에 대한 디스크 버전 및 SAS URI를 제공합니다. 각 VM 이미지에 대해 최대 16개의 데이터 디스크를 추가합니다. 지정된 제출에서 계획당 하나의 새 이미지 버전만 제공합니다. 이미지가 게시된 후에는 편집할 수 없지만 삭제할 수는 있습니다. 버전을 삭제하면 새 사용자와 기존 사용자가 모두 삭제된 버전의 새 인스턴스를 배포할 수 없습니다.

- **디스크 버전은** 제공하는 이미지의 버전입니다.
- **SAS URI는** 운영 체제 VHD를 저장한 Azure 저장소의 위치입니다.
- 데이터 디스크 이미지는 Azure 저장소에 저장된 VHD SAS URI도 있습니다.
- 계획에서 제출당 하나의 이미지만 추가합니다.

사용하는 운영 체제에 관계없이 솔루션에 필요한 최소 데이터 디스크 수만 추가합니다. 고객은 배포 시 이미지의 일부인 디스크를 제거할 수 없지만 배포 중이나 배포 후에 언제든지 디스크를 추가할 수 있습니다.

계속하기 전에 **초안 저장을** 선택하고 **계획 개요로**돌아갑니다.

## <a name="resell-through-csps"></a>CSP를 통한 재판매

[CSP(클라우드 솔루션 공급자)](https://azure.microsoft.com/offers/ms-azr-0145p/) 프로그램의 파트너가 사용할 수 있도록 하여 오퍼의 범위를 확장합니다. 모든 BYOL(사용자 라이선스 가져오기) 플랜은 자동으로 옵트인됩니다. BYOL 이외 플랜을 선택할 수 있습니다.

계속하기 전에 **초안 저장을** 선택합니다.

## <a name="test-drive"></a>시험 사용

고객이 제품을 구매하기 전에 제품을 시험해 볼 수 있는 데모(테스트 드라이브)를 설정합니다. 고객이 일정 기간 동안 쿠폰을 시도할 수 있는 데모 환경을 만들려면 [상업용 시장에서 제품을 테스트 드라이브를 참조하세요.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

테스트 드라이브를 활성화하려면 [제안 설정](#test-drive) 탭에서 테스트 드라이브 활성화 확인란을 선택합니다. 오퍼에서 테스트 드라이브를 제거하려면 이 확인란을 선택 취소하십시오.

추가 테스트 드라이브 리소스:

- 기술 모범 사례
- 마케팅 모범 사례
- 테스트 드라이브 개요(PDF; 팝업 차단이 꺼져 있는지 확인).

계속하기 전에 **초안 저장을** 선택합니다.

## <a name="review-and-publish"></a>검토 및 게시

오퍼의 모든 필수 섹션을 완료한 후 제출하여 검토하고 게시할 수 있습니다.

포털의 오른쪽 상단 모서리에서 **검토 및 게시를**선택합니다.

이 페이지에서는

- 오퍼의 각 섹션에 대한 완료 상태를 참조하십시오.
  - **시작되지 않음** - 섹션이 시작되지 않았으며 완료해야 합니다.
  - **불완전** - 섹션에 수정해야 하거나 추가 정보를 제공해야 하는 오류가 있습니다. 이 섹션을 업데이트하는 방법에 대한 지침은 이 문서의 이전 섹션을 참조하십시오.
  - **완료** - 섹션이 완료되었으며 오류가 없습니다. 오퍼를 제출하려면 먼저 오퍼의 모든 섹션이 완료되어야 합니다.
- **인증 참고 사항** - 인증 팀에 테스트 지침을 제공하여 앱이 올바르게 테스트되었는지 확인합니다. 앱을 이해하는 데 도움이 되는 추가 메모를 제공합니다.

게시를 위한 제안을 제출하려면 **검토 및 게시를**선택합니다.

제안의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일이 전송됩니다. 쿠폰을 공개(또는 비공개 잠재고객에게 제공하는 경우)에 게시하려면 파트너 센터로 이동하여 오퍼의 **개요** 페이지를 찾은 다음 라이브 로 **이동을**선택합니다. 게시가 진행 중일 때 쿠폰 상태가 페이지 상단에 표시됩니다.

### <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

게시 프로세스의 **수동 유효성 검사** 단계는 오퍼및 관련 기술 자산을 광범위하게 검토한 것입니다. 이 프로세스에서 오퍼의 오류를 발견하면 문제를 자세히 설명한 인증 보고서를 받게 됩니다. 필요한 수정을 하고 오퍼를 다시 게시하기만 하면 됩니다.

## <a name="offer-overview"></a>제품 개요

**제안 개요** 페이지에는 이 제안을 게시하는 데 필요한 단계(완료 및 진행 중)와 각 단계를 완료하는 데 걸리는 시간이 시각적으로 표시됩니다.

이 페이지에는 선택한 항목을 기반으로 이 오퍼에서 작업을 수행하는 링크가 포함되어 있습니다. 예를 들어:

- 제안이 초안인 경우 - [초안 오퍼 삭제](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- 오퍼가 실시간 인 경우 - [오퍼 판매 중지](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- 제안이 미리 보기인 경우 - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- 게시자 사인오프를 완료하지 않은 경우 - [게시 취소](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>마켓플레이스 예제

다음 예제에서는 Azure 마켓플레이스에 오퍼가 표시되는 방식을 보여 주며

### <a name="azure-marketplace-offer-details"></a>Azure 마켓플레이스 제공 세부 정보

![Azure 마켓플레이스 제공 세부 정보 화면 예제](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure 마켓플레이스 검색 결과

![Azure 마켓플레이스 검색 세부 정보 화면 예제](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Azure 마켓플레이스 계획 세부 정보

![Azure 마켓플레이스 계획 세부 정보 화면 예제](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Azure 포털 제공 세부 정보

![Azure 포털 제공 세부 정보 화면 예제](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure 포털 검색 결과

![Azure 포털 검색 결과 화면 예제](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Azure 포털 계획 세부 정보

![Azure 포털 계획 세부 정보 화면 예제](media/avm-create6.png)

## <a name="next-step"></a>다음 단계

- [상용 시장에서 기존 오퍼 업데이트](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
