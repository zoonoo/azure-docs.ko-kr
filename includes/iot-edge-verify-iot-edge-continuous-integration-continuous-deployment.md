---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: b5450e4846c3c49c89830ae65c50a95ee0c8d6eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803363"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>빌드 및 릴리스 파이프라인을 사용하여 IoT Edge CI/CD 확인

빌드 작업을 트리거하려면 소스 코드 리포지토리에 커밋을 푸시하거나 수동으로 트리거할 수 있습니다. 이 섹션에서는 CI/CD 파이프라인을 수동으로 트리거하여 작동하는지 테스트합니다. 그런 다음, 배포에 성공했는지 확인합니다.

1. 왼쪽 창 메뉴에서 **파이프라인** 을 선택하고 이 문서의 시작 부분에서 만든 빌드 파이프라인을 엽니다.

2. 다음 스크린샷과 같이 오른쪽 위에서 **파이프라인 실행** 단추를 선택하여 빌드 파이프라인에서 빌드 작업을 트리거할 수 있습니다.

    ![파이프라인 실행 단추를 사용하여 수동으로 빌드 파이프라인 트리거](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. **파이프라인 실행** 설정을 검토합니다. 그런 다음, **실행** 을 선택합니다.

    ![파이프라인 실행 옵션을 지정하고 실행을 선택합니다.](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. **에이전트 작업 1** 을 선택하여 실행의 진행률을 확인합니다. 작업을 선택하여 작업 출력의 로그를 검토할 수 있습니다. 

    ![작업의 로그 출력 검토](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. 빌드 파이프라인이 성공적으로 완료되면 **dev** 스테이지로 릴리스가 트리거됩니다. 성공적인 **dev** 릴리스는 대상 IoT Edge 디바이스에 IoT Edge 배포를 만듭니다.

    ![dev로 릴리스](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. **dev** 스테이지를 클릭하여 릴리스 로그를 봅니다.

    ![릴리스 로그](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)

7. 파이프라인이 실패하면 먼저 로그를 봅니다. 파이프라인 실행 요약으로 이동하고 작업을 선택하여 로그를 볼 수 있습니다. 특정 작업이 실패하는 경우 해당 작업에 대한 로그를 확인합니다. 로그를 구성하고 사용하는 방법에 대한 자세한 내용은 [로그를 검토하여 파이프라인 문제 진단](/azure/devops/pipelines/troubleshooting/review-logs)을 참조하세요.
