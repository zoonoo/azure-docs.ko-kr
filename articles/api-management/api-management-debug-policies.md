---
title: Visual Studio Code에서 Azure API Management 정책 디버그 | Microsoft Docs
description: Azure API Management Visual Studio Code 확장을 사용 하 여 Azure API Management 정책을 디버그 하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 4eb32243df219d721d7baae80984c45d0fc4cf25
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343021"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Visual Studio Code에서 Azure API Management 정책 디버그

Azure API Management [정책은](api-management-policies.md) API 게시자가 인증, 권한 부여, 제한, 캐싱, 변환 등의 교차 문제를 해결 하는 데 도움이 되는 강력한 기능을 제공 합니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 

이 문서에서는 [Visual Studio Code에 대 한 Azure API Management 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)을 사용 하 여 API Management 정책을 디버그 하는 방법을 설명 합니다. 

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 [자습서](get-started-create-service-instance.md) 를 먼저 따라 API Management 개발자 계층 인스턴스를 만듭니다.

[Visual Studio Code에 대 한 Azure API Management 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)의 [Visual Studio Code](https://code.visualstudio.com/) 및 최신 버전을 설치 합니다. 

## <a name="restrictions-and-limitations"></a>제한 사항

이 기능은 API Management 개발자 계층 에서만 사용할 수 있습니다. 각 API Management 인스턴스는 동시 디버깅 세션을 하나만 지원 합니다.

## <a name="initiate-a-debugging-session"></a>디버깅 세션 시작

1. Visual Studio Code 시작
2. Azure 확장에서 API Management 확장으로 이동 합니다.
3. 디버그할 API Management 인스턴스 찾기
4. 디버그할 API 및 작업 찾기
5. 작업을 마우스 오른쪽 단추로 클릭 하 고 **정책 디버깅 시작** 을 선택 합니다.

이 시점에서 확장 프로그램은 API Management gateway를 사용 하 여 디버깅 세션을 시작 하 고 설정 하려고 합니다.

![디버깅 시작](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>테스트 요청 보내기
디버깅 세션이 설정 되 면 확장에서 [REST 클라이언트 확장](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)을 활용 하 여이 작업에 대 한 테스트 HTTP 요청을 만들고 보낼 수 있는 새 편집기가 열립니다.

**Ocp-so** 요청에 이미 추가 된 것을 알 수 있습니다. 이 헤더가 필요 하며,이 값은 API Management 게이트웨이에서 디버깅 기능을 트리거하기 위해 서비스 수준, 모든 액세스 구독 키로 설정 되어야 합니다.

테스트 시나리오에 따라 편집기에서 HTTP 요청을 수정 합니다. 그런 다음 **요청 보내기** 를 클릭 하 여 API Management 게이트웨이로 테스트 요청을 보냅니다.

![테스트 요청 보내기](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>디버그 정책
테스트 HTTP 요청을 보낸 후에는이 작업의 유효한 정책이 표시 되 고 첫 번째 유효 정책에서 중지 되는 디버깅 창이 열립니다. 

![디버그 정책](media/api-management-debug-policies/main-window.png)

정책 파이프라인을 따르려면 개별 정책을 한 단계씩 실행 하거나 정책에서 중단점을 설정 하 고 해당 정책에 대 한 단계를 직접 수행할 수 있습니다. 

**변수** 패널에서 시스템 생성 및 사용자가 만든 변수의 값을 검사할 수 있습니다. **중단점** 패널에서 설정 된 모든 중단점의 목록을 볼 수 있습니다. **호출 스택** 패널에서 현재 유효 정책 범위를 볼 수 있습니다. 

정책을 실행 하는 동안 오류가 발생 하면 오류가 발생 한 정책에 오류 정보가 표시 됩니다. 

![예외](media/api-management-debug-policies/exception.png)

> [!TIP]
> 완료 되 면 **중지** 단추를 클릭 하 여 디버그 세션을 종료 해야 합니다.


## <a name="next-steps"></a>다음 단계

+ [Visual Studio Code API Management 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement)에 대해 자세히 알아보세요. 
+ [GitHub 리포지토리에서](https://github.com/Microsoft/vscode-apimanagement) 문제 보고

