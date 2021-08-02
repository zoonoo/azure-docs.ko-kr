---
title: Azure Static Web Apps에서 배포 토큰 초기화
description: Azure Static Web Apps 사이트에서 토큰 초기화
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: 8d516a448240e32f58318689d985bc0aad906b9f
ms.sourcegitcommit: 0ce834cd348bb8b28a5f7f612c2807084cde8e8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109814013"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps"></a>Azure Static Web Apps에서 배포 토큰 초기화

새 Azure Static Web Apps 사이트를 만들 때 Azure는 배포 중 애플리케이션을 식별하는 데 사용되는 토큰을 생성합니다. 프로비저닝 중에 해당 토큰은 GitHub 리포지토리에서 비밀로 저장됩니다. 이 문서에서는 해당 토큰을 사용하고 관리하는 방법을 설명합니다.

일반적으로 배포 토큰에 대해 걱정할 필요가 없지만, 토큰을 검색하거나 초기화해야 할 때가 있으며, 그 이유는 다음과 같습니다.

* **토큰 손상**: 토큰이 외부 파티에 노출되는 경우 초기화합니다.
* **별도의 GitHub 리포지토리에서 배포**: 별도의 GitHub 리포지토리에서 수동으로 배포하는 경우 새 리포지토리에서 배포 토큰을 설정해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Static Web Apps로 구성된 기존 GitHub 리포지토리입니다.
- 없는 경우 [첫 번째 정적 앱 빌드](getting-started.md)를 참조하세요.

## <a name="reset-a-deployment-token"></a>배포 토큰 초기화

1. Azure Static Web Apps 사이트의 ‘개요’ 페이지에서 **배포 토큰 관리** 링크를 클릭합니다.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="배포 토큰 관리":::

1. **Reset token(토큰 초기화)** 단추를 클릭합니다.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="배포 토큰 초기화":::

1. ‘배포 토큰’ 필드에 새 토큰을 표시한 후 **클립보드로 복사** 아이콘을 클릭하여 토큰을 복사합니다.


## <a name="update-a-secret-in-the-github-repository"></a>GitHub 리포지토리에서 비밀 업데이트

자동화된 배포를 계속 실행하려면 토큰을 초기화한 후 해당 GitHub 리포지토리에서 새 값을 설정해야 합니다.

1. GitHub에서 프로젝트 리포지토리로 이동하고 **설정** 탭을 클릭합니다.
1. **비밀** 메뉴 항목을 클릭합니다. ‘리포지토리 암호’ 섹션에서 Static Web App 프로비저닝 중에 생성된 _AZURE_STATIC_WEB_APPS_API_TOKEN_ 으로 시작하는 이름의 비밀을 찾을 수 있습니다.

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="리포지토리 비밀 목록":::

    > [!NOTE]
    > 해당 리포지토리의 여러 분기에 대해 Azure Static Web Apps 사이트를 만든 경우 해당 목록에 _AZURE_STATIC_WEB_APPS_API_TOKEN_ 으로 시작하는 여러 비밀이 표시됩니다. Static Web Apps 사이트의 ‘개요’ 탭에 있는 ‘워크플로 편집’ 필드에 나열된 파일 이름과 일치하는 항목을 선택합니다. 

1. **업데이트** 단추를 클릭하여 편집기를 엽니다.
1. 배포 토큰의 값을 ‘값’ 필드에 **붙여넣습니다**.
1. **비밀 업데이트** 를 클릭합니다.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="리포지토리 비밀 업데이트":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [정적 사이트 생성기에서 게시](publish-gatsby.md)
