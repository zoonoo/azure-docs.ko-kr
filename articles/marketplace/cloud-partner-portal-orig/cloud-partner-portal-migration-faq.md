---
title: 파트너 센터로의 마이그레이션에 대해 자주 묻는 질문 | Azure 마켓플레이스
description: 이 문서에서는 클라우드 파트너 포털에서 파트너 센터로의 오퍼 마이그레이션에 대해 자주 묻는 질문을 다룹니다.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mingshen
ms.openlocfilehash: 672153eba4aa2b739b67694f939c4796b39ac4c6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274381"
---
# <a name="frequently-asked-questions-for-migrating-from-the-cloud-partner-portal-to-partner-center"></a>클라우드 파트너 포털에서 파트너 센터로 마이그레이션하기 위해 자주 묻는 질문

이 문서에서는 클라우드 파트너 포털에서 파트너 센터로의 오퍼 마이그레이션에 대해 자주 묻는 질문을 다룹니다.

## <a name="what-does-offer-migration-mean"></a>제공 마이그레이션은 무엇을 의미합니까?

오퍼 게시 및 관리 환경이 변경되어 클라우드 파트너 포털에서 파트너 센터로 오퍼 데이터를 이동하고 있습니다.

| 영역  | 변경  |
|-------|----------|
| **게시 및 제공 관리 경험** | 파트너 센터의 직관적인 인터페이스를 통해 향상된 사용자 환경을 제공합니다. 자세한 내용은 [파트너 센터와 클라우드 파트너 포털간의 차이점을 참조하세요.](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **마켓플레이스에서 오퍼의 가용성** | 변경 내용이 없습니다. 오퍼가 마켓플레이스에 있는 경우 마이그레이션이 완료된 후에도 계속 유지됩니다. |
| **새로운 구매 및 배포** | 변경 내용이 없습니다. 고객은 중단 없이 제품을 계속 구매하고 배포할 수 있습니다. |
| **지급** | 마이그레이션 중 또는 마이그레이션 후에 발생하는 모든 구매 및 배포는 정상적으로 지급됩니다. |
|**기존 [클라우드 파트너 포털 API와의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) API 통합** | 기존 클라우드 파트너 포털 API는 마이그레이션 후에도 계속 지원되며 기존 통합은 계속 작동합니다. 자세한 내용은 [클라우드 파트너 포털 REST API가 마이그레이션 후 지원되나요?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>마이그레이션 하는 동안 클라우드 파트너 포털에 액세스 하 고 내 제안을 관리할 수 있습니까?

오퍼는 4월 13일부터 5월 한 달 동안 파트너 센터로 마이그레이션됩니다. 이 기간 동안 제공하는 마이그레이션 일정을 제외하고 평소와 같이 클라우드 파트너 포털에 액세스할 수 있습니다.
오퍼가 마이그레이션되는 동안 다음 스크린샷과 같이 "잠긴 - 파트너 센터로 이동"이라는 상태가 표시됩니다. 이 마이그레이션 기간은 24시간 미만이어야 합니다. 이 기간 동안에는 오퍼를 업데이트할 수 없습니다. 오퍼 마이그레이션을 완료하면 이메일 알림을 받게 됩니다.

:::image type="content" source="media/migration-faq/all-offers-1.png" alt-text="마이그레이션된 오퍼의 상태를 보여 줍니다.":::

오퍼를 마이그레이션한 후 클라우드 파트너 포털에서 **제한된 기간 동안** 읽기 전용 모드로 제공됩니다. 해당 상태는 '파트너 센터로 이동'을 표시하고 다음 스크린샷과 같이 파트너 센터에 오퍼에 대한 링크를 포함합니다. 이 시점부터는 모든 오퍼에 대한 업데이트를 관리하거나 파트너 센터를 통해서만 새로운 오퍼를 생성할 수 있습니다.

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="파트너 센터로 마이그레이션된 오퍼에 대해 제공된 메시지를 보여 줍니다.":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="마이그레이션된 오퍼에 대한 클라우드 파트너 포털 페이지를 보여 줍니다.":::

## <a name="how-will-i-create-new-offers"></a>새 오퍼를 만들려면 어떻게 해야 하나요?

클라우드 파트너 포털에서 파트너 센터에서 새로운 오퍼를 만들도록 안내됩니다.

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="클라우드 파트너 포털에서 새 오퍼를 만드는 메뉴를 보여 줍니다.":::

새 오퍼를 선택하면 다음과 같은 메시지가 표시됩니다.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="CPP에서 새 오퍼를 만들 때 받은 메시지를 보여 줍니다.":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>파트너 센터에서 오퍼를 관리하기 위해 새 계정을 만들어야 하나요?

아니요. 기본 계정은 보존되며 파트너 센터에서 이미 관리해야 합니다. 즉, 기존 파트너인 경우 기존 Cloud Partner Portal 계정 자격 증명을 사용하여 마이그레이션 후 파트너 센터에 로그인할 수 있습니다. 오퍼 및 관련 관리 환경만 클라우드 파트너 포털에서 파트너 센터로 이동하고 있습니다. 쿠폰이 새 계정과 연결되지 않기 때문에 새 계정을 만들지 않도록 요청합니다.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>클라우드 파트너 포털에 내 계정을 활성화하라는 메시지가 표시되는데, 이것이 무엇을 의미합니까?

계정을 파트너 센터로 올바르게 마이그레이션하고 오퍼 마이그레이션이 완료된 후 파트너 센터에서 오퍼를 관리할 수 있도록 하려면 몇 가지 세부 정보가 필요합니다. 계정 활성화에 대한 자세한 내용은 [클라우드 파트너 포털에서 파트너 센터로의 계정 마이그레이션을](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)참조하십시오.

계정 활성화를 완료하는 데 필요한 단계는 계정 역할에 따라 다릅니다.

| 계정 역할 | 활성화 단계 |
|--------------|----------------|
|소유자 | 클라우드 파트너 포털의 [게시자 프로필](https://cloudpartner.azure.com/#profile) 페이지로 이동한 다음 배너의 링크를 클릭하여 활성화합니다. 계정 활성화를 완료하려면 파트너 센터로 리디렉션됩니다. |
| 참가자 | 소유자 역할이 있는 계정의 사용자만 계정을 활성화할 수 있습니다. 계정 활성화를 완료하려면 계정 소유자에게 문의하세요. 계정 소유자가 배너 메시지에 나열되어야 합니다. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>내 계정에 로그인하여 지원 티켓을 여는 데 문제가 있습니다.

계정에 로그인할 수 없는 경우 [지원 티켓을](https://partner.microsoft.com/support/v2/?stage=1)열 수 있습니다.

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>새로운 파트너 센터 게시 경험에 대한 설명서는 어디에서 찾을 수 있습니까?

[상용 시장 설명서로](https://docs.microsoft.com/azure/marketplace/)이동하십시오. 그런 다음 파트너  >  **센터에서 상업용 마켓플레이스 포털을**확장하여 각 유형의 오퍼를 만들기 위한 도움말 항목을 보려면 새 오퍼를**만듭니다.**

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="파트너 센터의 도움말 항목 설명":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>파트너 센터와 클라우드 파트너 포털의 차이점은 무엇입니까?

클라우드 파트너 포털과 파트너 센터 간에는 다음과 같은 차이점이 있을 수 있습니다.

### <a name="modular-publishing-capabilities"></a>모듈식 게시 기능

파트너 센터는 모든 업데이트를 한 번에 게시하는 대신 게시할 변경 내용을 선택할 수 있는 모듈식 게시 옵션을 제공합니다. 예를 들어 다음 스크린샷은 게시할 변경 내용만 **속성** 및 **제안 목록의**변경 내용임을 보여 주며.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="미리 보기 및 게시 페이지를 보여 줍니다.":::

게시하지 않는 업데이트는 초안으로 저장됩니다. 오퍼 미리 보기를 계속 사용하여 쿠폰을 공개하기 전에 확인합니다.

### <a name="rich-text-format"></a>풍부한 텍스트 형식

오퍼 목록 및 계획 목록 페이지의 진부한 텍스트 편집기에서 쿠폰 및 계획 설명을 개선합니다.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="리치 텍스트 편집기 설명":::

### <a name="enhanced-preview-options"></a>향상된 미리 보기 옵션

파트너 센터에는 향상된 필터링 옵션과 [비교 기능이](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) 포함되어 있습니다. 이렇게 하면 오퍼의 미리 보기 및 라이브 버전과 비교할 수 있습니다.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="비교 기능 설명":::

### <a name="branding-and-navigation-changes"></a>브랜딩 및 내비게이션 변경

일부 브랜딩 변경 사항을 확인할 수 있습니다. 예를 들어 "SCO"는 파트너 센터에서 "계획"으로 브랜딩됩니다.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="계획 개요를 보여 줍니다.":::

또한 클라우드 파트너 포털의 **마켓플레이스** 또는 S**토런트 세부 정보(컨설팅** 서비스, Power BI 앱) 페이지에서 제공한 정보는 파트너 센터의 **오퍼 목록** 페이지에서 수집됩니다.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="오퍼 목록 페이지를 보여 줍니다.":::

이제 클라우드 파트너 포털의 단일 페이지에서 SUS에 제공한 정보는 파트너 센터의 여러 페이지에서 수집될 수 있습니다.

* 계획 설정 페이지
* 계획 목록 페이지
* 계획 가용성 페이지
* 이 스크린샷에 표시된 대로 기술 구성 페이지를 계획합니다.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="계획 기술 구성 페이지를 보여 줍니다.":::

이제 오퍼의 왼쪽 탐색 모음에 오퍼 ID가 표시됩니다.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="오퍼 ID가 있는 왼쪽 탐색 메뉴를 보여 줍니다.":::

### <a name="stop-selling-an-offer"></a>오퍼 판매 중지

파트너 센터 포털에서 직접 마켓플레이스에서 [쿠폰 판매를 중단하도록](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) 요청할 수 있습니다. 이 옵션은 **오퍼의 제안 개요** 페이지에서 확인할 수 있습니다.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="판매 중지 옵션이 있는 오퍼 개요 페이지를 보여 줍니다.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>파트너 센터의 어떤 페이지가 클라우드 파트너 포털에서 사용한 페이지에 해당하나요?

다음 표에서는 두 포털 간의 해당 링크를 보여 주십습니다.

| 호출 | 클라우드 파트너 포털 링크 | 파트너 센터 링크 |
|------|---------------------------|---------------------|
| **모든 제품 페이지** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **모든 게시자 페이지** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **게시자 프로필** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **사용자 페이지** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **히스토리 페이지** | https://cloudpartner.azure.com/#history | 파트너 센터에서는 기록 기능이 아직 지원되지 않습니다. |
| **인사이트 대시보드** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **지급 보고서** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>클라우드 파트너 포털 REST API가 마이그레이션 후 지원되나요?

클라우드 파트너 포털 API는 파트너 센터와 통합되며 오퍼가 파트너 센터로 마이그레이션된 후에도 계속 작동합니다. 통합은 작은 변화를 도입합니다. 파트너 센터로 마이그레이션한 후에도 코드가 계속 작동하는지 아래 표의 변경 내용을 검토합니다.

| **API** | **변경 내용 설명** | **영향** |
| ------- | ---------------------- | ---------- |
| 포스트 게시, GoLive, 취소 | 마이그레이션된 오퍼의 경우 응답 헤더는 다른 형식을 가지지만 작업 상태를 검색하는 상대 경로를 나타내는 동일한 방식으로 계속 작동합니다. | 오퍼에 대한 해당 POST 요청을 보낼 때 위치 헤더는 오퍼의 마이그레이션 상태에 따라 두 가지 형식 중 하나를 갖습니다.<ul><li>마이그레이션되지 않은 오퍼<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>마이그레이션된 오퍼<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| 작업 수행 | 응답에서 이전에 '알림 이메일' 필드를 지원했던 오퍼의 경우 이 필드는 더 이상 사용되지 않으며 마이그레이션된 오퍼에 대해 더 이상 반환되지 않습니다. | 마이그레이션된 오퍼의 경우 요청에 지정된 이메일 목록에 더 이상 알림이 전송되지 않습니다. 대신 API 서비스는 파트너 센터의 알림 이메일 프로세스와 일치하여 이메일을 보냅니다. 특히 파트너 센터의 계정 설정의 판매자 연락처 정보 섹션에 설정된 이메일 주소로 알림이 전송되어 운영 진행 상황을 알려줍니다.<br><br>파트너 센터의 [거래처 설정에서](https://partner.microsoft.com/dashboard/account/management) 판매자 연락처 정보 섹션에 설정된 이메일 주소를 검토하여 알림에 대한 올바른 이메일이 제공되는지 확인합니다.  |
| | | |
