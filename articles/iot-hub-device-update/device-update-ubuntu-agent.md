---
title: Ubuntu Server 18.04 x64 패키지 에이전트를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트 자습서 | Microsoft Docs
description: Ubuntu Server 18.04 x64 패키지 에이전트를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트를 시작합니다.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659054"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Ubuntu Server 18.04 x64 패키지 에이전트를 사용하는 Azure IoT Hub에 대한 디바이스 업데이트 자습서

IoT Hub에 대한 디바이스 업데이트는 이미지 기반 및 패키지 기반의 두 가지 업데이트 형식을 지원합니다.

패키지 기반 업데이트는 디바이스의 특정 구성 요소 또는 애플리케이션만 변경 대상으로 하는 업데이트입니다. 그러면 대역폭 사용량이 줄어들며 업데이트를 다운로드하고 설치하는 시간을 줄일 수 있습니다. 패키지 업데이트는 일반적으로 업데이트를 적용할 때 디바이스의 가동 중지 시간을 최소화하고 이미지를 만드는 오버헤드를 방지할 수 있습니다.

이 자습서에서는 IoT Hub에 대한 디바이스 업데이트를 통해 엔드투엔드 패키지 기반 업데이트를 완료하는 단계를 안내합니다. 이 자습서에서는 Ubuntu Server 18.04 x64용 샘플 패키지 에이전트를 사용합니다. 다른 OS 플랫폼 구성을 사용하려는 경우에도 이 자습서는 IoT Hub에 대한 디바이스 업데이트의 도구 및 개념에 대해 알아보는 데 유용합니다. 엔드투엔드 업데이트 프로세스에 대한 이 소개를 완료하고 원하는 업데이트 형식 및 OS 플랫폼을 선택하여 세부 정보를 살펴봅니다. IoT Hub에 대한 디바이스 업데이트를 사용하여 Azure IoT 또는 Azure IoT Edge 디바이스를 업데이트할 수 있습니다(이 자습서 참조). 

이 자습서에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 디바이스 업데이트 패키지 리포지토리 구성
> * 디바이스 업데이트 에이전트 및 해당 종속성 다운로드 및 설치
> * IoT 디바이스에 태그 추가
> * 업데이트 가져오기
> * 디바이스 그룹 만들기
> * 패키지 업데이트 배포
> * 업데이트 배포 모니터링

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소
* IoT Hub에 대한 액세스. S1(표준) 계층 이상을 사용하는 것이 좋습니다.
* IoT Hub에 연결된, Ubuntu Server 18.04 x64를 실행하는 Azure IoT 또는 Azure IoT Edge 디바이스
   * Azure IoT Edge 디바이스를 사용하는 경우에는 Edge 런타임 v1.2.0 이상인지 확인합니다. 
* Azure IoT Edge 디바이스를 사용하지 않는 경우 [IoT 디바이스에 최신 `aziot-identity-service` 패키지(미리 보기)를 설치합니다](https://github.com/Azure/iot-identity-service/actions/runs/575919358). 
* [위와 동일한 IoT Hub에 연결된 디바이스 업데이트 계정 및 인스턴스](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>디바이스 업데이트 패키지 리포지토리 구성

1. 디바이스 운영 체제와 일치하는 리포지토리 구성을 설치합니다. 이 자습서에서는 Ubuntu Server 18.04입니다. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. 생성된 목록을 sources.list.d 디렉터리에 복사합니다.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Microsoft GPG 공개 키를 설치합니다.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>디바이스 업데이트 .deb 에이전트 패키지 설치

1. 디바이스에서 패키지 목록을 업데이트합니다.

   ```shell
      sudo apt-get update
   ```

2. deviceupdate-agent 패키지 및 해당 종속성을 설치합니다.

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Azure IoT Hub에 대한 디바이스 업데이트 소프트웨어 패키지에는 다음 사용 조건이 적용됩니다.
   * [IoT Hub에 대한 디바이스 업데이트 라이선스](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [배달 최적화 클라이언트 라이선스](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치하고 사용하면 이러한 사용 조건에 동의하는 것입니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Azure IoT ID 서비스(미리 보기)를 사용하여 디바이스 업데이트 에이전트 구성

필요한 패키지가 설치되면 해당 클라우드 ID 및 인증 정보를 사용하여 디바이스를 프로비저닝해야 합니다.

1. 구성 파일을 엽니다.

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. 파일의 프로비저닝 구성 섹션을 찾습니다. "연결 문자열을 사용하여 수동 프로비저닝" 섹션의 주석 처리를 제거합니다. connection_string의 값을 IoT(Edge) 디바이스의 연결 문자열로 업데이트합니다. 다른 모든 프로비저닝 섹션을 주석으로 처리해야 합니다.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Ctrl+X, Y를 사용하여 파일을 저장한 후 닫습니다.

4. 구성을 적용합니다. 

   IoT Edge 디바이스를 사용하는 경우 다음 명령을 사용합니다. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   `aziot-identity-service` 패키지가 설치된 상태에서 IoT 디바이스를 사용하는 경우 다음 명령을 사용합니다. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. 필요에 따라 다음으로 서비스가 실행 중인지 확인할 수 있습니다.

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    출력은 다음과 같아야 합니다.

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>디바이스에 태그 추가

1. [Azure Portal](https://portal.azure.com)에 로그인하여 IoT Hub로 이동합니다.

2. 왼쪽 탐색 창의 'IoT 디바이스' 또는 'IoT Edge'에서 IoT 디바이스를 찾아 디바이스 쌍으로 이동합니다.

3. 디바이스 쌍에서 기존 디바이스 업데이트 태그 값을 null로 설정하여 삭제합니다.

4. 아래와 같이 새 디바이스 업데이트 태그 값을 추가합니다.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>업데이트 가져오기

1. 다음 [APT 매니페스트 파일](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json)을 다운로드하고 [매니페스트 파일을 가져옵니다](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). 이 APT 매니페스트는 IoT 디바이스에 `libcurl4-doc package`의 사용 가능한 최신 버전을 설치합니다. 

   또는 이 [APT 매니페스트 파일](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json)을 다운로드하고 [매니페스트 파일을 가져올 수 있습니다](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). 그러면 `libcurl4-doc package`의 특정 버전 v7.58.0이 IoT 디바이스에 설치됩니다. 

2. Azure Portal의 IoT Hub 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

3. 업데이트 탭을 선택합니다.

4. "+ 새 업데이트 가져오기"를 선택합니다.

5. "매니페스트 파일 가져오기 선택"에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 이전에 다운로드한 가져오기 매니페스트를 선택합니다. 다음으로 "하나 이상의 업데이트 파일 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 파일 선택기 대화 상자가 표시됩니다. 이전에 다운로드한 APT 매니페스트 업데이트 파일을 선택합니다.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="업데이트 파일 선택이 표시된 스냅샷" lightbox="media/import-update/select-update-files.png":::

6. "스토리지 컨테이너 선택" 아래에서 폴더 아이콘 또는 텍스트 상자를 선택합니다. 그런 다음 적절한 스토리지 계정을 선택합니다.

7. 컨테이너를 이미 만든 경우 다시 사용할 수 있습니다. 그렇지 않으면 "+ 컨테이너"를 선택하여 업데이트에 대한 새 스토리지 컨테이너를 만듭니다.  사용할 컨테이너를 선택하고 "선택"을 클릭합니다.

   :::image type="content" source="media/import-update/container.png" alt-text="컨테이너 선택이 표시된 스크린샷" lightbox="media/import-update/container.png":::

8. "제출"을 선택하여 가져오기 프로세스를 시작합니다.

9. 가져오기 프로세스가 시작되고 화면이 "가져오기 기록" 섹션으로 변경됩니다. 가져오기 프로세스가 완료될 때까지 진행률을 보려면 "새로 고침"을 선택합니다. 업데이트 크기에 따라 몇 분 내에 완료될 수 있지만 시간이 오래 걸릴 수 있습니다.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="업데이트 가져오기 시퀀스가 표시된 스크린샷" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 상태 열에 가져오기가 성공했음을 나타내는 경우 "배포할 준비가 됨" 헤더를 선택합니다. 이제 목록에 가져온 업데이트가 표시됩니다.

가져오기 업데이트에 대해 [자세히 알아보기](import-update.md)

## <a name="create-update-group"></a>업데이트 그룹 만들기

1. 이전에 디바이스 업데이트 인스턴스에 연결한 IoT Hub로 이동합니다.

2. 왼쪽 탐색 모음에서 자동 디바이스 관리 아래에 있는 디바이스 업데이트 옵션을 선택합니다.

3. 페이지 맨 위에서 그룹 탭을 선택합니다. 

4. 추가 단추를 선택하여 새 그룹을 만듭니다.

5. 이전 단계에서 만든 IoT Hub 태그를 목록에서 선택합니다. 업데이트 그룹 만들기를 선택합니다.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="태그 선택이 표시된 스크린샷" lightbox="media/create-update-group/select-tag.PNG":::

태그를 추가하고 업데이트 그룹을 만드는 방법에 대해 [자세히 알아보기](create-update-group.md)


## <a name="deploy-update"></a>업데이트 배포

1. 그룹이 만들어지면 보류 중인 업데이트 아래에 업데이트 링크와 함께 디바이스 그룹에 사용할 수 있는 새 업데이트가 표시되어야 합니다. 한 번 새로 고쳐야 할 수 있습니다. 

2. 사용 가능한 업데이트를 클릭합니다.

3. 올바른 그룹이 대상 그룹으로 선택되었는지 확인합니다. 배포를 예약한 후 업데이트 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="업데이트 선택" lightbox="media/deploy-update/select-update.png":::

4. 규정 준수 차트를 봅니다. 이제 업데이트가 진행 중인 것으로 표시됩니다. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="업데이트 진행 중" lightbox="media/deploy-update/update-in-progress.png":::

5. 디바이스를 성공적으로 업데이트한 후에는 규정 준수 차트와 배포 세부 정보 업데이트를 확인하여 동일하게 반영해야 합니다. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="업데이트 성공" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>업데이트 배포 모니터링

1. 페이지 맨 위에서 배포 탭을 선택합니다.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="배포 탭" lightbox="media/deploy-update/deployments-tab.png":::

2. 배포 세부 정보를 보려면 만든 배포를 선택합니다.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="배포 세부 정보" lightbox="media/deploy-update/deployment-details.png":::

3. 최신 상태 세부 정보를 보려면 새로 고침을 선택합니다. 상태가 성공으로 변경될 때까지 이 프로세스를 계속합니다.

이제 Ubuntu Server 18.04 x64 디바이스에서 IoT Hub에 대한 디바이스 업데이트를 사용하여 엔드투엔드 패키지 업데이트를 성공적으로 완료했습니다. 

## <a name="bonus-steps"></a>보너스 단계

1. 다음 [APT 매니페스트 파일](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json)을 다운로드하고 [매니페스트 파일을 가져옵니다](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). 이 APT 매니페스트는 IoT 디바이스에서 설치된 `libcurl4-doc package`를 제거합니다. 

2. "업데이트 가져오기" 및 "업데이트 배포" 섹션을 반복합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 디바이스 업데이트 계정, 인스턴스, IoT Hub 및 IoT 디바이스를 정리합니다. 각 개별 리소스로 이동하고 "삭제"를 선택하여 이 작업을 수행할 수 있습니다. 디바이스 업데이트 인스턴스를 정리해야 디바이스 업데이트 계정을 정리할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Raspberry Pi 3 B+에서 이미지 업데이트 자습서](device-update-raspberry-pi.md)

