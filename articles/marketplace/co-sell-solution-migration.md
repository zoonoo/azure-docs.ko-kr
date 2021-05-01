---
title: OCP GTM에서 Microsoft AppSource용 파트너 센터로 공동 판매 솔루션 마이그레이션
description: OCP GTM에서 Microsoft AppSource용 파트너 센터로 공동 판매 솔루션을 마이그레이션하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/04/2021
ms.openlocfilehash: 7ffb5a3dfd23a1515c6d21784b82ccdbf0674f0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593410"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>공동 판매 솔루션을 OCP GTM에서 상업용 Marketplace로 마이그레이션

Microsoft는 게시 환경을 바꾸고 있습니다. [상업용 Marketplace](overview.md)는 이미 Microsoft와 관계를 관리하고 있는 파트너 센터에서 제품의 제작과 관리를 중앙화하여 Microsoft의 3개의 채널을 통해서 공동 판매를 위한 간편한 제품 게시 서비스를 제공합니다.

상업용 Marketplace에 등록된 Microsoft 파트너는 다음 활동이 가능합니다.

- 제품을 중앙에 게시하고 Microsoft 직접 고객, 파트너 및 판매자 채널에 공동 판매하세요.
- 제품이 제품의 기능에 부합하는 수백만 명의 클라우드 고객에게 도달하기 위해 올바른 온라인 매장([Microsoft AppSource](https://appsource.microsoft.com) 또는 [Azure Marketplace](https://azure.microsoft.com))에 있는지 확인하세요.
- 비즈니스 목표와 일치하는 제품을 공동 판매하기 위한 자체 게시 환경을 운용하세요.
- 이미 Microsoft와 계약을 맺어 공동 판매 기회를 관리하고 있는 파트너 센터에서 제품 게시를 맞춰보세요.
- [Marketplace Rewards](partner-center-portal/marketplace-rewards.md)의 잠금을 해제합니다.

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Microsoft와 공동 판매를 지속하기 위한 전제 조건

활성화된 Microsoft 파트너 네트워크 멤버 자격이 있고 파트너 센터의 상업용 Marketplace에 등록되어 있는지 확인하세요.

- Microsoft 파트너 네트워크에 [무료로](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) 조인하세요. 파트너는 독점적 리소스, 프로그램, 도구 및 연결에 액세스하여 비즈니스를 성장시킬 수 있습니다.
- 상업용 Marketplace 계정이 없는 경우 [지금 등록](partner-center-portal/create-account.md)해서 Microsoft와 지속적으로 공동 판매를 하고 전체 게시 환경에 액세스하세요.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>공동 판매 준비 상태를 위한 업데이트 게시

솔루션이 Microsoft 판매자와 파트너에 노출되려면 [공동 판매 준비 요구 사항](marketplace-co-sell.md)을 충족해야 합니다. Microsoft 판매자가 인센티브를 받으려면 [인센티브 자격 요구 사항](marketplace-co-sell.md)을 충족해야 합니다. 파트너 센터의 공동 판매 탭에서 요구 사항을 충족하세요(이 문서 뒷부분의 [이 이미지](#cosell-tab) 참조).

> [!NOTE]
> 상업용 Marketplace에서 솔루션은 게시 환경 전체에서 “제품”으로 간주됩니다.

상업용 Marketplace에 등록한 후에는 OCP GTM에서 솔루션 마이그레이션을 준비합니다.

OCP GTM에서 솔루션을 가져오기 전에 다음 단계를 수행합니다.

1. 회사의 [게시자 목록](https://partner.microsoft.com/dashboard/account/v3/publishers/list)에 접속합니다. 게시자에는 게시 액세스 권한이 있는 계정 소유자, 관리자 및 개발자가 포함됩니다. 자세한 정보는 [파트너 센터 사용자 역할](./partner-center-portal/manage-account.md#define-user-roles-and-permissions)에서 알아보세요.
2. 해당 역할들로만 솔루션을 편집 및 게시할 수 있으므로 나열된 연락처 중 하나를 통해 *관리자* 또는 *개발자* 로서 상업용 Marketplace에 [사용자를 추가](https://partner.microsoft.com/dashboard/account/usermanagement)하도록 요청합니다.
3. 개발자와 협력하여 OCP GTM 계정에서 상업용 Marketplace로 솔루션을 이동합니다.
4. 수행할 작업을 결정합니다.
    1. 이 솔루션을 상업용 Marketplace에 있는 유사한 제품과 병합합니다.
    1. 이 솔루션을 OCP GTM에서 상업용 Marketplace로 마이그레이션합니다.
    1. 이 솔루션을 삭제합니다.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>OCP GTM에서 공동 판매 솔루션 마이그레이션

1. [여기](https://partner.microsoft.com/solutions/migration#)에서 마이그레이션을 시작합니다.
2. **개요** 페이지를 선택하고 **여기를 클릭하여 시작하세요**.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="파트너 센터 개요 페이지, 개요 탭.":::

3. 마이그레이션을 시작하려면 MPN ID와 연결된 모든 솔루션을 표시하는 **솔루션** 탭을 선택합니다.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="파트너 센터 개요 페이지, 솔루션 탭.":::

    > [!NOTE]
    > 이 탭은 상업용 Marketplace로의 마이그레이션이 보류 중인 솔루션이 없는 경우에 표시됩니다. Microsoft와 공동 판매를 계속하려면 마이그레이션된 솔루션이 상업용 Marketplace에 게시되었는지 확인하세요.

    도구 설명을 검토해서 솔루션 상태에 대해 자세히 알아보세요. 모든 솔루션의 대기 중인 작업은 **작업** 아래에 나열됩니다.<a name="beginmigration"></a>

4. 마이그레이션할 솔루션의 **마이그레이션 시작**(위 이미지 참조)을 선택하고 다음 옵션 중 하나를 선택합니다.

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="마이그레이션에 대한 세 가지 옵션.":::

### <a name="merge-solution-with-a-similar-offer"></a>유사한 제품과 솔루션 병합

솔루션이 이미 상업용 Marketplace에 게시되어 있고 둘을 단일 제품으로 병합해야 하는 경우 이 옵션을 선택합니다. 이렇게 하면 중복 제품이 생성되지 않습니다.

1. 기존 제품을 식별합니다.
    1. **이 솔루션을 상업용 Marketplace의 유사한 제품으로 병합합니다** 를 선택합니다([위의](#beginmigration) **작업 필요** 이미지 참조).
    1. **작업 1** 탭은 OCP GTM 솔루션을 연결할 수 있는 라이브 상업용 Marketplace 제품을 보여 줍니다. 제품이 있다면 목록에서 라이브인 제품을 선택합니다. 선택할 제품 목록이 없는 경우 Microsoft AppSource 또는 Azure Marketplace에서 고객 연결 주소(URL)를 입력합니다.
        [![병합 프로세스의 작업 1 탭입니다.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. **계속** 을 선택합니다.
1. 병합을 요청합니다.
    1. **작업 2** 탭에서는 식별한 것과 함께 OCP GTM 솔루션을 병합하도록 요청하는 지침을 보여 줍니다. 병합을 요청하려면 **저장 및 기술 지원 서비스에 문의** 를 선택하세요. 브라우저에서 파트너 지원 페이지가 열립니다.
    1. **문제 세부 정보 제공** 을 선택하고       [![ 병합 프로세스의 작업 2 탭](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)을 입력합니다.
    1. **제출** 을 선택합니다. 파트너 지원 팀은 영업일 2일 이내에 사용자에게 답변을 드립니다.
    1. 파트너 지원은 성공적인 병합을 보장하고 라이브 제품으로 게시되도록 사용자와 협력합니다.

### <a name="migrate-this-solution-from-ocp-gtm"></a>OCP GTM에서 이 솔루션을 마이그레이션

아직 상업용 Marketplace에 해당 제품이 게시되지 않았을 때 OCP GTM 솔루션을 사용할 경우 이 옵션을 선택합니다. Microsoft와 공동 판매를 지속하기 위해 이 솔루션을 상업용 Marketplace에 게시하고, 이 솔루션을 마이그레이션하면 OCP GTM의 정보와 제품 구성 정보를 보존해서 시간을 절약할 수 있습니다. 이 옵션을 사용하려면 제품 유형을 선택해야 합니다.

1. **이 솔루션을 OCP GTM에서 상업용 Marketplace로 마이그레이션합니다**( [위의](#beginmigration) **작업 필수** 이미지 참조)를 선택하고 **계속** 합니다.
1. **작업 1** 탭에서 [제품 유형](publisher-guide-by-offer-type.md)을 선택하고 **계속** 합니다.

    [![마이그레이션 프로세스의 작업 1 탭.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. **작업 2** 탭의 목록에서 [게시자 프로필](partner-center-portal/create-account.md)을 선택합니다. 게시자 계정이 없으면 [파트너 센터](https://partner.microsoft.com/solutions/migration)에서 게시자 계정을 만든 다음 여기에서 선택합니다.

    [![마이그레이션 프로세스의 작업 2 탭.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. **제품 초안 만들기** 를 선택하여 솔루션을 초안 상태로 상업용 Marketplace에 마이그레이션합니다. 아직 라이브가 되지 않습니다.
1. 파트너 센터의 게시 프로세스를 계속 진행합니다. 파트너 센터 게시에 대한 도움이 필요한 경우 아래 [상업용 Marketplace에서 제품을 라이브 상태로 만들기](#make-your-offer-live-in-the-marketplace)를 참조하세요.

### <a name="discard-this-solution"></a>이 솔루션을 삭제합니다

OCP GTM 솔루션의 솔루션이 더 이상 상관이 없는 경우 이 옵션을 선택합니다. 삭제를 확인하라는 메시지가 표시되며 나중에 삭제를 취소할 수도 있습니다.

1. **이 솔루션을 삭제합니다**([위의](#beginmigration) **작업 필수** 이미지 참조)을 선택하고 **계속** 합니다.
2. **삭제** 를 선택합니다.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="삭제를 확인합니다.":::

3. 삭제를 취소하려면 **삭제 취소** 를 선택합니다.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="삭제 취소 링크입니다.":::

4. 추가적인 도움이 필요하다면 **도움말 보기** 탭을 선택하여 파트너 지원 팀에 문의하세요.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="도움말 보기 탭의 지원 링크.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>마켓플레이스에서 제품을 라이브 상태로 만들기

제품을 상업용 Marketplace로 마이그레이션하도록 선택했다면 초안 상태로 도착합니다. 상업용 Marketplace에서 라이브 상태로 만들려면 제품을 게시해야 합니다. 게시하면 공동 판매 상태, 인센티브 및 참조 파이프라인이 보존됩니다.

제품을 게시하기 전에 입력해야 하는 정보에 대한 자세한 지침은 적절한 [게시 가이드](publisher-guide-by-offer-type.md)를 참조하세요. 아래는 요약 정보입니다.

1. 파트너 센터의 **개요** 페이지에서 제품 초안 이름을 선택합니다.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="파트너 센터의 제품 개요 페이지.":::

<a name="cosell-tab"></a>

2. 각 탭에서 필요한 정보를 입력합니다. 필요하다면 **CSP를 통해 재판매** 페이지(왼쪽 탐색 메뉴)를 완료해서 CSP(클라우드 솔루션 공급자) 프로그램을 통해 재판매합니다. **자세한 내용** 링크 및 도구 설명에서는 요구 사항 및 세부 정보를 안내합니다.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="파트너 센터의 제품 개요 설정 탭.":::

3. Microsoft 판매자 대상 세부 정보 중 일부는 OCP GTM 솔루션에서 복사됩니다. **Microsoft와 공동 판매** 탭에서 나머지 필수 정보를 작성하여 제품을 공동 판매하도록 준비합니다. 완료되면 **검토 및 게시** 를 선택합니다. 자세한 내용은 [상업용 Marketplace 제품에 대한 공동 판매 구성](commercial-marketplace-co-sell.md)을 참조하세요.

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="파트너 센터의 제품 공동 판매 탭입니다.":::

4. 제출된 모든 정보를 검토한 후 **게시** 를 선택하여 인증 검토를 위한 제품 초안을 제출합니다.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="파트너 센터의 제품 검토 탭 및 게시 단추.":::

5. **개요** 탭에서 제출 상태를 추적합니다.

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="파트너 센터의 개요 탭에 있는 제품 게시 상태 표시줄.":::

6. 인증 검토가 완료되면 사용자에게 알려드립니다. 실행 가능한 피드백을 제공받았다면 이를 해결하고 **게시** 를 선택해 재인증을 시작합니다.
7. 제품이 인증을 통과하면 제공된 링크를 사용하여 제품을 미리 보고 원하는 대로 최종 조정을 할 수 있습니다. 준비가 되면 **실시간 전송**(위 단추 참조)을 선택하여 제품을 관련 상업용 Marketplace 상점에 게시합니다. 일단 라이브 상태가 되면 제품은 원래 OCP GTM 솔루션에서의 공동 판매 상태를 유지합니다.

## <a name="next-steps"></a>다음 단계

- [CSP 파트너를 통한 재판매](cloud-solution-providers.md)
- [상업용 Marketplace 제품에 대한 공동 판매 구성](commercial-marketplace-co-sell.md)
- 해당 [FAQ](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) 보기(PDF)
