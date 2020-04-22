---
title: Power BI 앱 만들기 개요 - Azure 마켓플레이스
description: 이 문서에서는 Power BI 앱을 Microsoft AppSource에 게시하기 위한 상위 단계에 대해 설명합니다. 상용 시장에 게시하기 위해 Power BI 앱이 충족해야 하는 기술 및 비즈니스 요구 사항도 제공됩니다.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 8f050a43cbdf4ab29df55cd5526eb231c301b271
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732674"
---
# <a name="power-bi-app-creation-overview"></a>Power BI 앱 생성 개요

> [!IMPORTANT]
> Power BI 앱 의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 [Power BI 앱 의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-power-bi-offer) 지침에 따라 클라우드 파트너 포털에서 오퍼를 관리하십시오.

이 문서에서는 Power BI 앱을 Microsoft [AppSource](https://appsource.microsoft.com/)에 게시하는 방법을 설명합니다. Power BI 앱은 데이터 집합, 보고서 및 대시보드를 포함하여 사용자 지정 가능한 콘텐츠를 패키지화합니다. 그런 다음 AppSource를 사용하여 다른 Power BI 플랫폼에서 앱을 사용하고 개발자가 허용하는 조정 및 사용자 지정을 수행하고 자신의 데이터에 연결할 수 있습니다.

## <a name="publishing-benefits"></a>게시 이점

상업 시장에 게시의 이점:

- Microsoft 브랜드를 사용하여 회사를 홍보합니다.
- 잠재적으로 Azure Marketplace를 통해 AppSource의 1억 명 이상의 Office 365 및 Dynamics 365 사용자 및 200,000개 이상의 조직에 도달할 수 있습니다.
- 이러한 마켓플레이스에서 고품질 잠재 고객을 받으십시오.
- Microsoft 필드 및 원격 판매 팀에서 서비스를 승격합니다.

## <a name="overview"></a>개요

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Power BI 앱을 게시하는 단계 개요" border="false":::

다음은 주요 게시 단계입니다.

1. Power BI에서 응용 프로그램을 만듭니다. 오퍼의 주요 기술 자산인 패키지 설치 링크를 받게 됩니다. 파트너 센터에서 오퍼를 만들기 전에 테스트 패키지를 사전 프로덕션으로 보냅니다. 자세한 내용은 [Power BI 앱이란 무엇입니까?](https://docs.microsoft.com/power-bi/service-template-apps-overview)
2. 공식 이름, 설명 및 로고와 같은 마케팅 자료를 추가합니다.
3. 이용 약관, 개인 정보 보호 정책, 지원 정책 및 사용자 도움말과 같은 제품의 법률 및 지원 문서를 포함합니다.
4. 오퍼 만들기: 파트너 센터를 사용하여 제안 설명, 마케팅 자료, 법률 정보, 지원 정보 및 자산 사양을 비롯한 세부 정보를 편집합니다.
5. 게시를 위해 제출합니다.
6. AppSource 온보딩 팀이 앱을 테스트하고 유효성을 검사하고 인증하는 파트너 센터에서 프로세스를 모니터링합니다.
7. 인증이 끝나면 테스트 환경에서 앱을 검토하고 릴리스합니다. 이것은 AppSource에 나열됩니다 (그것은 "라이브 간다").
8. Power BI에서 패키지를 프로덕션으로 보냅니다. 자세한 내용은 [Power BI 앱 릴리스 관리를](https://docs.microsoft.com/power-bi/service-template-apps-create#manage-the-template-app-release)참조하십시오.

## <a name="before-you-begin"></a>시작하기 전에

템플릿, 팁 및 샘플을 제공하는 아래 링크를 검토합니다.

- [Power BI 앱 만들기](https://docs.microsoft.com/power-bi/service-template-apps-create)
- [Power BI 앱 작성팁](https://docs.microsoft.com/power-bi/service-template-apps-tips)
- [샘플](https://docs.microsoft.com/power-bi/service-template-apps-samples)

## <a name="requirements"></a>요구 사항

상용 마켓플레이스에 게시하려면 Power BI 앱 오퍼가 다음 기술 및 비즈니스 요구 사항을 충족해야 합니다.

### <a name="technical-requirements"></a>기술적인 요구 사항

필요한 주요 기술 자산은 [Power BI 앱입니다.](https://go.microsoft.com/fwlink/?linkid=2028636) 기본 데이터 집합, 보고서 또는 대시보드의 컬렉션입니다. 또한 이전에 [콘텐츠 팩이라고](https://docs.microsoft.com/power-bi/service-organizational-content-pack-introduction)하는 선택적 연결된 서비스 및 임베디드 데이터 집합도 포함됩니다. 이러한 유형의 앱 개발에 대한 자세한 내용은 [Power BI 앱이란 무엇입니까?](https://go.microsoft.com/fwlink/?linkid=2028636)

#### <a name="get-an-installation-web-address"></a>설치 웹 주소 받기

Power BI 환경 내에서만 Power [BI](https://powerbi.microsoft.com/) 앱을 빌드할 수 있습니다.

1. Power BI [Pro 라이선스로 로그인합니다.](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)
2. Power BI에서 앱을 만들고 테스트합니다.
3. 앱 설치 웹 주소를 받으면 파트너 센터의 **기술 구성** 페이지에 추가합니다.

Power BI에서 앱을 만들고 테스트한 후 [Power BI 앱 오퍼를 만드는](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)데 필요한 응용 프로그램 설치 웹 주소를 저장합니다.

### <a name="business-requirements"></a>비즈니스 요구 사항

비즈니스 요구 사항에는 절차, 계약 및 법적 의무가 포함됩니다. 다음이 필요합니다.

- 등록된 상업용 마켓플레이스 퍼블리셔가 되시다. 등록하지 않은 경우 [상업용 마켓플레이스 게시자 되기](https://docs.microsoft.com/azure/marketplace/become-publisher)의 단계를 따르십시오.
- AppSource에 나열될 오퍼링의 기준을 충족하는 콘텐츠를 제공합니다. 자세한 내용은 [AppSource에 나열할 앱이 있습니까? 방법은 다음과 같습니다.](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)
- [Microsoft 개인 정보 보호 정책에](https://privacy.microsoft.com/privacystatement)동의하고 따르십시오.

## <a name="next-steps"></a>다음 단계

- [파트너 센터에서 Power BI 앱 오퍼 만들기](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer)