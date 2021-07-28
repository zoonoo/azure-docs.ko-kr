---
title: Azure Marketplace의 컨테이너 제품 게시 가이드
description: 이 문서에서는 Azure Marketplace에서 컨테이너 제품을 게시하기 위한 요구 사항을 설명합니다.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 43c569204f879e865eb5bd4f040c96e907e63abb
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144712"
---
# <a name="plan-an-azure-container-offer"></a>Azure 컨테이너 제품 계획

Azure 컨테이너 제품을 통해 컨테이너 이미지를 Azure Marketplace에 게시할 수 있습니다. 이 가이드를 사용하여 이 제품 유형에 대한 요구 사항을 이해합니다.

Azure 컨테이너 제품은 Azure Marketplace를 통해 배포되고 청구되는 트랜잭션 제품입니다. 사용자에게 표시되는 목록 옵션은 **지금 가져오기** 입니다.

솔루션이 Kubernetes 기반 Azure Container 인스턴스로 설정된 Docker 컨테이너 이미지인 경우 Azure Container 제품 유형을 사용합니다.

> [!NOTE]
> Azure Container 인스턴스는 가상 머신을 관리하거나 더 높은 수준의 서비스를 채택하지 않고도 Azure에서 컨테이너를 실행하는 가장 빠르고 간단한 방법을 제공하는 런타임 Docker 인스턴스입니다. 컨테이너 인스턴스는 Azure에 직접 배포하거나 Azure Kubernetes Service 또는 Azure Kubernetes Service 엔진을 통해 오케스트레이션할 수 있습니다.  

## <a name="licensing-options"></a>라이선싱 옵션

Azure Container 제품에 사용할 수 있는 라이선싱 옵션은 다음과 같습니다.

| 라이선싱 옵션 | 트랜잭션 프로세스 |
| --- | --- |
| 무료 | 고객에게 평가판으로 제공하는 제품을 나열합니다. |
| BYOL | 사용자 라이선스 필요 옵션을 사용하면 고객이 기존 소프트웨어 라이선스를 Azure로 가져올 수 있습니다.\* |
|

\* 게시자는 주문, 처리, 계량, 청구, 송장, 결제, 수금을 포함하지만 이에 제한되지 않는 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원합니다.

## <a name="customer-leads"></a>잠재 고객

파트너 센터를 통해 제품을 상업용 Marketplace에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 시험 사용을 하도록 설정하려면 CRM에 연결해야 합니다. 그러지 않으면 CRM에 연결하는 것은 선택 사항입니다. 파트너 센터는 Azure 테이블, Dynamics 365 Customer Engagement, HTTPS 엔드포인트, Marketo 및 Salesforce를 지원합니다.

## <a name="legal-contracts"></a>법적 계약

고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상업용 Marketplace에서 제품에 사용할 수 있는 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 이 계약을 한 번 읽고 동의하기만 하면 되므로 사용자 지정 계약조건을 만들 필요가 없습니다.

표준 계약 대신 사용자 고유의 계약조건을 제공하도록 선택할 수 있습니다. 고객은 이 약관에 동의해야 제품을 사용할 수 있습니다.

## <a name="offer-listing-details"></a>제품 목록 세부 정보

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다”라는 문구로 시작하는 경우 제품 목록 내용을 영어로 작성하지 않아도 됩니다.

제품을 보다 쉽게 만들 수 있도록 관련 항목 중 일부는 미리 준비합니다. 명시된 경우를 제외하고 모두 필요합니다.

- **이름** - 이 이름은 상업용 Marketplace에서 제품 목록의 제목으로 표시됩니다. 이름은 상표로 등록될 수 있습니다. 상표 및 저작권 기호가 아닌 경우 이모지를 포함할 수 없으며 50자로 제한됩니다.
- **검색 결과 요약** - 줄 바꿈 없이 100자 이하의 한 문장으로 제품의 목적 또는 기능을 설명합니다. 이 요약문은 상업용 Marketplace 목록 검색 결과에 사용됩니다.
- **간단한 설명** - 줄 바꿈이 없는 일반 텍스트로 작성된 제품의 용도 또는 기능에 대한 세부 정보입니다. 이는 제품의 세부 정보 페이지에 표시됩니다.
- **설명** - 이 설명은 상업용 Marketplace 목록 개요에 표시됩니다. 가치 제안, 주요 이점, 대상 사용자 베이스, 범주 또는 산업 협회, 앱 내 구매 기회, 필요한 정보를 공개하고 자세한 정보를 볼 수 있는 링크를 포함하는 것이 좋습니다. 이 텍스트 상자에는 보다 설득력 있는 설명을 도와주는 다양한 텍스트 편집기 컨트롤이 들어 있습니다. 필요에 따라 서식을 지정하기 위해 HTML 태그를 사용합니다.
- **개인정보처리방침** – 회사의 개인정보처리방침에 대한 URL입니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인할 책임은 본인에게 있습니다.
- **유용한 링크**(선택 사항): 제품의 사용자를 위한 다양한 리소스에 대한 링크입니다. 예를 들어 포럼, FAQ, 릴리스 정보 등이 있습니다.
- **연락처 정보**
  - **고객 지원팀 연락처** - 고객이 티켓을 열 때 사용할 Microsoft 파트너의 이름, 전화 번호 및 이메일입니다. 고객 지원팀 웹 사이트의 URL도 포함해야 합니다.
  - **엔지니어링 연락처** - 제품에 문제가 있을 때 Microsoft에서 직접 사용할 수 있는 이름, 전화 번호 및 이메일입니다. 이 연락처 정보는 상업용 Marketplace에 표시되지 않습니다.
  - **CSP 프로그램 연락처**(선택 사항): CSP 프로그램을 옵트인하는 경우 이름, 전화 번호 및 이메일을 제공합니다. 그러면 해당 파트너가 질문이 있을 때 연락할 수 있습니다. 마케팅 자료의 URL을 포함할 수도 있습니다.
- **미디어**
    - **로고** - **큰** 로고에 대한 PNG 파일입니다. 파트너 센터는 이를 사용하여 기타 필요한 로고 크기를 만듭니다. 원한다면 나중에 다른 이미지로 바꿀 수 있습니다.
    - **스크린샷** - 제품의 작동 방식을 보여 주는 1~5개의 스크린샷입니다. 이미지는 PNG 형식에 1280 x 720 픽셀이며 캡션을 포함해야 합니다.
    - **동영상**(선택 사항) - 제품을 설명하는 동영상을 4개까지 추가할 수 있습니다. 이름, YouTube 또는 Vimeo URL 및 1280 x 720 픽셀 PNG 썸네일을 포함합니다.

> [!Note]
> 제품은 상업용 Marketplace에 게시되는 일반 [상업용 Marketplace 인증 정책을](/legal/marketplace/certification-policies#100-general) 충족해야 합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

미리 보기 대상 그룹은 제품이 온라인 스토어에 라이브로 게시되기 전에 제품에 액세스하여 엔드투엔드 기능을 테스트할 수 있습니다. **미리 보기 대상 그룹** 페이지에서 제한된 미리 보기 대상 그룹을 정의할 수 있습니다.

Azure 구독 ID에 초대를 보낼 수 있습니다. 최대 10개의 ID를 수동으로 추가하거나 .csv 파일로 최대 100개를 가져옵니다. 제품이 이미 출시된 경우에도 제품의 변경 내용 또는 업데이트 내용을 테스트하기 위한 미리 보기 대상 그룹을 정의할 수 있습니다.

## <a name="plans-and-pricing"></a>플랜 및 가격 책정

컨테이너 제품에는 하나 이상의 플랜이 필요합니다. 플랜은 솔루션 범위 및 한도를 정의합니다. 제품에 대한 여러 플랜을 만들어 고객에게 다양한 기술 및 라이선스 옵션을 제공할 수 있습니다. 

컨테이너는 두 가지 라이선스 모델을 지원합니다. 무료 또는 BYOL(사용자 라이선스 필요)입니다. BYOL은 사용자가 고객에게 직접 요금을 청구하고 Microsoft는 아무런 요금도 청구하지 않습니다. Microsoft는 Azure 인프라 사용 요금을 통해서만 전달합니다. 자세한 내용은 [상업용 Marketplace 거래 기능](marketplace-commercial-transaction-capabilities-and-considerations.md)을 참조하세요.

## <a name="additional-sales-opportunities"></a>추가 영업 기회

Microsoft에서 지원하는 마케팅 및 판매 채널을 옵트인하도록 선택할 수 있습니다. 파트너 센터에서 제품을 만들 때 프로세스 마지막 부분에서 다음과 같은 두 개의 탭이 표시됩니다.

- **CSP를 통한 재판매** – Microsoft CSP(클라우드 솔루션 공급자) 파트너가 솔루션을 번들 제품의 일부로 재판매할 수 있습니다. 이 프로그램에 대한 자세한 내용은 [클라우드 솔루션 공급자 프로그램](cloud-solution-providers.md)을 참조하세요.
- **Microsoft와 공동 판매** - Microsoft 영업 팀이 고객의 요구 사항을 평가할 때 IP 공동 판매 적합 솔루션을 고려합니다. 공동 판매 자격에 대한 자세한 내용은 [공동 판매 상태의 요구 사항](/legal/marketplace/certification-policies)을 참조하세요. 제품 평가 준비에 대한 자세한 내용은 [파트너 센터의 공동 판매 옵션](./co-sell-configure.md)을 참조하세요.

## <a name="container-offer-requirements"></a>컨테이너 제품 요구 사항

| 요구 사항 | 세부 정보 |  
|:--- |:--- |  
| 청구 및 계량 | 무료 또는 BYOL 청구 모델을 지원합니다. |
| Dockerfile에서 작성된 이미지 | 컨테이너 이미지는 Docker 이미지 사양을 기반으로 해야 하며 Dockerfile에서 작성합니다. Docker 이미지 작성에 대한 자세한 내용은 [Dockerfile 참조](https://docs.docker.com/engine/reference/builder/#usage)의 "사용법" 섹션을 참조하세요. |
| Azure Container Registry 리포지토리에서 호스팅 | 컨테이너 이미지는 ACR(Azure Container Registry) 리포지토리에서 호스팅되어야 합니다. Azure Container Registry 작업에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 프라이빗 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-portal.md)를 참조하세요.<br><br> |
| 이미지 태그 지정 | 컨테이너 이미지는 태그를 하나 이상 포함해야 합니다(최대 태그 수: 16). 이미지 태그 지정에 대한 자세한 내용은 [Docker 설명서](https://docs.docker.com/engine/reference/commandline/tag) 사이트의 `docker tag` 페이지를 참조하세요. |

## <a name="next-steps"></a>다음 단계

- [기술 자산 준비](azure-container-technical-assets.md)