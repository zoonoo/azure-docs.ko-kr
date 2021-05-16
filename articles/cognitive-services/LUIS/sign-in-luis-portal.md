---
title: LUIS 포털에 로그인
description: 새 사용자가 LUIS 포털에 로그인하는 경우 로그인 환경은 현재 사용자 계정에 따라 약간 달라집니다.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101706778"
---
# <a name="sign-in-to-luis-portal"></a>LUIS 포털에 로그인

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

이 문서를 사용하여 LUIS 포털을 시작하고 제작 리소스를 만드세요. 이 문서의 단계를 완료한 후에는 LUIS 앱을 만들고 게시할 수 있습니다.

## <a name="access-the-portal"></a>포털 액세스


1. LUIS을 시작하려면 [LUIS 포털](https://www.luis.ai)로 이동합니다. 아직 구독이 없는 경우 [체험 계정](https://azure.microsoft.com//free/cognitive-services/)을 만들고 포털로 다시 돌아가라는 메시지가 표시됩니다.
2. 페이지를 새고 고침하여 새로 만든 구독으로 업데이트합니다.
3. 드롭다운 목록에서 구독을 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![구독 선택](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. 구독이 다른 테넌트 아래에 있는 경우 기존 창에서 테넌트를 전환할 수 없습니다. 이 창을 닫고 상단 표시줄에서 이니셜이 포함된 맨 오른쪽 아바타를 선택하여 테넌트를 전환할 수 있습니다. 창을 다시 열려면 상단에서 **다른 제작 리소스 선택** 을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![디렉터리 전환](./media/migrate-authoring-key/switch-directories.png)

5. 구독과 연결된 기존 LUIS 작성 리소스를 사용하는 경우 드롭다운 목록에서 선택합니다. 이 제작 리소스에 따라 생성된 모든 애플리케이션을 볼 수 있습니다.
6. 그렇지 않은 경우 이 모달 하단에서 **새 제작 리소스 만들기** 를 클릭합니다.
7.  새 제작 리소스를 만들 때 다음 정보를 제공합니다.

    > [!div class="mx-imgBorder"]
    > ![새 리소스 만들기](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **테넌트 이름** - Azure 구독이 연결된 테넌트입니다. 기존 창에서 테넌트를 전환할 수 없습니다. 이 창을 닫고 화면 오른쪽 상단에서 이니셜이 포함된 아바타를 선택하여 테넌트를 전환할 수 있습니다. 창을 다시 열려면 상단에서 **다른 제작 리소스 선택** 을 선택합니다.
    * **Azure 리소스 그룹 이름** - 사용자가 구독에서 선택한 사용자 지정 리소스 그룹 이름입니다. 리소스 그룹을 사용하면 액세스 및 관리를 위해 Azure 리소스를 그룹화할 수 있습니다. 현재 구독에 리소스 그룹이 없으면 LUIS 포털에서 리소스 그룹을 만들 수 없습니다. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup)로 이동하여 리소스 그룹을 만든 다음, LUIS로 이동하여 로그인 프로세스를 진행합니다.
    * **Azure 리소스 이름** - 사용자가 선택한 사용자 지정 이름으로, 제작 트랜잭션의 URL 중 일부로 사용됩니다. 리소스 이름은 영숫자 문자와 `-`만 사용해야 하며 `-`로 시작하거나 끝나면 안 됩니다. 다른 기호가 이름에 포함되어 있으면 리소스 만들기가 실패합니다.
    * **위치** - 미국 서부, 서유럽 및 오스트레일리아 동부를 포함하여 현재 LUIS에서 지원하는 [세 제작 위치](./luis-reference-regions.md) 중 하나에서 애플리케이션을 제작하도록 선택합니다.
    * **가격 책정 계층** - 기본적으로 F0 제작 가격 책정 계층을 선택하는 것이 좋습니다. 추가 보안 계층을 찾고 있는 경우 Azure Portal에서 [고객 관리형 키](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding)를 만듭니다.
8. 이제 LUIS에 로그인했습니다. 이제 애플리케이션 만들기를 시작할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

* 새 리소스를 만들 때 리소스 이름에는 영숫자 문자와 '-'만 사용해야 하며 '-'로 시작하거나 끝나면 안 됩니다. 그렇지 않으면 배포 취소가 실패합니다.
* [Azure 리소스를 만들 수 있도록 구독에 대한 적절한 권한](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)이 있는지 확인합니다. 적절한 권한이 없는 경우 구독 관리자에게 문의하여 충분한 권한을 부여하세요.

## <a name="next-steps"></a>다음 단계

* [새 앱 시작](luis-how-to-start-new-app.md) 방법 알아보기