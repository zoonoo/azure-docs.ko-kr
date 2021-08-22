---
title: Azure Portal에서 문제 해결 - Azure IoT Edge | Microsoft Docs
description: Azure Portal의 문제 해결 페이지를 사용하여 IoT Edge 디바이스 및 모듈 모니터링
author: kgremban
ms.author: kgremban
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e0761262463d69fe80f7dc15ed1a5944dcc235bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535656"
---
# <a name="troubleshoot-iot-edge-devices-from-the-azure-portal"></a>Azure Portal에서 IoT Edge 디바이스 문제 해결

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge는 Azure Portal에서 모듈을 모니터링하고 문제를 해결하는 간소화된 방법을 제공합니다. 문제 해결 페이지는 IoT Edge 에이전트의 직접 메서드에 대한 래퍼로서, 이를 통해 배포된 모듈에서 로그를 쉽게 검색하고 원격으로 다시 시작할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

포털에서 이 문제 해결 기능을 전체적으로 사용하려면 장기 지원 분기에 있는 경우 IoT Edge 버전 1.1.3 이상이 필요하고 안정적인 최신 분기에 있는 경우 버전 1.2.1 이상이 필요합니다. IoT Edge 호스트 구성 요소와 edgeAgent 모듈은 모두 이러한 버전에 있어야 합니다.

## <a name="access-the-troubleshooting-page"></a>문제 해결 페이지에 액세스

IoT Edge 디바이스 세부 정보 페이지 또는 IoT Edge 모듈 세부 정보 페이지를 통해 포털의 문제 해결 페이지에 액세스할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

1. 왼쪽 창의 메뉴에서 **IoT Edge** 를 선택합니다.

1. 디바이스 목록에서 모니터링할 IoT Edge 디바이스를 선택합니다.

1. 디바이스 세부 정보 페이지의 메뉴에서 **문제 해결** 을 선택하거나 검사할 특정 모듈의 런타임 상태를 선택할 수 있습니다.

   ![디바이스 세부 정보 페이지에서 문제 해결 또는 모듈 런타임 상태 선택](./media/troubleshoot-in-portal/troubleshoot-from-device-details.png)

1. 디바이스 세부 정보 페이지에서 모듈 이름을 선택하여 모듈 세부 정보 페이지를 열 수도 있습니다. 메뉴에서 **문제 해결** 을 선택할 수 있습니다.

   ![모듈 세부 정보 페이지에서 문제 해결 선택](./media/troubleshoot-in-portal/troubleshoot-from-module-details.png)

## <a name="view-module-logs-in-the-portal"></a>포털에서 모듈 로그 보기

**문제 해결** 페이지에서 IoT Edge 디바이스의 실행 중인 모듈에 대해 로그를 보고 다운로드할 수 있습니다.

이 페이지에는 최대 1,500개의 로그 줄 제한이 있으며, 그보다 긴 로그는 잘립니다. 로그가 너무 크면 모듈 로그를 가져오려는 시도가 실패합니다. 이 경우 더 적은 데이터를 검색하도록 시간 범위 필터를 변경하거나, 더 큰 로그 파일을 수집하려면 [IoT Edge 배포에서 로그를 검색](how-to-retrieve-iot-edge-logs.md)할 수 있는 직접 메서드 사용을 고려할 수 있습니다.

드롭다운 메뉴를 사용하여 검사할 모듈을 선택합니다.

![드롭다운 메뉴에서 모듈 선택](./media/troubleshoot-in-portal/select-module.png)

기본적으로 이 페이지에는 마지막 15분 동안의 로그가 표시됩니다. **시간 범위** 필터를 선택하여 다른 로그를 확인합니다. 슬라이더를 사용하여 지난 60분 이내의 시간 창을 선택하거나 **Enter time instead**(대신 시간 입력)를 확인하여 특정 날짜/시간 창을 선택합니다.

![시간 범위 선택](./media/troubleshoot-in-portal/select-time-range.png)

검사하려는 시간 범위 동안 문제를 해결하려는 모듈의 로그가 있으면 **찾기** 필터를 사용하여 로그에서 특정 줄을 검색할 수 있습니다. 경고 또는 오류를 필터링하거나 검색할 특정 용어 또는 문구를 제공할 수 있습니다. **찾기** 기능은 좀 더 복잡한 검색을 위해 일반 텍스트 검색 또는 [.NET 정규식](/dotnet/standard/base-types/regular-expression-language-quick-reference)을 지원합니다.

모듈 로그를 텍스트 파일로 다운로드할 수 있습니다. 다운로드한 로그 파일에는 로그에 적용한 활성 필터가 반영됩니다.

>[!TIP]
>포털의 요청에 대한 응답으로 로그를 수집하면 디바이스의 CPU 사용률이 일시적으로 급증합니다. 이는 예상되는 동작이며, 작업이 완료된 후에는 사용률이 안정화됩니다.

## <a name="restart-modules"></a>모듈 다시 시작

**문제 해결** 페이지에는 모듈을 다시 시작하는 기능이 포함되어 있습니다. 이 옵션을 선택하면 선택한 모듈을 다시 시작하도록 IoT Edge 에이전트에 명령이 전송됩니다. 모듈을 다시 시작해도 다시 시작하기 전에 로그를 검색하는 기능에는 영향을 미치지 않습니다.

![문제 해결 페이지에서 모듈 다시 시작](./media/troubleshoot-in-portal/restart-module.png)

## <a name="next-steps"></a>다음 단계

[IoT Edge 디바이스 문제 해결](troubleshoot.md)에 대한 추가 팁을 찾아보거나 [일반적인 문제 및 해결 방법](troubleshoot-common-errors.md)에 대해 알아봅니다. 

추가 질문이 있으면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만들어 도움을 받으세요.
