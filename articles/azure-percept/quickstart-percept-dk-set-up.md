---
title: Azure Percept DK 설정
description: devkit를 Azure에 연결하고 첫 번째 AI 모델 배포
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 49bf89d38edef6a9186cbdb5bb89a763339385b4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175824"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Azure Percept DK를 설정하고 첫 번째 AI 모델 배포

Azure Percept DK 설정 환경을 사용하여 디바이스를 Azure에 연결하고 첫 번째 AI 모델을 배포하여 Azure Percept DK 및 Azure Percept Studio를 시작합니다. Azure 계정이 Azure Percept Studio와 호환되는 것을 확인한 후에는 Edge AI 개념 증명을 만들도록 Azure Percept DK를 구성하는 설정 환경을 완료합니다.

이 빠른 시작을 진행하는 동안 문제가 발생하는 경우 [문제 해결](./troubleshoot-dev-kit.md) 가이드에서 가능한 해결 방법을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Percept DK.
- wi-fi 기능과 웹 브라우저를 지원하는 Windows, Linux 또는 OS X 기반 호스트 컴퓨터
- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure 계정에는 구독의 "소유자" 또는 "기여자" 역할이 있어야 합니다. [Azure 역할 정의](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles)에 대해 자세히 알아봅니다.

### <a name="prerequisite-check"></a>필수 구성 요소 확인

Azure 계정이 구독의 "소유자" 또는 "기여자"인지 확인하려면 다음 단계를 수행합니다.

1. Azure Portal로 이동한 다음, Azure Percept Studio에서 사용하려는 것과 동일한 Azure 계정으로 로그인합니다. 

    > [!NOTE]
    > Azure 계정이 여러 개 있는 경우 브라우저가 다른 계정의 자격 증명을 캐시할 수 있습니다. 올바른 계정으로 로그인했는지 확인하는 방법에 대한 자세한 내용은 문제 해결 가이드를 참조하세요.

1. 화면의 왼쪽 위 모서리에서 주 메뉴를 확장하고 "구독"을 클릭하거나 홈 페이지의 아이콘 메뉴에서 "구독"을 선택합니다. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. 목록에서 구독을 선택합니다. 목록에 구독이 표시되지 않으면 올바른 Azure 계정으로 로그인했는지 확인합니다. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
새 구독을 만들려면 [다음 단계](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)를 수행합니다.

1. 구독 메뉴에서 “액세스 제어(IAM)”를 선택합니다.
1. "내 액세스 보기" 단추 클릭
1. 역할 확인
    - "읽기 권한자" 역할이 표시되거나 역할을 볼 수 있는 권한이 없다는 메시지가 표시되는 경우 조직 내에서 계정 역할을 승격하기 위해 필요한 프로세스를 수행해야 합니다.
    - 역할이 "소유자" 또는 "기여자"로 표시되는 경우 계정이 Azure Percept Studio와 함께 작동합니다. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK 설정 환경 시작

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. 호스트 컴퓨터를 devkit의 wi-fi 액세스 지점에 직접 연결합니다. 다른 wi-fi 네트워크에 연결할 때와 같은 방법으로 하면 됩니다.
    - **네트워크 이름**: scz-xxxx(여기서 "xxxx"는 devkit의 MAC 네트워크 주소 마지막 네 자리)
    - **암호**: devkit와 함께 제공된 시작 카드에서 찾을 수 있습니다.

    > [!WARNING]
    > Azure Percept DK wi-fi 액세스 지점에 연결된 동안 호스트 컴퓨터의 인터넷 연결이 일시적으로 끊깁니다. 활성 화상 회의 통화, 웹 스트리밍 또는 기타 네트워크 기반 환경은 Azure Percept DK 설정 환경의 3단계가 완료될 때까지 중단됩니다.

1. devkit의 wi-fi 액세스 지점에 연결되면 호스트 디바이스는 자동으로 새 브라우저 창에서 Azure Percept DK 설정 환경을 시작합니다. 새 브라우저 창이 자동으로 열리지 않는 경우 브라우저 창을 열고 [http://10.1.1.1](http://10.1.1.1)로 이동하여 수동으로 시작할 수 있습니다. 

1. Azure Percept 설정 환경을 시작했으므로, 설정 환경을 진행할 준비가 되었습니다. 

    > [!NOTE]
    > Azure Percept DK 설정 환경 웹 서비스는 30분 동안 사용하지 않거나 설정 환경이 완료될 때 종료됩니다. 서비스가 종료될 경우 devkit wi-fi 액세스 지점의 연결 문제를 방지하기 위해 devkit를 다시 시작하는 것이 좋습니다.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Azure Percept DK 설정 환경 완료

1. 시작 - [시작] 화면에서 **다음** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="[시작] 페이지":::

1. wi-fi에 연결 - [네트워크 연결] 페이지에서 **새 WiFi 네트워크에 연결** 을 클릭하여 devkit를 wi-fi 네트워크에 연결합니다.

    이전에 이 devkit를 wi-fi 네트워크에 연결했거나 wi-fi 네트워크를 통해 Azure Percept DK 설정 환경에 이미 연결되어 있는 경우 **건너뛰기** 링크를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="wi-fi에 연결":::

1. 사용 가능한 연결 옵션 중에서 wi-fi 네트워크를 선택하고 연결합니다. (로컬 wi-fi 암호가 필요)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="wi-fi 네트워크 선택"::: 

1. IP 주소 복사 - 선택한 네트워크에 devkit가 성공적으로 연결된 후에는 페이지에 표시되는 **IPv4 주소** 를 적어 둡니다. **이 빠른 시작 가이드의 뒷부분에서 이 IP 주소가 필요합니다**. 

    > [!NOTE]
    > 각 디바이스가 부팅될 때 IP 주소가 변경될 수 있습니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="IP 주소 복사":::

1. 사용권 계약을 검토하고 동의 - 사용권 계약을 모두 읽고, **사용권 계약을 읽었으며 이에 동의합니다** 를 선택하고(계약의 맨 아래까지 스크롤해야 함), **다음** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="EULA 동의":::

1. SSH 로그인 계정 만들기 - devkit에 원격으로 액세스할 수 있도록 SSH를 설정합니다. SSH 사용자 이름 및 암호를 만듭니다. 

    > [!NOTE]
    > SSH(Secure Shell)는 호스트 디바이스와 devkit 간의 보안 통신에 사용되는 네트워크 프로토콜입니다. SSH에 대한 자세한 내용은 [이 문서](https://en.wikipedia.org/wiki/SSH_(Secure_Shell))를 참조하세요.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="SSH 계정 만들기"::: 

1. devkit 연결 프로세스 시작 - 다음 화면에서 **새 디바이스에 연결** 을 클릭하여 devkit를 Azure IoT Hub에 연결하는 프로세스를 시작합니다. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Azure에 연결"::: 

1. 디바이스 코드 복사 - **복사** 링크를 클릭하여 디바이스 코드를 복사합니다. 그런 다음, **Azure에 로그인** 을 클릭합니다. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="디바이스 코드 복사"::: 

1. 디바이스 코드 붙여넣기 - 복사한 디바이스 코드를 요청하는 창이 포함된 새 브라우저 탭이 열립니다. 이 창에 코드를 붙여넣고 **다음** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="디바이스 코드 입력"::: 

1. Azure에 로그인 - 다음 창에서는 빠른 시작을 시작할 때 확인한 Azure 계정으로 로그인해야 합니다. 해당 로그인 자격 증명을 입력하고 **다음** 을 클릭합니다. 

    > [!NOTE]
    > 브라우저에서 다른 자격 증명을 자동으로 캐시할 수 있습니다. 올바른 계정으로 로그인하고 있는지 다시 확인합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Azure에 로그인":::
 
1. 이 단계에서는 설정 환경 브라우저 탭을 닫지 말 것 - 로그인하면 로그인되었음을 알리는 화면이 표시됩니다. 창을 닫아도 된다고 나오지만, **어떤 창도 닫지 않는 것이 좋습니다**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="로그인 성공"::: 

1. 설정 환경을 호스트하는 브라우저 탭으로 돌아갑니다.
1. IoT Hub 옵션 선택
    - 이미 IoT Hub가 있고 이 페이지에 나열되어 있는 경우 해당 허브를 선택하고 **17단계로 이동** 합니다.
    - IoT Hub가 없거나 새로 만들려는 경우 목록의 맨 아래로 이동하여 **새 Azure IoT Hub 만들기** 를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="IoT Hub 선택"::: 

1. 새 IoT Hub 만들기 – 이 페이지의 모든 필드를 채웁니다. 
    - Azure Percept Studio에서 사용할 Azure 구독을 선택합니다.
    - 기존 리소스 그룹을 선택합니다. 없는 경우 "새로 만들기"를 클릭하고 프롬프트의 지시를 따릅니다. 
    - Azure 지역을 선택합니다. 
    - 새 IoT Hub 이름을 지정합니다.
    - 가격 책정 계층을 선택합니다(일반적으로 구독과 일치).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="새 IoT Hub 만들기"::: 

1. IoT Hub가 배포될 때까지 대기 – 몇 분 정도 걸릴 수 있습니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="IoT Hub 배포"::: 

1. devkit 등록 - 배포가 완료되면 **등록** 단추를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub가 성공적으로 배포됨"::: 

1. devkit 이름 지정 - devkit의 디바이스 이름을 입력하고 **다음** 을 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="디바이스 이름 지정"::: 

1. 기본 AI 모델이 다운로드될 때까지 대기 - 몇 분 정도 걸립니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="설정 완료"::: 

1. 비전 AI의 실제 작동 모습 확인 - devkit가 Azure IoT Hub에 성공적으로 연결되었으며 기본 비전 AI 개체 감지 모델을 받았습니다. 이제 Azure Percept 비전 카메라는 클라우드에 연결하지 않고도 개체 감지 추론을 만들 수 있습니다. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="비디오 출력 미리 보기 클릭"::: 
       
    > [!NOTE]
    > 지금은 호스트 컴퓨터에 대한 온보딩 프로세스 및 디바이스 Wifi 액세스 연결이 종료되지만, devkit는 인터넷에 연결된 상태로 유지됩니다.   devkit를 다시 부팅하여 온보딩 환경을 다시 시작할 수 있습니다. 그러면 온보딩 프로세스로 다시 돌아가서 디바이스를 동일한 또는 다른 Azure 구독과 연결된 다른 IOT 허브에 다시 연결할 수 있습니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="설정 환경 연결 끊김 경고"::: 

1. Azure Portal 계속 진행 - 설정 환경 창으로 돌아가서 **Azure Portal 계속** 단추를 클릭하여 Azure Percept Studio에서 사용자 지정 AI 모델 만들기를 시작합니다.

    > [!NOTE]
    > 호스트 컴퓨터가 wifi 설정의 devkit 액세스 지점에 더 이상 연결되어 있지 않고 이제 로컬 wifi에 다시 연결되었는지 확인합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Azure Percept Studio로 이동"::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Azure Percept Studio에서 디바이스를 살펴보고 미리 빌드된 샘플 앱을 배포합니다.

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 개요 페이지에서 디바이스 목록을 봅니다. Azure Percept 개요 페이지는 고급 AI Edge 모델 및 솔루션 개발을 이제 막 시작하는 분들과 고급 개발자가 모두 사용 가능한 여러 워크플로에 액세스할 수 있는 시작 지점입니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="디바이스 목록 보기":::
    
1. 방금 만든 디바이스가 연결되어 있는지 확인하고 해당 디바이스를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="디바이스를 선택합니다.":::

1. 디바이스 스트림을 살펴보고 devkit 카메라에 무엇이 보이는지 확인합니다. 기본 개체 감지 모델은 즉시 배포되며 다양한 일반 개체를 감지합니다.

    > [!NOTE]
    > 새 디바이스에서 이 작업을 처음으로 수행할 때 오른쪽 위 모서리에 새 모듈이 배포된다는 알림이 표시됩니다.  완료되면 카메라 비디오 스트림으로 창을 시작할 수 있습니다. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="비디오 스트림 보기":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="개체 감지 보기":::

1. 미리 작성된 샘플 AI 모델을 살펴봅니다.   Azure Precept Studio에는 클릭 한 번으로 배포할 수 있는 여러 가지 미리 작성된 샘플이 있습니다.   "샘플 모델 배포"를 선택하여 이러한 샘플을 살펴보고 배포합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="미리 작성된 모델 살펴보기":::
    
1. 연결된 디바이스에 미리 작성된 새 샘플을 배포합니다. 라이브러리에서 샘플을 선택하고 "디바이스에 배포"를 클릭합니다.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="개체 감지의 실제 작동 모습 살펴보기":::

## <a name="next-steps"></a>다음 단계

비슷한 흐름에 따라 [미리 작성된 음성 모델](./tutorial-no-code-speech.md)을 사용해 볼 수 있습니다.