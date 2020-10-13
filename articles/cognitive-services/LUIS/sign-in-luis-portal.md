---
title: LUIS 포털에 로그인
description: 새 사용자가 LUIS 포털에 로그인 하는 경우 로그인 환경은 현재 사용자 계정에 따라 약간 달라 집니다.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: a-sakand
author: skandil
ms.openlocfilehash: 22f5ab332c52a3b567b37089def6e4a494a15d29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91309475"
---
# <a name="sign-in-to-luis-portal"></a>LUIS 포털에 로그인

새 사용자가 LUIS 포털에 로그인 하는 경우 로그인 환경은 현재 사용자 계정에 따라 약간 달라 집니다.
  * Azure 구독과 연결 되어 있습니다.
  * Azure 구독과 연결 되지 않음

## <a name="determine-account-type"></a>계정 유형 확인

LUIS 포털에 처음 로그인 할 때 다음 시각적 표시기를 사용 하 여 계정 유형을 결정 합니다.

### <a name="account-without-azure-subscription"></a>Azure 구독이 없는 계정

Azure 구독과 연결 되지 않은 계정에는 오른쪽 위 탐색 모음에 Azure 아이콘이 있습니다. 연결 된 계정 유형으로 마이그레이션하면 아이콘이 더 이상 표시 되지 않습니다.

:::image type="content" source="media/sign-in/sign-in-with-account-without-azure-subscription.png" alt-text="부분 화면-Azure 아이콘이 있는 LUIS 탐색 모음의 스크린샷.":::

### <a name="account-with-azure-subscription"></a>Azure 구독을 사용 하는 계정

Azure 구독과 연결 된 계정을 사용 하 여 사용할 구독 및 리소스를 선택할 수 있습니다.

:::image type="content" source="media/sign-in/resource-selection.png" alt-text="부분 화면-Azure 아이콘이 있는 LUIS 탐색 모음의 스크린샷.":::

## <a name="sign-in-with-account-associated-with-an-azure-subscription"></a>Azure 구독과 연결 된 계정으로 로그인

1. [LUIS 포털](https://www.luis.ai) 에 로그인 하 고 사용 약관에 동의 합니다.

1. 등록 하는 두 가지 옵션이 있습니다.

    * 권장 경로인 Azure 리소스를 계속 사용 하 여 사용 가능한 유일한 경로를 제공 합니다. 이 경로를 사용 하면 구독에서 기존 리소스를 선택 하거나 새 리소스를 만들어 등록 하는 동안 Azure Authoring resource와 LUIS 계정을 연결할 수 있습니다. 이는 나중에 [마이그레이션 프로세스](luis-migration-authoring.md#what-is-migration) 를 할 필요 없이 마이그레이션에 등록 하는 것과 같습니다. 모든 사용자는 2020 년 11 월 2 일부 터 마이그레이션해야 합니다.

    * 시작 또는 평가판 키를 계속 사용 합니다. 이 경로를 사용 하면 리소스를 만들지 않고도 제공 되는 스타터 또는 평가판 리소스를 사용 하 여 LUIS에 로그인 할 수 있습니다. 이 경로를 선택 하는 경우 [계정을 마이그레이션하고](luis-migration-authoring.md#migration-steps) 응용 프로그램을 제작 리소스에 연결 해야 합니다. Azure 리소스를 계속 사용 하는 경로를 선택 하는 것이 좋습니다.

    [작성 및 시작 키에 대해 자세히 알아보세요](luis-how-to-azure-subscription.md#luis-resources). 두 리소스 모두 100만 무료 제작 트랜잭션과 1000 무료 예측 끝점 트랜잭션을 제공 합니다.

    :::image type="content" source="media/sign-in/signup-landing-page.png" alt-text="부분 화면-Azure 아이콘이 있는 LUIS 탐색 모음의 스크린샷.":::

1. 기존 제작 리소스 사용

    :::image type="content" source="media/sign-in/signup-choose-resource.png" alt-text="부분 화면-Azure 아이콘이 있는 LUIS 탐색 모음의 스크린샷.":::

    이미 구독에서 리소스를 LUIS 하 고 로그인 중에 LUIS 계정에 연결 하는 경우 **기존 제작 리소스 사용** 옵션을 선택 하 고 다음 정보를 제공 합니다.

    * **테넌트** - Azure 구독이 연결된 테넌트입니다. 기존 창에서 테 넌 트를 전환할 수 없습니다. 맨 오른쪽 아바타를 선택 하 여 테 넌 트를 전환할 수 있습니다 .이 아바타는 위쪽 막대에 이니셜을 포함 합니다.
    * **구독 이름** -리소스와 연결 되는 구독입니다. 테 넌 트에 속하는 구독이 둘 이상 있는 경우 드롭다운 목록에서 원하는 하나를 선택 합니다.
    * **리소스 이름** -계정을 연결 하려는 제작 리소스입니다.

    > [!Note]
    > 찾고 있는 제작 리소스가 드롭다운 목록에서 회색으로 표시 되는 경우 다른 지역 포털에 로그인 했음을 의미 합니다. [지역 포털의 개념을 이해](luis-reference-regions.md#luis-authoring-regions)합니다.

1. 새 제작 리소스 만들기

    :::image type="content" source="media/sign-in/signup-create-resource.png" alt-text="부분 화면-Azure 아이콘이 있는 LUIS 탐색 모음의 스크린샷." 라는 기호가 나타날 때까지 몇 초 정도 걸릴 수 있습니다. [ **계속**]을 선택 하 여 완료 합니다.

    :::image type="content" source="media/sign-in/signup-confirm-2.png" alt-text="부분 화면-Azure 아이콘이 있는 LUIS 탐색 모음의 스크린샷.":::

    > [!Note]
    > 구독 및 지역에 있는 하나 이상의 제작 리소스가 포털에서 등록 하는 것과 동일한 경우 이동할 경로를 선택할 필요 없이 자동으로 LUIS에 로그인 하 고 리소스와 연결 될 수 있습니다.


## <a name="sign-in-with-user-account-not-associated-with-an-azure-subscription"></a>Azure 구독과 연결 되지 않은 사용자 계정을 사용 하 여 로그인

1. [LUIS 포털](https://www.luis.ai) 에 로그인 하 고 사용 약관에 동의 하는지 확인 합니다.

1. [ **계속**]을 선택 하 여 완료 합니다. 평가판/시작 키를 사용 하 여 자동으로 로그인 합니다. 따라서 결국 [계정을 마이그레이션하고](luis-migration-authoring.md#migration-steps) 응용 프로그램을 제작 리소스에 연결 해야 합니다. 마이그레이션 프로세스를 진행 하려면 [Azure 무료 평가판](https://azure.microsoft.com/free/)에 로그인 해야 합니다.

    :::image type="content" source="media/sign-in/signup-no-subscription.png" alt-text="부분 화면-Azure 아이콘이 있는 LUIS 탐색 모음의 스크린샷.":::

## <a name="troubleshooting"></a>문제 해결

* 사용자가 로그인 하는 포털과 다른 지역의 Azure Portal에서 제작 리소스를 만드는 경우 제작 리소스는 회색으로 표시 됩니다.
* 새 리소스를 만들 때 리소스 이름에는 영숫자, '-'만 포함 해야 하며, '-'로 시작 하거나 끝날 수 없습니다. 그렇지 않으면 배포 취소가 실패합니다.
* [구독에 대 한 적절 한 권한이 있는지 확인 하 여 Azure 리소스를 만듭니다](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). 적절 한 권한이 없는 경우 구독 관리자에 게 문의 하 여 충분 한 권한을 부여 합니다.

## <a name="next-steps"></a>다음 단계

* [새 앱을 시작](luis-how-to-start-new-app.md) 하는 방법 알아보기
