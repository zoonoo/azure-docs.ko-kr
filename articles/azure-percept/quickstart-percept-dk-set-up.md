---
title: Azure Percept DK 설정
description: devkit를 Azure에 연결하고 첫 번째 AI 모델 배포
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 1b844eb66a287e103e453e242bec407b2f3a38e6
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109788412"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Azure Percept DK를 설정하고 첫 번째 AI 모델 배포

Azure Percept DK 설정 환경을 완료하여 개발자 키트를 구성하고 첫 번째 AI 모델을 배포하세요. Azure 계정이 Azure Percept와 호환되는지 확인한 후 다음을 수행합니다.

- Wi-Fi 네트워크에 개발 키트 연결
- 개발 키트에 원격으로 액세스할 수 있도록 SSH 로그인 설정
- Azure Percept에서 사용할 새 IoT Hub 만들기
- IoT Hub 및 Azure 계정에 개발 키트 연결

이 과정에서 문제가 발생하는 경우 [설정 문제 해결 가이드](./how-to-troubleshoot-setup.md)에서 가능한 해결 방법을 참조하세요.

> [!TIP]
> 언제든지 설치 환경으로 돌아가서 새 Wi-Fi 네트워크에 연결, 새 SSH 사용자 만들기, IoT Hub 다시 연결 등의 목적으로 개발 키트를 다시 초기화할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Percept DK(개발 키트)
- Wi-Fi 기능과 웹 브라우저를 지원하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- 활성 구독이 있는 Azure 계정. [체험 계정을 만드세요.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure 계정은 구독 내에서 **소유자** 또는 **기여자** 역할이 있어야 합니다. 아래 단계에 따라 Azure 계정 역할을 확인하세요. Azure 역할 정의에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어 설명서](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)를 참조하세요.

    > [!CAUTION]
    > Azure 계정이 여러 개 있는 경우 브라우저가 다른 계정의 자격 증명을 캐시할 수 있습니다. 혼동을 피하려면 설정 환경을 시작하기 전에 사용하지 않는 브라우저 창을 모두 닫고 [Azure Portal](https://portal.azure.com/)에 로그인하는 것이 좋습니다. 올바른 계정으로 로그인했는지 확인하는 방법에 대한 자세한 내용은 [설정 문제 해결 가이드](./how-to-troubleshoot-setup.md)를 참조하세요.

### <a name="check-your-azure-account-role"></a>Azure 계정 역할 확인

Azure 계정이 구독 내에서 "소유자" 또는 "기여자"인지 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동한 다음, Azure Percept에서 사용하려는 Azure 계정으로 로그인합니다.

1. 노란색 키처럼 생긴 **구독** 아이콘을 클릭합니다.

1. 목록에서 구독을 선택합니다. 구독이 보이지 않으면 올바른 Azure 계정으로 로그인했는지 확인합니다. 새 구독을 만들려면 [다음 단계](../cost-management-billing/manage/create-subscription.md)를 수행합니다.

1. 구독 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
1. **내 액세스 보기** 를 클릭합니다.
1. 다음과 같이 역할을 확인합니다.
    - 역할이 **읽기 권한자** 로 표시되거나 역할을 볼 수 있는 권한이 없다는 메시지가 표시되는 경우 조직 내에서 계정 역할을 승격하기 위해 필요한 프로세스를 수행해야 합니다.
    - 역할이 **소유자** 또는 **기여자** 로 표시되는 경우 해당 계정은 Azure Percept와 함께 작동하며, 설정 환경을 계속 진행할 수 있습니다.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK 설정 환경 시작

1. 호스트 컴퓨터를 개발 키트의 Wi-Fi 액세스 지점에 직접 연결합니다. 다른 Wi-Fi 네트워크에 연결할 때와 마찬가지로 컴퓨터에서 네트워크 및 인터넷 설정을 열고, 다음 네트워크를 클릭하고, 네트워크 암호를 입력하라는 메시지가 표시되면 입력합니다.

    - **네트워크 이름**: 개발 키트의 운영 체제 버전에 따라 Wi-Fi 액세스 지점의 이름은 **scz-xxxx** 또는 **apd-xxxx**(여기서 "xxxx"는 개발 키트 MAC 주소의 마지막 네 자리)입니다.
    - **암호**: 개발 키트와 함께 제공된 시작 카드에서 찾을 수 있습니다.

    > [!WARNING]
    > Azure Percept DK Wi-Fi 액세스 지점에 연결된 동안 호스트 컴퓨터의 인터넷 연결이 일시적으로 끊깁니다. 활성 영상 회의 통화, 웹 스트리밍 또는 기타 네트워크 기반 환경이 중단됩니다.

1. 개발 키트의 Wi-Fi 액세스 지점에 연결되면 호스트 컴퓨터는 주소 표시줄에 **your.new.device/** 가 입력된 새 브라우저 창에서 자동으로 설정 환경을 시작합니다. 탭이 자동으로 열리지 않으면 [http://10.1.1.1](http://10.1.1.1)로 이동하여 설정 환경을 시작합니다. Azure Percept에서 사용하려는 계정과 동일한 Azure 계정 자격 증명으로 브라우저에 로그인해야 합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="[시작] 페이지":::

    > [!CAUTION]
    > 설정 환경 웹 서비스는 30분 동안 사용하지 않으면 종료됩니다. 서비스가 종료될 경우 개발 키트의 Wi-Fi 액세스 지점에서 연결 문제가 발생하지 않도록 개발 키트를 다시 시작하는 것이 좋습니다.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Azure Percept DK 설정 환경 완료

1. **시작** 화면에서 **다음** 을 클릭합니다.

1. **네트워크 연결** 페이지에서 **새 WiFi 네트워크에 연결** 을 클릭합니다.

    개발 키트를 이미 Wi-Fi 네트워크에 연결한 경우 **건너뛰기** 를 클릭합니다.

1. 사용 가능한 네트워크 목록에서 Wi-Fi 네트워크를 선택하고 **연결** 을 클릭합니다. 암호를 입력하라는 메시지가 표시되면 암호를 입력합니다.

1. 선택한 네트워크에 개발 키트가 성공적으로 연결되면 이 페이지에는 개발 키트에 할당된 IPv4 주소가 표시됩니다. **이 페이지에 표시되는 IPv4 주소를 기록해 둡니다.** 문제를 해결하고 디바이스를 업데이트하기 위해 SSH를 통해 개발 키트에 연결할 때 이 IP 주소가 필요합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="IP 주소 복사":::

    > [!NOTE]
    > 각 디바이스가 부팅될 때 IP 주소가 변경될 수 있습니다.

1. 사용권 계약을 모두 읽고, **사용권 계약을 읽었으며 이에 동의합니다** 를 선택하고(계약의 맨 아래까지 스크롤해야 함), **다음** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="EULA 동의":::

1. SSH 계정 이름 및 암호를 입력하고, **나중에 사용할 수 있도록 로그인 정보를 기록해 둡니다**.

    > [!NOTE]
    > SSH(Secure Shell)는 호스트 컴퓨터를 통해 개발 키트에 원격으로 연결할 수 있도록 하는 네트워크 프로토콜입니다.

1. 다음 페이지에서 **새 디바이스로 설치** 를 클릭하여 Azure 계정 내에 새 디바이스를 만듭니다.

1. **복사** 를 클릭하여 디바이스 코드를 복사합니다. 그리고 **Azure에 로그인** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="디바이스 코드 복사":::

1. 새 브라우저 탭이 열리고 **코드 입력** 이라는 창이 표시됩니다. 이 창에 코드를 붙여넣고 **다음** 을 클릭합니다. 설정 환경에서 **시작** 탭을 닫지 마세요.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="디바이스 코드 입력":::

1. 개발 키트와 함께 사용할 Azure 계정 자격 증명으로 Azure Percept에 로그인합니다. 모두 마쳤으면 브라우저 탭을 열어 둡니다.

    > [!CAUTION]
    > 브라우저에서 다른 자격 증명을 자동으로 캐시할 수 있습니다. 올바른 계정으로 로그인하고 있는지 다시 확인합니다.

    디바이스에서 Azure Percent에 성공적으로 로그인한 후에는 **시작** 탭으로 돌아가서 설정 환경을 계속합니다.

1. **Azure IoT Hub에 디바이스 할당** 페이지가 **시작** 탭에 표시되면 다음 작업 중 하나를 수행합니다.

    - Azure Percept에서 사용하려는 IoT Hub가 이미 있고 이 페이지에 나열되어 있는 경우에는 해당 허브를 선택하고 15단계로 이동합니다.
    - IoT Hub가 없거나 새로 만들려는 경우 **새 Azure IoT Hub 만들기** 를 클릭합니다.

    > [!IMPORTANT]
    > IoT Hub가 있지만 목록에 나타나지 않는 경우에는 잘못된 자격 증명으로 Azure Percept에 로그인했을 수 있습니다. 도움말은 [설정 문제 해결 가이드](./how-to-troubleshoot-setup.md)를 참조하세요.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="IoT Hub 선택":::

1. 새 IoT Hub를 만들려면 다음 필드를 완료합니다.

    - Azure Percept에서 사용할 Azure 구독을 선택합니다.
    - 기존 리소스 그룹을 선택합니다. 없는 경우 **새로 만들기** 를 클릭하고 프롬프트의 지시를 따릅니다.
    - 실제 위치와 가장 가까운 Azure 지역을 선택합니다.
    - 새 IoT Hub 이름을 지정합니다.
    - S1(표준) 가격 책정 계층을 선택합니다.

    > [!NOTE]
    > 에지 AI 애플리케이션에 더 높은 [메시지 처리량](../iot-hub/iot-hub-scaling.md#message-throughput)이 필요한 경우 언제든지 Azure Portal에서 [더 높은 표준 계층으로 IoT Hub를 업그레이드](../iot-hub/iot-hub-upgrade.md)할 수 있습니다. B 및 F 계층은 Azure Percept를 지원하지 않습니다.

1. IoT Hub 배포는 몇 분 정도 걸릴 수 있습니다. 배포가 완료되면 **등록** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub가 성공적으로 배포됨":::

1. 개발 키트의 디바이스 이름을 입력하고 **다음** 을 클릭합니다.

1. 디바이스 모듈이 다운로드될 때까지 기다립니다. 몇 분 정도 걸립니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="설정 완료":::

1. **디바이스 설정 완료!** 페이지가 표시되면 개발 키트가 IoT Hub에 연결하여 필요한 소프트웨어를 다운로드한 것입니다. 개발 키트는 자동으로 Wi-Fi 액세스 지점의 연결을 해제하며, 그 결과로 다음과 같은 두 가지 알림이 표시됩니다.

    > [!NOTE]
    > 이 설정 프로세스의 일부로 구성된 IoT Edge 컨테이너는 90일 후에 만료될 인증서를 사용합니다. IoT Edge를 다시 시작하여 인증서를 자동으로 재생성할 수 있습니다. 자세한 내용은 [IoT Edge 디바이스에서 인증서 관리](https://docs.microsoft.com/azure/iot-edge/how-to-manage-device-certificates)를 참조하세요.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="설정 환경 연결 끊김 경고":::

1. 2단계에서 개발 키트를 연결한 Wi-Fi 네트워크에 호스트 컴퓨터를 연결합니다.

1. **Azure Portal 계속 진행** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-Azure-portal-continue.png" alt-text="Azure Percept Studio로 이동":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>개발 키트 비디오 스트림 보기 및 샘플 모델 배포

1. [Azure Percept Studio 개요 페이지](https://go.microsoft.com/fwlink/?linkid=2135819)는 고급 에지 AI 솔루션 개발을 이제 막 시작하는 분들과 고급 개발자가 모두 사용 가능한 여러 워크플로에 액세스할 수 있는 시작 지점입니다. 시작하려면 왼쪽 메뉴에서 **디바이스** 를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="디바이스 목록 보기":::

1. 개발 키트가 **연결됨** 으로 표시되는지 확인한 후 개발 키트를 클릭하여 디바이스 페이지를 살펴봅니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="디바이스를 선택합니다.":::

1. **디바이스 스트림 보기** 를 클릭합니다. 디바이스의 비디오 스트림을 처음으로 보는 경우 새 모델이 배포된다는 알림이 오른쪽 위 모서리에 표시됩니다. 몇 분이 걸릴 수 있습니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="비디오 스트림 보기":::

    모델이 배포되면 **스트림 보기** 링크가 포함된 또 다른 알림을 받게 됩니다. 링크를 클릭하여 새 브라우저 창에서 Azure Percept Vision 카메라의 비디오 스트림을 볼 수 있습니다. 개발 키트에는 여러 공통 개체의 개체 감지를 자동으로 수행하는 AI 모델이 미리 로드되어 있습니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="개체 감지 보기":::

1. 또한 Azure Percept Studio에는 다양한 샘플 AI 모델이 포함되어 있습니다. 개발 키트에 샘플 모델을 배포하려면 디바이스 페이지로 돌아가서 **샘플 모델 배포** 를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="미리 작성된 모델 살펴보기":::

1. 라이브러리에서 샘플 모델을 선택하고 **디바이스에 배포** 를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="개체 감지의 실제 작동 모습 살펴보기":::

1. 모델이 성공적으로 배포되면 **스트림 보기** 링크가 포함된 알림이 화면 오른쪽 위에 표시됩니다. 모델이 실제로 추론하는 모습을 살펴보려면 알림의 링크를 클릭하거나 디바이스 페이지로 돌아가서 **디바이스 스트림 보기** 를 클릭합니다. 이전에 개발 키트에서 실행되던 모델은 이제 새 모델로 대체됩니다.

## <a name="video-walkthrough"></a>연습 동영상

위에서 설명한 단계의 시각적 연습은 다음 비디오를 참조하세요(설정 환경은 0:50에 시작).

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [코드 없는 비전 솔루션 만들기](./tutorial-nocode-vision.md)