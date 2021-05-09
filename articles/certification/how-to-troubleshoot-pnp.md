---
title: IoT 플러그 앤 플레이 디바이스 문제 해결
description: IoT 플러그 앤 플레이 디바이스를 인증하는 파트너에게 권장되는 문제 해결 단계의 가이드입니다.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 591436de14916a919f0179978825b88dcc80117c
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108129732"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>IoT 플러그 앤 플레이 인증 프로젝트 문제 해결

IoT 플러그 앤 플레이 인증 프로젝트의 연결 및 테스트 단계 중에 AICS(Azure for IoT Certification Service) 테스트를 통과하지 못하도록 하는 몇 가지 시나리오가 발생할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 로그인되어 있고 [Azure Certified Device 포털](https://certify.azure.com)에서 만든 디바이스용 프로젝트가 있어야 합니다. 자세한 내용은 [자습서](tutorial-01-creating-your-project.md)를 참조하세요.

## <a name="when-aics-tests-arent-passing"></a>AICS 테스트를 통과하지 못하는 경우

여러 가지 원인으로 인해 AICS 테스트를 통과하지 못할 수 있습니다. 다음 단계에 따라 일반적인 문제를 확인하고 디바이스 문제를 해결합니다.

1. DPS 프로비저닝 중에 디바이스 코드가 모델 ID 페이로드를 설정하고 있는지 다시 확인합니다. AICS가 디바이스의 유효성을 검사하려면 이 작업이 필요합니다.
1. `View Logs` 단추를 눌러 이전 테스트 실행의 원격 분석 로그를 확인하여 테스트가 실패하는 원인을 식별할 수 있습니다. 테스트 메시징 및 원시 데이터를 둘 다 검토할 수 있습니다.  

    ![테스트 데이터 검토](./media/images/review-logs.png)

1. 로그가 `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected`를 표시하는 일부 경우에는 디바이스를 다시 부팅하고 디바이스 프로비저닝 프로세스를 다시 시작해 봅니다.
1. 자동화된 테스트가 계속 실패할 경우 대체할 결과의 `request a manual review`을 수행할 수 있습니다. 이렇게 하면 Azure Certified Device 팀을 통한 **수동 유효성 검사** 에 대한 요청이 트리거됩니다.  

    ![수동 검토 요청](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>“통과했지만 경고 발생”이 표시되는 경우

테스트를 실행하는 동안 `Passed with warnings`의 결과가 표시되면 이는 테스트 기간 중에 일부 원격 분석이 수신되지 않았음을 의미합니다. 원인은 원격 분석이 더 긴 시간 간격 또는 사용할 수 없는 외부 트리거에 종속되기 때문일 수 있습니다. 검토 팀에서 향후 **수동 유효성 검사** 가 필요한지 확인하는 동안에도 검토를 위해 디바이스를 계속 제출할 수 있습니다.

## <a name="when-you-need-help-with-the-model-repository"></a>모델 리포지토리와 관련된 도움이 필요한 경우

모델 리포지토리와 관련된 IoT 플러그 앤 플레이 문제는 [디바이스 모델 리포지토리에 관한 문서 지침](../iot-pnp/concepts-model-repository.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 가이드를 통해 IoT 플러그 앤 플레이 인증 과정을 계속 진행할 수 있기를 바랍니다. AICS를 통과한 후 자습서를 진행하여 디바이스를 제출하고 게시할 수 있습니다.

- [자습서: 디바이스 테스트](tutorial-03-testing-your-device.md)