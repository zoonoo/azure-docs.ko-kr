---
title: Azure 애플리케이션 제품 속성 구성 방법
description: 파트너 센터(Azure Marketplace)에서 Azure 애플리케이션 제품의 속성을 구성하는 방법을 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: fb5b8488585555ec25f9bf437ed1166f8d2687c3
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542856"
---
# <a name="configure-azure-application-offer-properties"></a>Azure 애플리케이션 제공 속성 구성

이 문서에서는 상업용 Marketplace에서 Azure 애플리케이션 제품의 속성을 구성하는 방법을 설명합니다.

**속성** 페이지에서 제품 및 법적 계약서에 적용되는 범주를 정의합니다. 제품이 적절히 표시되고 올바른 고객 그룹에게 제안되도록 이 페이지에서 제품에 대한 완전하고 정확한 세부 정보를 제공해야 합니다.

## <a name="select-a-category-for-your-offer"></a>제품에 대한 범주 선택

**범주** 에서 **범주** 링크를 선택하고 제품을 적절한 상업용 Marketplace 검색 영역으로 그룹화하기 위한 범주를 하나 이상 선택합니다. 각 기본 및 보조 범주에 최대 두 개의 하위 범주를 선택합니다. 제안에 적용되는 하위 범주가 없으면 **해당 없음** 을 선택합니다.

## <a name="provide-terms-and-conditions"></a>사용 약관 만들기

**법률 정보** 에서 제품에 대한 사용 약관을 제공합니다. 다음과 같은 두 가지 옵션이 있습니다.

- [수정안 옵션이 있는 표준 계약 사용](#use-the-standard-contract)
- [사용자 고유의 사용 약관 사용](#use-your-own-terms-and-conditions)

표준 계약에 대한 자세한 내용은 [Microsoft 상업용 Marketplace의 표준 계약](standard-contract.md)을 참조하세요. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) PDF를 다운로드할 수 있습니다(팝업 차단이 해제되어 있는지 확인).

### <a name="use-the-standard-contract"></a>표준 계약 사용

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상업용 Marketplace에서 제품에 사용할 수 있는 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 이 계약을 한 번 읽고 동의하기만 하면 되고 사용자 지정 사용 약관을 만들 필요는 없습니다.

1. **Microsoft의 상업용 마켓플레이스의 표준 계약 사용** 확인란을 선택합니다.

   ![Microsoft의 상업용 마켓플레이스의 표준 계약 사용 확인란을 보여줍니다.](partner-center-portal/media/use-standard-contract.png)

1. **확인** 대화 상자에서 **동의** 를 선택합니다. 이 필터를 찾기 위해 위로 스크롤해야 할 수도 있습니다.
1. 계속하기 전에 **초안 저장** 을 선택합니다.

> [!NOTE]
> 상업용 Marketplace 표준 계약을 사용하여 제품이 게시되면 사용자 고유의 사용 약관을 사용할 수 없습니다. 수정안 옵션이 있는 표준 계약 또는 사용자 고유의 사용 약관에 따라 솔루션을 제공합니다.

### <a name="add-amendments-to-the-standard-contract-optional"></a>표준 계약에 수정안 추가(선택 사항)

사용 가능한 수정계약에는 _범용_ 및 _사용자 지정_ 의 두 가지 종류가 있습니다.

#### <a name="add-universal-amendment-terms"></a>범용 수정계약 사용 약관 추가

**Microsoft의 상업용 Marketplace 표준 계약에 대한 범용 수정계약 조건** 상자에서 범용 수정계약 사용 약관을 입력합니다. 이 상자에는 문자를 개수 제한 없이 입력할 수 있습니다. 이러한 조건은 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에게 표시됩니다.

#### <a name="add-one-or-more-custom-amendments"></a>하나 이상의 사용자 지정 수정안 추가

1. **Microsoft의 상업용 마켓플레이스 표준 계약에 대한 사용자 지정 수정안 사용 약관** 에서 **사용자 지정 수정안 사용 약관 추가(최대 10개)** 링크를 선택합니다.
1. **사용자 지정 수정안 사용 약관** 상자에 수정안 사용 약관을 입력합니다.
1. **테넌트 ID** 상자에 테넌트 ID를 입력합니다. 이러한 사용자 지정 사용 약관에 대해 지정한 테넌트 ID의 고객만 Azure Portal의 제품 구매 흐름에서 이러한 약관을 확인할 수 있습니다.

   > [!TIP]
   > 테넌트 ID는 Azure에서 고객을 식별합니다. 게시자는 고객에게 이 ID를 요청할 수 있으며 고객은 [ **https://portal.azure.com**](https://portal.azure.com) > **Azure Active Directory** > **속성** 으로 이동하여 ID를 확인할 수 있습니다. 디렉터리 ID 값은 테넌트 ID(예: `50c464d3-4930-494c-963c-1e951d15360e`)입니다. [내 Microsoft Azure 및 Office 365 테넌트 ID는 무엇인가요?](https://www.whatismytenantid.com/)에서 도메인 이름 URL을 사용하여 고객의 조직 테넌트 ID를 조회할 수도 있습니다.

1. **설명** 상자에 테넌트 ID에 대한 간단한 설명을 입력합니다(선택 사항). 이 설명은 수정안으로 대상 고객을 식별하는 데 도움이 됩니다.
1. 다른 테넌트 ID를 추가하려면 **고객의 테넌트 ID 추가** 링크를 선택하고 3단계와 4단계를 반복합니다. 최대 20개의 테넌트 ID를 추가할 수 있습니다.
1. 다른 수정안 사용 약관을 추가하려면 1 ~ 5단계를 반복합니다. 제품당 최대 10개의 사용자 지정 수정안 사용 약관을 제공할 수 있습니다.
1. 계속하기 전에 **초안 저장** 을 선택합니다.

### <a name="use-your-own-terms-and-conditions"></a>사용자 고유의 사용 약관 사용

표준 계약 대신 사용자 고유의 사용 약관을 제공하도록 선택할 수 있습니다. 고객은 이 약관에 동의해야 제품을 사용할 수 있습니다.

1. **법률 정보** 에서 **Microsoft의 상업용 마켓플레이스에 대한 표준 계약 사용** 확인란의 선택을 취소 했는지 확인합니다.
1. **사용 약관** 상자에 최대 1만 문자 텍스트를 입력합니다.
1. 다음 탭인 제품 목록으로 이동하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 목록 세부 정보 구성](azure-app-offer-listing.md)
