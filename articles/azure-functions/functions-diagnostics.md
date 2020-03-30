---
title: Azure 함수 진단 개요
description: Azure Functions 진단을 통해 함수 앱에서 문제를 해결하는 방법을 알아봅니다.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834043"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure 함수 진단 개요

함수 앱을 실행할 때 4xx 오류에서 트리거 오류에 이르기까지 발생할 수 있는 모든 문제에 대비해야 합니다. Azure Functions 진단은 구성이나 추가 비용 없이 함수 앱 문제를 해결하는 데 도움이 되는 지능형 대화형 환경입니다. 함수 앱에 문제가 발생하면 Azure Functions 진단은 올바른 정보로 안내하여 문제를 보다 쉽고 빠르게 해결하고 해결하는 데 문제가 있는 문제점을 지적합니다. 이 문서에서는 일반적인 함수 앱 문제를 보다 신속하게 진단하고 해결하기 위해 Azure Functions 진단을 사용하는 방법에 대한 기본 을 보여 주며 있습니다.

## <a name="start-azure-functions-diagnostics"></a>Azure 함수 진단 시작

Azure 함수 진단에 액세스하려면 다음을 수행하십시오.

1. [Azure 포털에서](https://portal.azure.com)함수 앱으로 이동합니다.
2. 플랫폼 **기능** 탭을 선택합니다.
3. Azure Functions 진단을 여는 **리소스 관리에서** **문제 진단을 선택하고 해결합니다.**
4. 홈페이지 타일의 키워드를 사용하여 함수 앱의 문제를 가장 잘 설명하는 범주를 선택합니다. 검색 표시줄에서 문제를 가장 잘 설명하는 키워드를 입력할 수도 있습니다. 예를 들어 함수 `execution` 앱 실행과 관련된 진단 보고서 목록을 보고 홈페이지에서 직접 열도록 입력할 수 있습니다.

![홈페이지](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>대화형 인터페이스 사용

함수 앱의 문제에 가장 잘 맞는 홈페이지 범주를 선택하면 Azure Functions 진단의 대화형 인터페이스인 Genie가 앱의 문제를 진단하고 해결하는 데 안내할 수 있습니다. 지니에서 제공하는 타일 바로 가기를 사용하여 관심 있는 문제 범주의 전체 진단 보고서를 볼 수 있습니다. 타일 바로 가기는 진단 메트릭에 액세스하는 직접적인 방법을 제공합니다.

![요정](./media/functions-diagnostics/genie.png)

타일을 선택한 후 타일에 설명된 문제와 관련된 항목 목록을 볼 수 있습니다. 이러한 항목은 전체 보고서의 주목할 만한 정보의 조각을 제공합니다. 이러한 항목 중 에서 문제를 더 자세히 조사할 수 있습니다. 또한 **전체 보고서 보기를** 선택하여 한 페이지의 모든 주제를 탐색할 수 있습니다.

![진단 보고서 미리 보기](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>진단 보고서 보기

주제를 선택한 후 함수 앱과 관련된 진단 보고서를 볼 수 있습니다. 진단 보고서는 상태 아이콘을 사용하여 앱에 특정 문제가 있는지 나타냅니다. 문제에 대한 자세한 설명, 권장 작업, 관련 메트릭 및 유용한 문서가 표시됩니다. 사용자 지정된 진단 보고서는 함수 앱에서 실행되는 일련의 검사에서 생성됩니다. 진단 보고서는 함수 앱의 문제를 정확히 찾아내고 문제를 해결하는 데 유용한 도구가 될 수 있습니다.

## <a name="find-the-problem-code"></a>문제 코드 찾기

스크립트 기반 함수의 경우 함수 앱 다운 **또는 보고 오류에서** **함수 실행 및 오류를** 사용하여 예외 또는 오류를 일으키는 코드 줄을 좁힐 수 있습니다. 이 기능은 근본 원인을 파악하고 특정 코드 줄의 문제를 해결하는 데 유용한 도구가 될 수 있습니다. 미리 컴파일된 C# 및 Java 함수에는 이 옵션을 사용할 수 없습니다.

![기능 실행 오류에 대한 진단 보고서](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![기능 예외](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>다음 단계

[UserVoice](https://feedback.azure.com/forums/355860-azure-functions)에서 Azure Functions 진단에 대한 질문을 하거나 피드백을 제공할 수 있습니다. 피드백 `[Diag]` 제목에 포함하십시오.

> [!div class="nextstepaction"]
> [기능 앱 모니터링](functions-monitoring.md)
