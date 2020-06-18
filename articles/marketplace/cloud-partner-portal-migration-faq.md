---
title: 파트너 센터로의 마이그레이션에 대한 일반적인 질문 - Microsoft 상업용 마켓플레이스
description: Cloud 파트너 포털에서 파트너 센터로의 제품 마이그레이션에 대해 일반적으로 자주 묻는 질문의 답변입니다.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727481"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Cloud 파트너 포털에서 파트너 센터로의 마이그레이션에 대한 일반적인 질문

이 문서에서는 Cloud 파트너 포털에서 파트너 센터로의 제품 마이그레이션에 대한 일반적인 질문을 다룹니다.

## <a name="what-does-offer-migration-mean"></a>제품 마이그레이션이란 무엇인가요?

제품 게시 및 관리 환경을 변경하여 Cloud 파트너 포털에서 파트너 센터로 제품 데이터를 이동하고 있습니다.

| 영역  | 변경  |
|-------|----------|
| **게시 및 제품 관리 환경** | 파트너 센터의 직관적인 인터페이스를 사용하여 향상된 사용자 환경을 제공합니다. 자세한 내용은 [파트너 센터와 Cloud 파트너 포털의 차이점은 무엇인가요?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal)를 참조하세요. |
| **Marketplace에서 제품의 가용성** | 변경 내용이 없습니다. 제품이 Marketplace에 있으면 마이그레이션 도중 및 마이그레이션 후에 계속해서 이용할 수 있습니다. |
| **새로운 구매 및 배포** | 변경 내용이 없습니다. 고객은 중단 없이 제품을 계속 구매하고 배포할 수 있습니다. |
| **지급** | 마이그레이션 도중 또는 마이그레이션 후에 발생하는 모든 구매 및 배포는 평상시와 같이 계속 청구됩니다. |
|**기존 [Cloud 파트너 포털 API와 API 통합](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)** | 기존 Cloud 파트너 포털 API는 마이그레이션 후에도 계속 지원되며 기존 통합은 계속 작동합니다. 자세한 내용은 [Cloud 파트너 포털 REST API가 마이그레이션 후에도 지원되나요?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration)를 참조하세요. |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>마이그레이션하는 동안 계속 Cloud 파트너 포털에 액세스하고 내 제품을 관리할 수 있나요?

모든 제품이 파트너 센터로 마이그레이션되었습니다. 각 제품의 마이그레이션 기간은 24시간 미만이어야 합니다. 제품이 마이그레이션된 후에는 이메일 알림을 받게 됩니다.

제품이 마이그레이션된 후에 Cloud 파트너 포털에서 **제한된 기간 동안** 읽기 전용 모드가 됩니다. 상태에는 다음 스크린샷에 표시된 것처럼 “파트너 센터로 이동됨”이 표시되고 파트너 센터의 사용자 제품에 대한 링크가 포함됩니다. 이 시점부터 파트너 센터를 통해 모든 제품에 대한 업데이트를 관리하거나 새 제품을 독점적으로 만듭니다.

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="파트너 센터에 마이그레이션된 제품에 제공된 메시지를 보여 줍니다.":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="마이그레이션된 제품의 Cloud 파트너 포털 페이지를 보여 줍니다.":::

## <a name="how-will-i-create-new-offers"></a>새 제품은 어떻게 만드나요?

Cloud 파트너 포털에서 파트너 센터에 새 제품을 만들도록 안내합니다.

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Cloud 파트너 포털에서 새 제품을 만드는 메뉴를 보여 줍니다.":::

새 제품을 선택하면 다음과 같은 메시지가 표시됩니다.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="CPP에서 새 제품을 만들 때 수신되는 메시지를 보여 줍니다.":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>파트너 센터에서 제품을 관리할 새 계정을 만들어야 하나요?

아니요. 기본 계정이 유지되며 파트너 센터에서 이미 관리하고 있어야 합니다. 즉, 기존 파트너의 경우 기존 Cloud 파트너 포털 계정 자격 증명을 사용하여 파트너 센터 마이그레이션 후에 로그인할 수 있습니다. 제품 및 연결된 관리 환경만 Cloud 파트너 포털에서 파트너 센터로 이동됩니다. 제품이 새 계정에 연결되지 않으므로 새 계정을 만들지 않도록 요청합니다.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>내 계정을 활성화하라는 Cloud 파트너 포털 메시지가 표시됩니다. 무슨 뜻인가요?

계정을 파트너 센터로 올바르게 마이그레이션하고 마이그레이션이 완료된 후 파트너 센터에서 제품을 관리할 수 있도록 하기 위해서는 더 자세한 사용자 정보가 필요합니다. 계정 활성화에 대한 자세한 내용은 [Cloud 파트너 포털에서 파트너 센터로 계정 마이그레이션](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc)을 참조하세요.

계정 활성화를 완료하는 데 필요한 단계는 계정 역할에 따라 다릅니다.

| 계정 역할 | 활성화 단계 |
|--------------|----------------|
|소유자 | Cloud 파트너 포털에서 [게시자 프로필](https://cloudpartner.azure.com/#profile) 페이지로 이동한 다음, 배너의 링크를 클릭하여 활성화합니다. 계정 활성화를 완료하기 위해 파트너 센터로 리디렉션됩니다. |
| 참가자 | 소유자 역할의 계정에 있는 사용자만 계정을 활성화할 수 있습니다. 계정 활성화를 완료하려면 계정 소유자에게 문의하세요. 계정 소유자는 배너 메시지에 나열되어야 합니다. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>내 계정에 로그인하여 지원 티켓을 여는 데 문제가 있습니다.

계정에 로그인할 수 없는 경우 [지원 티켓](https://partner.microsoft.com/support/v2/?stage=1)을 열 수 있습니다.

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>새로운 파트너 센터 게시 경험에 대한 설명서는 어디서 찾을 수 있나요?

[상업용 마켓플레이스 설명서](https://docs.microsoft.com/azure/marketplace/)로 이동합니다. 그런 다음, **파트너 센터의 상업용 마켓플레이스 포털**  > **새 제품 만들기**를 확장하여 각 제품 유형을 만들기 위한 도움말 항목을 표시합니다.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="파트너 센터에 대한 도움말 항목을 보여 줍니다.":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>파트너 센터와 Cloud 파트너 포털의 차이점은 무엇인가요?

Cloud 파트너 포털과 파트너 센터의 차이점은 다음과 같습니다.

### <a name="modular-publishing-capabilities"></a>모듈식 게시 기능

파트너 센터는 항상 모든 업데이트를 한 번에 게시하는 대신 게시하려는 변경 내용을 선택할 수 있는 모듈식 게시 옵션을 제공합니다. 예를 들어 다음 스크린샷은 게시하도록 선택한 변경 내용이 **속성** 및 **제품 목록**에 대한 변경 내용임을 보여 줍니다.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="미리 보기 및 게시 페이지를 보여 줍니다.":::

게시하지 않는 업데이트는 초안으로 저장됩니다. 계속해서 제품 미리 보기를 사용하여 공개로 설정하기 전에 제품을 확인합니다.

### <a name="rich-text-format"></a>서식 있는 텍스트 형식

제품 목록 및 플랜 목록 페이지에서 서식 있는 텍스트 편집기를 사용하여 제품 및 플랜 설명을 향상시킵니다.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="서식 있는 텍스트 편집기를 보여 줍니다.":::

### <a name="enhanced-preview-options"></a>향상된 미리 보기 옵션

파트너 센터에는 향상된 필터링 옵션을 사용하는 [비교 기능](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers)이 포함되어 있습니다. 이를 통해 제품의 미리 보기 및 라이브 버전과 비교할 수 있습니다.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="비교 기능을 보여 줍니다.":::

### <a name="branding-and-navigation-changes"></a>브랜딩 및 탐색 변경 내용

일부 브랜딩 변경 내용이 있습니다. 예를 들어 “SKU”는 파트너 센터에서 “플랜”으로 브랜딩됩니다.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="플랜 개요를 보여 줍니다.":::

또한 Cloud 파트너 포털에서 **상점 세부 정보**(컨설팅 서비스, Power BI 앱) 페이지 또는 **Marketplace**에서 제공하는 데 사용한 정보는 파트너 센터의 **제품 목록** 페이지에서 수집됩니다.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="제품 목록 페이지를 보여 줍니다.":::

Cloud 파트너 포털의 단일 페이지에서 SKU를 제공하는 데 사용한 정보는 이제 파트너 센터의 여러 페이지에서 수집될 수 있습니다.

* 플랜 설정 페이지
* 플랜 목록 페이지
* 플랜 가용성 페이지
* 이 스크린샷에 표시된 대로 기술 구성 페이지를 계획합니다.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="기술 구성 페이지 계획을 보여 줍니다.":::

제품 ID는 이제 제품의 왼쪽 탐색 모음에 표시됩니다.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="제품 ID가 포함된 왼쪽 탐색 메뉴를 보여 줍니다.":::

### <a name="stop-selling-an-offer"></a>제품 판매 중지

파트너 센터 포털에서 직접 Marketplace의 [제품 판매 중지](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)를 요청할 수 있습니다. 이 옵션은 제품의 **제품 개요** 페이지에서 사용할 수 있습니다.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="판매 중지 옵션이 포함된 제품 개요 페이지를 보여 줍니다.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Cloud 파트너 포털에서 사용한 페이지에 해당하는 파트너 센터의 페이지는 무엇인가요?

다음 표에는 두 포털 간 해당하는 링크를 보여 줍니다.

| 호출 | Cloud 파트너 포털 링크 | 파트너 센터 링크 |
|------|---------------------------|---------------------|
| **모든 제품 페이지** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **모든 게시자 페이지** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **게시자 프로필** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **사용자 페이지** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **기록 페이지** | https://cloudpartner.azure.com/#history | 기록 기능은 파트너 센터에서 아직 지원되지 않습니다. |
| **인사이트 대시보드** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **지급 보고서** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>Cloud 파트너 포털 REST API가 마이그레이션 후에도 지원되나요?

Cloud 파트너 포털 API는 파트너 센터와 통합되며 제품을 파트너 센터로 마이그레이션한 후에도 계속 작동합니다. 통합에는 작은 변경 사항이 도입되었습니다. 파트너 센터로 마이그레이션한 후에도 코드가 계속 작동하는지 확인하려면 아래 표의 내용을 검토합니다.

| **API** | **변경 내용 설명** | **영향** |
| ------- | ---------------------- | ---------- |
| POST 게시, GoLive, 취소 | 마이그레이션된 제품의 경우 응답 헤더의 형식은 서로 다르지만 작업 상태를 검색하기 위한 상대 경로를 나타내는 것과 동일한 방식으로 계속 작동합니다. | 제품에 대한 해당 POST 요청을 전송하는 경우 위치 헤더는 제품의 마이그레이션 상태에 따라 다음 두 가지 형식 중 하나가 됩니다.<ul><li>마이그레이션되지 않은 제품<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>마이그레이션된 제품<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET 작업 | 응답의 ‘알림-이메일’ 필드가 이전에 지원되었던 제품의 경우 이 필드는 더 이상 사용되지 않으며 마이그레이션된 제품에 대해 더 이상 반환되지 않습니다. | 마이그레이션된 제품의 경우 요청에 지정된 이메일 목록에 더 이상 알림을 보내지 않습니다. 대신, API 서비스는 이메일을 보내기 위해 파트너 센터의 알림 이메일 프로세스와 정렬됩니다. 특히 알림은 파트너 센터에서 계정 설정의 판매자 연락처 정보 섹션에 설정된 이메일 주소에 전송되어 작업 진행률을 알립니다.<br><br>파트너 센터의 [계정 설정](https://partner.microsoft.com/dashboard/account/management)에서 판매자 연락처 정보 섹션에 있는 이메일 주소 세트를 검토하여 알림에 올바른 이메일이 제공되는지 확인합니다.  |
| | | |
