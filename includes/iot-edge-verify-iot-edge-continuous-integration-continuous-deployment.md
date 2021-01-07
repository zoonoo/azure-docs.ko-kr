---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303054"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>빌드 및 릴리스 파이프라인을 사용하여 IoT Edge CI/CD 확인

빌드 작업을 트리거하려면 소스 코드 리포지토리에 커밋을 푸시하거나 수동으로 트리거할 수 있습니다. 이 섹션에서는 CI/CD 파이프라인을 수동으로 트리거하여 작동하는지 테스트합니다. 그런 다음, 배포에 성공했는지 확인합니다.

1. 왼쪽 창 메뉴에서 **파이프라인** 을 선택 하 고이 문서의 시작 부분에서 만든 빌드 파이프라인을 엽니다.

2. 오른쪽 위에 있는 **파이프라인 실행** 단추를 선택 하 여 빌드 파이프라인에서 빌드 작업을 트리거할 수 있습니다.

    ![파이프라인 실행 단추를 사용 하 여 빌드 파이프라인 수동으로 트리거](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. **파이프라인 실행** 설정을 검토 합니다. 그런 다음 **실행**을 선택 합니다.

    ![파이프라인 실행 옵션을 지정 하 고 실행을 선택 합니다.](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. **에이전트 작업 1** 을 선택 하 여 실행의 진행 상태를 확인 합니다. 작업을 선택 하 여 작업 출력의 로그를 검토할 수 있습니다. 

    ![작업의 로그 출력을 검토 합니다.](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. 빌드 파이프라인이 성공적으로 완료 되 면 **개발** 단계로 릴리스를 트리거합니다. 성공적인 **dev** 릴리스는 대상 IoT Edge 장치에 IoT Edge 배포를 만듭니다.

    ![Dev로 릴리스](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. **개발** 단계를 클릭 하 여 릴리스 로그를 확인 합니다.

    ![릴리스 로그](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)