---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
origin.date: 03/05/2019
ms.date: 03/25/2019
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403865"
---
Visual Studio의 Azure Functions 프로젝트 템플릿은 Azure에서 함수 앱에 게시할 수 있는 프로젝트를 만듭니다. 함수 앱을 사용하여 리소스를 관리, 배포 및 공유하기 위한 논리적 단위로 함수를 그룹화할 수 있습니다.

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 차례로 선택합니다.

2. **새 프로젝트** 대화 상자에서 **설치됨** > **Visual C#** > **클라우드** > **Azure Functions**를 차례로 선택합니다. 프로젝트 이름을 입력하고 **확인**을 선택합니다. 함수 앱 이름은 C# 네임스페이스로 유효해야 하므로 밑줄, 하이픈 또는 기타 영숫자가 아닌 문자는 사용하지 마세요.

    ![Visual Studio에서 함수를 만드는 새 프로젝트 대화 상자](./media/functions-vstools-create/functions-vs-new-project.png)

3. 이미지 아래의 표에 지정된 설정을 사용합니다.

    ![Visual Studio의 새 함수 대화 상자](./media/functions-vstools-create/functions-vs-new-function.png) 

    | 설정      | 제안 값  | 설명                      |
    | ------------ |  ------- |----------------------------------------- |
    | **버전** | Azure Functions 2.x <br />(.NET Core) | .NET Core를 지원하는 Azure Functions 버전 2.x 런타임을 사용하는 함수 프로젝트를 만듭니다. Azure Functions 1.x는 .NET Framework를 지원합니다. 자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](../articles/azure-functions/functions-versions.md)을 참조하세요.   |
    | **템플릿** | HTTP 트리거 | HTTP 요청에서 트리거되는 함수를 만듭니다. |
    | **Storage 계정**  | Storage 에뮬레이터 | HTTP 트리거는 Azure Storage 계정 연결을 사용하지 않습니다. 다른 모든 트리거 유형은 유효한 저장소 계정 연결 문자열이 필요합니다. |
    | **액세스 권한** | 익명 | 만들어진 함수를 모든 클라이언트에서 키를 제공하지 않고 트리거할 수 있습니다. 이 권한 부여 설정을 통해 새 함수를 쉽게 테스트할 수 있습니다. 키 및 권한 부여에 대한 자세한 내용은 [HTTP 및 웹후크 바인딩](../articles/azure-functions/functions-bindings-http-webhook.md)에서 [권한 부여 키](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)를 참조하세요. |
    
    > [!NOTE]
    > **액세스 권한**을 `Anonymous`로 설정해야 합니다. 기본 수준인 `Function`을 선택하면 함수 엔드포인트에 액세스하도록 요구하는 요청에 [함수 키](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)를 제공해야 합니다.
    
4. **확인**을 선택하여 함수 프로젝트 및 HTTP 트리거 함수를 만듭니다.

