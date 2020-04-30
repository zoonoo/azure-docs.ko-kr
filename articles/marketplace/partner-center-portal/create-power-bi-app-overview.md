---
title: Power BI 앱 만들기 개요-Azure Marketplace
description: 이 문서에서는 Microsoft AppSource에 Power BI 앱을 게시 하기 위한 개략적인 단계에 대해 설명 합니다. 상업적 marketplace에 게시 하기 위해 Power BI 앱에서 충족 해야 하는 기술 및 비즈니스 요구 사항도 제공 됩니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 8f050a43cbdf4ab29df55cd5526eb231c301b271
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732674"
---
# <a name="power-bi-app-creation-overview"></a>Power BI 앱 만들기 개요

> [!IMPORTANT]
> Power BI 앱 제품의 관리를 Cloud 파트너 포털에서 파트너 센터로 전환 하 고 있습니다. 제품을 마이그레이션할 때까지 Cloud 파트너 포털에 대 한 [앱 제안 Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) 의 지침에 따라 제품을 관리 하세요.

이 문서에서는 Power BI 앱을 Microsoft [Appsource](https://appsource.microsoft.com/)에 게시 하는 방법을 설명 합니다. Power BI 앱은 데이터 집합, 보고서 및 대시보드를 포함 하 여 사용자 지정 가능한 콘텐츠를 패키지 합니다. 그런 다음 AppSource를 사용 하 여 다른 Power BI 플랫폼과 앱을 사용 하 고 개발자가 허용 하는 조정 및 사용자 지정을 수행 하 고 자신의 데이터에 연결할 수 있습니다.

## <a name="publishing-benefits"></a>게시 이점

상용 marketplace에 게시할 경우의 이점:

- Microsoft 브랜드를 사용 하 여 회사를 홍보 하세요.
- Azure Marketplace를 통해 AppSource 및 20만 조직에서 1억 개 이상의 Office 365 및 Dynamics 365 사용자에 게 도달할 수 있습니다.
- 이러한 마켓플레이스에서 고품질 리드를 받습니다.
- Microsoft 필드 및 전화 영업 팀이 서비스를 승격 합니다.

## <a name="overview"></a>개요

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Power BI 앱을 게시 하는 단계 개요" border="false":::

키 게시 단계는 다음과 같습니다.

1. Power BI에서 응용 프로그램을 만듭니다. 제품에 대 한 주요 기술 자산은 패키지 설치 링크를 받게 됩니다. 파트너 센터에서 제품을 만들기 전에 사전 프로덕션으로 테스트 패키지를 보냅니다. 자세한 내용은 [Power BI 앱 이란?](https://docs.microsoft.com/power-bi/service-template-apps-overview)을 참조 하세요.
2. 공식 이름, 설명 및 로고와 같은 마케팅 자료를 추가 합니다.
3. 사용 약관, 개인 정보 취급 방침, 지원 정책 및 사용자 도움말과 같은 제품의 법률 및 지원 문서를 포함 합니다.
4. 제품 만들기: 파트너 센터를 사용 하 여 제품 설명, 마케팅 자료, 법적 정보, 지원 정보 및 자산 사양을 비롯 한 세부 정보를 편집 합니다.
5. 게시를 위해 제출 합니다.
6. 파트너 센터에서 프로세스를 모니터링 합니다. 여기서 AppSource 온 보 딩 팀은 앱을 테스트 하 고, 유효성을 검사 하 고, 인증 합니다.
7. 인증 된 후에는 해당 테스트 환경에서 앱을 검토 하 고 릴리스 합니다. 그러면 AppSource ("라이브")에 나열 됩니다.
8. Power BI에서 패키지를 프로덕션 환경으로 보냅니다. 자세한 내용은 [Power BI 앱 릴리스 관리](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

템플릿, 팁 및 샘플을 제공 하는 아래 링크를 검토 합니다.

- [Power BI 앱 만들기](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Power BI 앱 제작을 위한 팁](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [샘플](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>요구 사항

상용 marketplace에 게시 하려면 Power BI 앱 제품이 다음 기술 및 비즈니스 요구 사항을 충족 해야 합니다.

### <a name="technical-requirements"></a>기술적인 요구 사항

필요한 주요 기술 자산은 [Power BI 앱](https://go.microsoft.com/fwlink/?linkid=2028636)입니다. 이는 기본 데이터 집합, 보고서 또는 대시보드의 모음입니다. 또한 이전에 [콘텐츠 팩](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction)으로 알려진 선택적인 연결 된 서비스 및 포함 된 데이터 집합을 포함 합니다. 이러한 유형의 앱을 개발 하는 방법에 대 한 자세한 내용은 [Power BI 앱 이란?](https://go.microsoft.com/fwlink/?linkid=2028636)을 참조 하세요.

#### <a name="get-an-installation-web-address"></a>설치 웹 주소 가져오기

[Power BI](https://powerbi.microsoft.com/) 환경 내 에서만 Power BI 앱을 빌드할 수 있습니다.

1. [Power BI Pro 라이선스](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)를 사용 하 여 로그인 합니다.
2. Power BI에서 앱을 만들고 테스트 합니다.
3. 앱 설치 웹 주소를 받으면 파트너 센터의 **기술 구성** 페이지에 추가 합니다.

Power BI에서 앱을 만들고 테스트 한 후에는 응용 프로그램 설치 웹 주소를 저장 하 여 [Power BI 앱 제품을 만드는](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)데 필요 합니다.

### <a name="business-requirements"></a>비즈니스 요구 사항

비즈니스 요구 사항에는 절차적, 계약 및 법적 의무가 포함 됩니다. 다음이 필요합니다.

- 등록 된 상용 마켓플레이스 게시자 여야 합니다. 등록 되지 않은 경우에는 [상업적 마켓플레이스 게시자가 되기](https://docs.microsoft.com/azure/marketplace/become-publisher)위한 단계를 수행 하세요.
- AppSource에 나열 되는 제품에 대 한 기준을 충족 하는 콘텐츠를 제공 합니다. 자세한 내용은 [AppSource에서 앱을 나열할 수 있나요?를 참조 하세요. 방법은 다음과 같습니다](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how).
- [Microsoft 개인 정보 취급 방침](https://privacy.microsoft.com/privacystatement)에 동의 하 고 다음을 수행 합니다.

## <a name="next-steps"></a>다음 단계

- [파트너 센터에서 Power BI 앱 제안 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)