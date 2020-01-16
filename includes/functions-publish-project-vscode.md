---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 66c100ecd40e9f0899f7686397339ab849315c3e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020870"
---
## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

Visual Studio Code를 사용하면 Azure에 직접 함수 프로젝트를 게시할 수 있습니다. 프로세스를 통해 함수 앱 및 관련된 리소스를 Azure 구독에서 만듭니다. 함수 앱은 함수를 위한 실행 컨텍스트를 제공합니다. 프로젝트는 패키지되어 Azure 구독에서 새 함수 앱에 배포됩니다.

기본적으로 Visual Studio Code는 함수 앱을 만드는 데 필요한 모든 Azure 리소스를 만듭니다. 이러한 리소스의 이름은 선택한 함수 앱 이름을 기반으로 합니다. 만들어진 리소스를 전체적으로 제어해야 하는 경우에는 대신 [고급 옵션을 사용하여 게시](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options)할 수 있습니다.

이 섹션에서는 Azure에서 새 함수 앱을 만든다고 가정합니다.

> [!IMPORTANT]
> 기존 함수 앱에 게시하면 Azure에서 해당 앱의 콘텐츠를 덮어씁니다.

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 `Azure Functions: Deploy to function app...`을 검색하여 선택합니다.

1. 로그인하지 않으면 **Azure에 로그인**하라는 메시지가 표시됩니다. 또한 **무료 Azure 계정을 만들** 수 있습니다. 브라우저에서 성공적으로 로그인한 후 Visual Studio Code로 다시 이동합니다. 

1. 여러 구독이 있다면 함수 앱에 대해 **구독을 선택**한 다음, **+ Azure에서 새 함수 앱 만들기**를 선택합니다.

1. 함수 앱을 식별하는 전역적으로 고유한 이름을 입력하고 Enter 키를 누릅니다. 함수 앱 이름에 대한 유효한 문자는 `a-z`, `0-9` 및 `-`입니다.

    Enter 키를 누르면 다음 Azure 리소스가 구독에서 생성됩니다.

    * **[리소스 그룹](../articles/azure-resource-manager/management/overview.md)** : 만든 Azure 리소스를 모두 포함합니다. 이름은 함수 앱 이름을 기반으로 합니다.
    * **[스토리지 계정](../articles/storage/common/storage-account-create.md)** : 표준 스토리지 계정은 함수 앱 이름을 기반으로 하는 고유한 이름으로 만들어집니다.
    * **[호스팅 계획](../articles/azure-functions/functions-scale.md)** : 소비 계획은 서버리스 함수 앱을 호스트할 미국 서부 지역에 생성됩니다.
    * **함수 앱**: 프로젝트가 이 새 함수 앱에 배포되고 실행됩니다.

    함수 앱을 만들고 배포 패키지가 적용되면 알림이 표시됩니다. 이 알림에서 **출력 보기**를 선택하여 사용자가 만든 Azure 리소스를 포함한 만들기 및 배포 결과를 표시합니다.

1. **Azure: Functions** 영역에서 구독에 있는 새 함수 앱을 확장합니다. **함수**를 확장하고, **HttpTrigger**를 마우스 오른쪽 단추로 클릭한 다음, **함수 URL 복사**를 선택합니다.

    ![새 HTTP 트리거에 대한 함수 URL 복사](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
