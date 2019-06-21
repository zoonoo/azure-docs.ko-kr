---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 3893b79cee96c3928897f64f3601ebe4c490ebdd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182311"
---
### <a name="enable-logging-with-diagnostics-settings"></a>진단 설정에 대한 로깅 사용

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

2. **진단 설정**을 선택합니다.

3. **진단 켜기**를 선택합니다.

   ![진단 켜기](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. 진단 설정에 이름을 지정합니다.

5. 로그를 보낼 위치를 선택합니다. 다음 세 가지 옵션을 조합해서 선택할 수 있습니다.

   * 저장소 계정에 보관
   * 이벤트 허브로 스트림
   * Log Analytics에 보내기

6. 모니터링하려는 작업을 선택하고 해당 작업에 대한 로그를 사용하도록 설정합니다. 진단 설정이 보고할 수 있는 작업은 같습니다.

   * 연결
   * 디바이스 원격 분석
   * 클라우드-장치 메시지
   * 디바이스 ID 작업
   * 파일 업로드
   * 메시지 라우팅
   * 클라우드-장치 쌍 작업
   * 디바이스-클라우드 쌍 작업
   * 쌍 작업
   * 업무 작업
   * 직접 메서드  
   * 분산 추적(미리 보기)
   * 구성
   * 장치 스트림
   * 장치 메트릭

6. 새 설정을 저장합니다. 

PowerShell 사용하여 진단 설정을 켜려면 다음 코드를 사용합니다.

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

새 설정은 약 10분 후에 적용됩니다. 그런 다음, 구성된 보관 대상의 **진단 설정** 블레이드에 로그가 나타납니다. 진단을 구성하는 방법에 대한 자세한 내용은 [Azure 리소스에서 로그 데이터 수집 및 사용](../articles/azure-monitor/platform/diagnostic-logs-overview.md)을 참조하세요.
