---
title: Azure 정적 Web Apps에서 배포 토큰 다시 설정 (미리 보기)
description: Azure 정적 Web Apps 사이트에서 토큰 다시 설정
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745540"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Azure 정적 Web Apps에서 배포 토큰 다시 설정 (미리 보기)

새 Azure 정적 Web Apps 사이트를 만들 때 Azure는 배포 하는 동안 응용 프로그램을 식별 하는 데 사용 되는 토큰을 생성 합니다. 프로 비전 하는 동안이 토큰은 GitHub 리포지토리에서 암호로 저장 됩니다. 이 문서에서는이 토큰을 사용 하 고 관리 하는 방법을 설명 합니다.

일반적으로 배포 토큰에 대해 걱정할 필요가 없지만 토큰을 검색 하거나 다시 설정 해야 하는 몇 가지 이유는 다음과 같습니다.

* **토큰 손상**: 토큰이 외부 파티에 노출 되는 경우 다시 설정 합니다.
* **별도의 github 리포지토리에서 배포**: 별도의 github 리포지토리에서 수동으로 배포 하는 경우 새 리포지토리에서 배포 토큰을 설정 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Static Web Apps로 구성된 기존 GitHub 리포지토리입니다.
- 없는 경우 [첫 번째 정적 앱 빌드](getting-started.md)를 참조하세요.

## <a name="reset-a-deployment-token"></a>배포 토큰 다시 설정

1. Azure 정적 Web Apps 사이트의 _개요_ 페이지에서 **배포 토큰 관리** 링크를 클릭 합니다.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="배포 토큰 관리":::

1. **토큰 다시 설정** 단추를 클릭 합니다.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="배포 토큰 다시 설정":::

1. _배포 토큰_ 필드에 새 토큰을 표시 한 후 **클립보드로 복사** 아이콘을 클릭 하 여 토큰을 복사 합니다.


## <a name="update-a-secret-in-the-github-repository"></a>GitHub 리포지토리에서 비밀 업데이트

자동화 된 배포를 계속 실행 하려면 토큰을 다시 설정한 후 해당 GitHub 리포지토리에서 새 값을 설정 해야 합니다.

1. GitHub에서 프로젝트의 리포지토리로 이동 하 고 **설정** 탭을 클릭 합니다.
1. **비밀** 메뉴 항목을 클릭 합니다. _AZURE_STATIC_WEB_APPS_API_TOKEN_ 이름이 지정 된 정적 웹 앱을 프로 비전 하는 동안 생성 된 암호를 찾을 수 있습니다. _리포지토리 암호_ 섹션에 있습니다.

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="리포지토리 암호 나열":::

    > [!NOTE]
    > 이 리포지토리의 여러 분기에 대해 Azure 정적 Web Apps 사이트를 만든 경우 여러 _AZURE_STATIC_WEB_APPS_API_TOKEN_ 이 표시 됩니다. 이 목록의 비밀입니다. 정적 Web Apps 사이트의 _개요_ 탭에서 _워크플로 편집_ 필드에 나열 된 파일 이름과 일치 하는 항목을 선택 합니다.

1. **업데이트** 단추를 클릭 하 여 편집기를 엽니다.
1. 배포 토큰의 값을 _값_ 필드에 **붙여넣습니다** .
1. **비밀 업데이트** 를 클릭 합니다.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="리포지토리 비밀 업데이트 중":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [정적 사이트 생성기에서 게시](publish-gatsby.md)
