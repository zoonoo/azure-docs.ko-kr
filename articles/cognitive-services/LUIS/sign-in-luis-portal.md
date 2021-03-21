---
title: LUIS 포털에 로그인
description: 새 사용자가 LUIS 포털에 로그인 하는 경우 로그인 환경은 현재 사용자 계정에 따라 약간 달라 집니다.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706778"
---
# <a name="sign-in-to-luis-portal"></a>LUIS 포털에 로그인

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

이 문서를 사용 하 여 LUIS 포털을 시작 하 고 제작 리소스를 만들 수 있습니다. 이 문서의 단계를 완료 한 후에는 LUIS apps를 만들고 게시할 수 있습니다.

## <a name="access-the-portal"></a>포털 액세스


1. LUIS을 시작 하려면 [LUIS 포털로](https://www.luis.ai)이동 합니다. 아직 구독이 없는 경우 [무료 계정](https://azure.microsoft.com//free/cognitive-services/) 만들기를 요청 하 고 포털로 다시 돌아갈 수 있습니다.
2. 새로 만든 구독으로 업데이트 하려면 페이지를 새로 고칩니다.
3. 드롭다운 목록에서 구독을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![구독 선택](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. 구독이 다른 테 넌 트 아래에 있는 경우 기존 창에서 테 넌 트를 전환할 수 없습니다. 이 창을 닫고 맨 위 막대에서 이니셜을 포함 하는 가장 오른쪽 아바타를 선택 하 여 테 넌 트를 전환할 수 있습니다. 위쪽에서 **다른 제작 리소스 선택** 을 클릭 하 여 창을 다시 엽니다.

    > [!div class="mx-imgBorder"]
    > ![디렉터리 전환](./media/migrate-authoring-key/switch-directories.png)

5. 구독과 연결 된 기존 LUIS authoring 리소스를 사용 하는 경우 드롭다운 목록에서 선택 합니다. 이 제작 리소스에 따라 생성 된 모든 응용 프로그램을 볼 수 있습니다.
6. 그렇지 않은 경우이 모달의 맨 아래에 있는 **새 제작 리소스 만들기** 를 클릭 합니다.
7.  새 제작 리소스를 만들 때 다음 정보를 제공합니다.

    > [!div class="mx-imgBorder"]
    > ![새 리소스 만들기](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **테 넌 트 이름** -Azure 구독이 연결 된 테 넌 트입니다. 기존 창에서 테 넌 트를 전환할 수 없습니다. 이 창을 닫고 화면 오른쪽 위에 있는 아바타를 선택 하 여 테 넌 트를 전환할 수 있습니다. 위쪽에서 **다른 제작 리소스 선택** 을 선택 하 여 창을 다시 엽니다.
    * **Azure 리소스 그룹 이름** -구독에서 선택 하는 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다. 현재 구독에 리소스 그룹이 없으면 LUIS 포털에서 리소스 그룹을 만들 수 없습니다. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) 로 이동 하 여 LUIS으로 이동 하 여 로그인 프로세스를 계속 합니다.
    * **Azure 리소스 이름** -제작 트랜잭션에 대 한 URL의 일부로 사용 되는 사용자 지정 이름입니다. 리소스 이름은 영숫자 문자인만 포함할 수 `-` 있고로 시작 하거나 끝날 수 없습니다 `-` . 다른 기호가 이름에 포함 되어 있으면 리소스 만들기가 실패 합니다.
    * **위치** -미국 서 부, 유럽 서부 및 동부 오스트레일리아를 포함 하 여 현재 LUIS에서 지원 되는 [세 가지 제작 위치](./luis-reference-regions.md) 중 하나에서 응용 프로그램을 작성 하도록 선택 합니다.
    * **가격 책정 계층** -기본적으로 F0 제작 가격 책정 계층을 선택 하는 것이 좋습니다. 추가 보안 계층을 찾고 있는 경우 Azure Portal에서 [고객 관리 키](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding) 를 만듭니다.
8. 이제 LUIS에 로그인 했습니다. 이제 응용 프로그램 만들기를 시작할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

* 새 리소스를 만들 때 리소스 이름에는 영숫자, '-'만 포함 해야 하며, '-'로 시작 하거나 끝날 수 없습니다. 그렇지 않으면 배포 취소가 실패합니다.
* [구독에 대 한 적절 한 권한이 있는지 확인 하 여 Azure 리소스를 만듭니다](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). 적절 한 권한이 없는 경우 구독 관리자에 게 문의 하 여 충분 한 권한을 부여 합니다.

## <a name="next-steps"></a>다음 단계

* [새 앱을 시작](luis-how-to-start-new-app.md) 하는 방법 알아보기