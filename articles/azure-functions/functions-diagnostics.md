---
title: Azure Functions 진단 개요
description: Azure Functions 진단을 사용 하 여 함수 앱 문제를 해결 하는 방법에 대해 알아봅니다.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122371"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 진단 개요

함수 앱을 실행 하는 경우 4xx 오류에서 오류를 트리거하기 때문에 발생할 수 있는 모든 문제에 대해 준비 하려고 합니다. Azure Functions 진단은 구성 또는 추가 비용 없이 함수 앱 문제를 해결 하는 데 도움이 되는 지능적이 고 대화형 환경입니다. 함수 앱에서 문제가 발생 하는 경우 Azure Functions 진단에서 잘못 된 기능을 가리킵니다. 이를 통해 보다 쉽고 빠르게 문제를 해결 하 고 문제를 해결할 수 있는 적절 한 정보를 안내해 줍니다. 이 문서에서는 Azure Functions 진단을 사용 하 여 일반적인 함수 앱 문제를 보다 신속 하 게 진단 하 고 해결 하는 방법의 기본 사항을 보여 줍니다.

## <a name="start-azure-functions-diagnostics"></a>Azure Functions 진단 시작

Azure Functions 진단을 시작 하려면:

1. [Azure Portal](https://portal.azure.com)의 함수 앱으로 이동 합니다.
1. 진단 **및 문제 해결** 을 선택 하 여 Azure Functions 진단을 엽니다.
1. 홈페이지 타일의 키워드를 사용 하 여 함수 앱의 문제를 가장 잘 설명 하는 범주를 선택 합니다. 검색 표시줄에서 문제를 가장 잘 설명 하는 키워드를 입력할 수도 있습니다. 예를 들어를 입력 `execution` 하 여 함수 앱 실행과 관련 된 진단 보고서 목록을 확인 하 고 홈 페이지에서 직접 열 수 있습니다.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Azure Functions 진단을 검색 합니다." border="true":::

## <a name="use-the-interactive-interface"></a>대화형 인터페이스 사용

함수 앱의 문제에 가장 잘 맞는 홈 페이지 범주를 선택 하면 Genie 라는 Azure Functions 진단의 대화형 인터페이스를 사용 하 여 앱의 문제를 진단 하 고 해결 하는 과정을 안내할 수 있습니다. Genie에서 제공 하는 타일 바로 가기를 사용 하 여 관심 있는 문제 범주의 전체 진단 보고서를 볼 수 있습니다. 타일 바로 가기를 통해 진단 메트릭에 직접 액세스할 수 있는 방법을 제공 합니다.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie는 Azure Functions 진단 인터페이스입니다." border="false":::

타일을 선택한 후 타일에 설명 된 문제와 관련 된 항목의 목록을 볼 수 있습니다. 이러한 항목에서는 전체 보고서에서 주목할 만한 정보를 제공 합니다. 이러한 항목 중 하나를 선택 하 여 문제를 자세히 조사 합니다. 또한 **전체 보고서 보기** 를 선택 하 여 단일 페이지의 모든 항목을 탐색할 수 있습니다.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="진단 보고서 미리 보기" border="false":::

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기

항목을 선택한 후에는 함수 앱과 관련 된 진단 보고서를 볼 수 있습니다. 진단 보고서는 상태 아이콘을 사용 하 여 앱에 특정 문제가 있는지 여부를 표시 합니다. 문제에 대 한 자세한 설명, 권장 조치, 관련 메트릭 및 유용한 문서를 볼 수 있습니다. 사용자 지정 된 진단 보고서는 함수 앱에서 실행 되는 일련의 검사를 통해 생성 됩니다. 진단 보고서는 함수 앱에서 문제를 어설션 매크로나 하는 데 유용한 도구 이며 문제를 해결 하는 데 안내 합니다.

## <a name="find-the-problem-code"></a>문제 코드 찾기

스크립트 기반 함수의 경우 **함수 앱 다운 또는 오류 보고** 에서 **함수 실행 및 오류** 를 사용 하 여 예외 또는 오류가 발생 하는 코드 줄의 범위를 좁힐 수 있습니다. 이 도구를 사용 하 여 근본 원인을 파악 하 고 특정 코드 줄에서 문제를 수정할 수 있습니다. 미리 컴파일된 c # 및 Java 함수에는이 옵션을 사용할 수 없습니다.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="함수 실행 오류에 대 한 진단 보고서" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="예외 정보를 표시 합니다." border="false":::

## <a name="next-steps"></a>다음 단계

[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)에서 Azure Functions 진단에 대해 질문 하거나 피드백을 제공할 수 있습니다. 사용자 `[Diag]` 의견의 제목에를 포함 합니다.

> [!div class="nextstepaction"]
> [함수 앱 모니터링](functions-monitoring.md)
