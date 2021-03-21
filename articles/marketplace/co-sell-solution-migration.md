---
title: Microsoft AppSource에 대 한 OCP GTM에서 파트너 센터로 공동 판매 솔루션 마이그레이션
description: OCP GTM에서 Microsoft AppSource에 대 한 파트너 센터로 공동 판매 솔루션을 마이그레이션하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/04/2021
ms.openlocfilehash: 7ffb5a3dfd23a1515c6d21784b82ccdbf0674f0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593410"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>OCP GTM에서 상업적 marketplace로 공동 판매 솔루션 마이그레이션

Microsoft에서 게시 환경을 이동 하 고 있습니다. [상업적 marketplace](overview.md) 는 파트너 센터에서 제품 만들기 및 관리를 중앙 집중화 하 여 microsoft와의 관계를 이미 관리 하는 microsoft의 세 가지 채널을 통해 공동 판매를 제공 하는 간소화 된 제품 게시를 제공 합니다.

상업적 marketplace에 등록 된 Microsoft 파트너는 다음을 수행할 수 있습니다.

- 제품을 중앙에서 게시 하 고 Microsoft direct customer, 파트너 및 판매자 채널에 공동 판매 하세요.
- 제품이 제품의 기능과 일치 하는 수백만 명의 클라우드 고객에 게 도달 하기 위해 올바른 온라인 매장 ([Microsoft AppSource](https://appsource.microsoft.com) 또는 [Azure Marketplace](https://azure.microsoft.com))에 있는지 확인 하세요.
- 비즈니스 목표와 일치 하는 제품을 공동 판매 하기 위해 자체 게시 환경을 구동 합니다.
- Microsoft 관계와 공동 판매 기회를 이미 관리 하 고 있는 파트너 센터 내에서 제품 게시를 맞춥니다.
- [Marketplace 보상](partner-center-portal/marketplace-rewards.md)의 잠금을 해제 합니다.

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Microsoft와 공동 판매를 계속 하기 위한 필수 구성 요소

활성 Microsoft 파트너 네트워크 멤버 자격이 있고 파트너 센터의 상업적 marketplace에 등록 되어 있는지 확인 하세요.

- Microsoft 파트너 네트워크를 [무료로](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)조인 하세요. 파트너는 독점적 리소스, 프로그램, 도구 및 연결에 액세스 하 여 비즈니스를 확장할 수 있습니다.
- 상업적 marketplace에 계정이 없는 경우 [지금 등록](partner-center-portal/create-account.md) 하 여 Microsoft와 공동 판매를 계속 하 고 전체 게시 환경에 액세스 합니다.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>공동 판매 준비 상태를 실현 하기 위해 업데이트를 게시 합니다.

솔루션이 Microsoft 판매자 및 파트너에 게 검색 가능 하려면 [공동 판매 준비 요구 사항을](marketplace-co-sell.md)충족 해야 합니다. Microsoft 판매자가 incentivized 하려면 솔루션이 적절 한 [요구 사항을](marketplace-co-sell.md)충족 해야 합니다. 파트너 센터의 공동 판매 탭에서 이러한 요구 사항을 완료 합니다 (이 문서 뒷부분의 [이 이미지](#cosell-tab) 참조).

> [!NOTE]
> 상업적 marketplace에서 솔루션은 게시 환경 전체에서 "제품" 이라고 합니다.

상용 marketplace에 등록 한 후에는 OCP GTM에서 솔루션 마이그레이션을 준비 합니다.

OCP GTM에서 솔루션을 가져오기 전에 다음 단계를 수행 합니다.

1. 회사의 [게시자 목록을](https://partner.microsoft.com/dashboard/account/v3/publishers/list)방문 합니다. 게시 액세스 권한이 있는 계정 소유자, 관리자 및 개발자를 포함 합니다. [파트너 센터 사용자 역할](./partner-center-portal/manage-account.md#define-user-roles-and-permissions)에 대해 자세히 알아보세요.
2. 이러한 역할만 솔루션을 편집 및 게시할 수 있으므로 나열 된 연락처 중 하나를 통해 *관리자* 또는 *개발자* 로 서 상업적 marketplace에 [사용자를 추가](https://partner.microsoft.com/dashboard/account/usermanagement) 하도록 요청 합니다.
3. 개발자와 협력 하 여 OCP GTM 계정에서 상업적 marketplace로 솔루션을 이동 합니다.
4. 수행할 작업을 결정 합니다.
    1. 이 솔루션을 상업적 marketplace의 유사한 제품으로 병합 합니다.
    1. 이 솔루션을 OCP GTM에서 상업적 marketplace로 마이그레이션합니다.
    1. 이 솔루션을 삭제 합니다.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>OCP GTM에서 솔루션 마이그레이션

1. [여기](https://partner.microsoft.com/solutions/migration#)에서 마이그레이션을 시작 합니다.
2. **개요** 페이지를 선택 하 고 **여기를 클릭 하 여 시작** 하세요.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="파트너 센터 개요 페이지, 개요 탭.":::

3. 마이그레이션을 시작 하려면 MPN Id와 연결 된 모든 솔루션을 표시 하는 **솔루션** 탭을 선택 합니다.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="파트너 센터 개요 페이지, 솔루션 탭":::

    > [!NOTE]
    > 이 탭은 상업적 marketplace로 마이그레이션하도록 보류 중인 솔루션이 없는 경우에 유의 합니다. Microsoft와 공동 판매를 계속 하려면 마이그레이션된 솔루션이 상용 marketplace에 게시 되었는지 확인 하세요.

    도구 설명을 검토 하 여 솔루션 상태에 대해 자세히 알아보세요. 모든 솔루션 보류 중인 작업은 **작업** 아래에 나열 됩니다.<a name="beginmigration"></a>

4. 마이그레이션할 솔루션의 **마이그레이션 시작** (위 이미지 참조)을 선택 하 고 다음 옵션 중 하나를 선택 합니다.

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="마이그레이션에 대 한 세 가지 옵션입니다.":::

### <a name="merge-solution-with-a-similar-offer"></a>유사한 제품을 사용 하 여 솔루션 병합

솔루션이 이미 상업적 marketplace에 게시 되어 있고 둘을 단일 제품으로 병합 해야 하는 경우이 옵션을 선택 합니다. 이렇게 하면 중복 된 제안이 생성 되지 않습니다.

1. 기존 제품을 식별 합니다.
    1. **이 솔루션을 상업적 marketplace의 유사한 제품으로 병합** 합니다 ( [위의](#beginmigration) **작업 필요** 이미지 참조).
    1. **작업 1** 탭은 OCP GTM 솔루션을 연결할 수 있는 live 상용 marketplace 제품을 보여 줍니다. 목록에서 live 제품을 선택 합니다 (있는 경우). 선택할 제품 목록이 없는 경우 Microsoft AppSource 또는 Azure Marketplace에서 고객 연결 주소 (URL)를 입력 합니다.
        [![병합 프로세스의 동작 1 탭입니다.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. **계속** 을 선택합니다.
1. 병합을 요청 합니다.
    1. **작업 2** 탭에서는 식별 한 것과 함께 OCP GTM 솔루션을 병합 하도록 요청 하는 지침을 보여 줍니다. 병합을 요청 하려면 저장을 선택 하 **& 지원 담당자에 게 문의 하세요**. 그러면 브라우저에서 파트너 지원 페이지가 열립니다.
    1. **문제 세부 정보 제공** 을 선택 하 고 [ ![ 병합 프로세스의 동작 2 탭](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox) 을 입력 합니다.
    1. **제출** 을 선택합니다. 파트너 지원 팀은 영업일 2 일 이내에 사용자에 게 연락 합니다.
    1. 파트너 지원은 live 제품으로 게시 되도록이 제품의 성공적인 병합을 보장 하기 위해 사용자와 함께 작동 합니다.

### <a name="migrate-this-solution-from-ocp-gtm"></a>OCP GTM에서이 솔루션 마이그레이션

아직 상용 marketplace에 게시 된 해당 제품이 없는 OCP GTM 솔루션을 사용할 수 있는 경우이 옵션을 선택 합니다. Microsoft와 공동 판매를 계속 하기 위해이 솔루션을 상용 marketplace에 게시 하 고,이 솔루션을 마이그레이션하면 OCP GTM의 정보 및 자료를 보존 하 여 시간을 절약할 수 있습니다. 이 옵션을 사용 하려면 제안 유형을 선택 해야 합니다.

1. **이 솔루션을 OCP GTM에서 상용 marketplace로 마이그레이션하려는** 경우 ( [위의](#beginmigration) **작업 필수** 이미지 참조) **계속** 을 선택 합니다.
1. **작업 1** 탭에서 [제안 유형을](publisher-guide-by-offer-type.md)선택 하 고 **계속** 합니다.

    [![마이그레이션 프로세스의 작업 1 탭입니다.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. **작업 2** 탭의 제공 된 목록에서 [게시자 프로필](partner-center-portal/create-account.md) 을 선택 합니다. 게시자 계정이 없으면 [파트너 센터](https://partner.microsoft.com/solutions/migration)에서 게시자 계정을 만든 다음 여기에서 선택 합니다.

    [![마이그레이션 프로세스의 작업 2 탭입니다.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. 초안 **제품 만들기** 를 선택 하 여 솔루션을 상용 marketplace로 마이그레이션합니다. 아직 라이브 되지 않습니다.
1. 파트너 센터에서 게시 프로세스를 계속 합니다. 파트너 센터의 게시에 대 한 도움이 필요한 경우 아래 [상업적 marketplace에서 제품을 라이브 상태로 만들기](#make-your-offer-live-in-the-marketplace) 를 참조 하세요.

### <a name="discard-this-solution"></a>이 솔루션을 삭제 합니다.

OCP GTM 솔루션의 솔루션이 더 이상 관련이 없는 경우이 옵션을 선택 합니다. 취소를 확인 하 라는 메시지가 표시 되며 나중에 다시 실행 취소할 수도 있습니다.

1. **이 솔루션을 삭제 하려면** ( [위의](#beginmigration) **작업 필수** 이미지 참조) **계속** 을 선택 합니다.
2. **취소** 를 선택 합니다.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="취소를 확인 합니다.":::

3. 취소를 취소 **하려면 취소 취소를 선택 합니다**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="취소 취소 링크입니다.":::

4. 추가 도움이 필요 하면 **도움말 가져오기** 탭을 선택 하 여 파트너 지원 팀에 문의 하세요.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="도움말 가져오기 탭의 지원 링크":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Marketplace에서 제품을 라이브 상태로 만들기

제품을 상업적 marketplace로 마이그레이션하도록 선택 하는 경우 초안으로 도착할 것입니다. 상업적 marketplace에서 live를 만들려면 제품을 게시 해야 합니다. 그러면 공동 판매 상태, 성과급 및 조회 파이프라인이 보존 됩니다.

제품을 게시 하기 전에 제공 해야 하는 정보에 대 한 자세한 지침은 적절 한 [게시 가이드](publisher-guide-by-offer-type.md)를 참조 하세요. 요약 정보는 아래를 참조 하세요.

1. 파트너 센터의 **개요** 페이지에서 초안 제안 이름을 선택 합니다.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="파트너 센터의 제품 개요 페이지.":::

<a name="cosell-tab"></a>

2. 각 탭에서 필요한 정보를 입력 합니다. 필요에 따라 CSP (클라우드 솔루션 공급자) 프로그램을 통해 재판매로 **재판매에서 csp** (왼쪽 탐색 메뉴) 페이지를 완료 합니다. **자세한 내용** 링크 및 도구 설명에서는 요구 사항 및 세부 정보를 안내 합니다.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="제안 개요는 파트너 센터의 설정 탭입니다.":::

3. Microsoft 판매자 관련 세부 정보 중 일부는 OCP GTM 솔루션에서 복사 되었습니다. **Microsoft와 공동** 판매 탭에서 나머지 필수 정보를 완료 하 여 제품을 공동 판매 하도록 준비 합니다. 완료 되 면 **검토 및 게시** 를 선택 합니다. 자세한 내용은 [상용 marketplace 제품에 대 한 공동 판매 구성](commercial-marketplace-co-sell.md)을 참조 하세요.

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="파트너 센터의 제품 공동 판매 탭":::

4. 제출 된 모든 정보를 검토 한 후 **게시** 를 선택 하 여 인증 검토를 위한 초안 제안을 제출 합니다.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="파트너 센터의 제품 검토 탭 및 게시 단추":::

5. **개요** 탭에서 제출 상태를 추적 합니다.

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="파트너 센터의 개요 탭에 있는 제품 게시 상태 표시줄입니다.":::

6. 귀하의 인증 검토가 완료 되 면 사용자에 게 알려줍니다. 실행 가능한 피드백을 제공 하는 경우이를 해결 하 고 **게시** 를 선택 하 여 재인증를 시작 합니다.
7. 제품이 인증을 통과 하면 제공 된 링크를 사용 하 여 제품을 미리 보고 원하는 최종 조정을 수행할 수 있습니다. 준비가 되 면 **라이브 전환** (위 단추 참조)을 선택 하 여 제품을 관련 상용 marketplace storefront에 게시 합니다. 살고 있으면 제품은 원래 OCP GTM 솔루션의 공동 판매 상태를 유지 합니다.

## <a name="next-steps"></a>다음 단계

- [CSP 파트너를 통한 재판매](cloud-solution-providers.md)
- [상업적 marketplace 제품에 대 한 공동 판매 구성](commercial-marketplace-co-sell.md)
- 이러한 [faq](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) 보기 (PDF)
