---
title: Azure 산업용 IoT 플랫폼 배포
description: 이 자습서에서는 IIoT 플랫폼을 배포하는 방법을 알아봅니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787409"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>자습서: Azure 산업용 IoT 플랫폼 배포

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * IIoT 플랫폼의 기본 구성 요소에 대한 정보
> * 여러 설치 유형 정보
> * 산업용 IoT 플랫폼을 배포하는 방법

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독을 만들어야 합니다.
- [Git](https://git-scm.com/downloads)을 다운로드합니다.
- 인증에 사용되는 AAD(Azure Active Directory) 앱 등록에는 테넌트 전체 관리자 동의를 제공하기 위해 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한이 필요합니다(추가 옵션은 아래 참조).
- 배포에 지원되는 운영 체제는 Windows, Linu 및 Mac입니다.
- IoT Edge는 Windows 10 IoT Enterprise LTSC 및 Ubuntu Linux 16.08/18.04 LTS Linux를 지원합니다.

## <a name="main-components"></a>기본 구성 요소

- 최소 종속성: IoT Hub, Cosmos DB, Service Bus, Event Hub, Key Vault, Storage
- 표준 종속성: 최소 + SignalR Service, AAD 앱 등록, Device Provisioning Service, Time Series Insights, 통합 문서, Log Analytics, Application Insights
- 마이크로서비스: App Service 요금제, App Service
- UI(웹앱): App Service 요금제(마이크로서비스와 공유), App Service
- 시뮬레이션: 가상 머신, 가상 네트워크, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>설치 유형

- 최소: 최소 종속성
- 로컬: 최소 및 표준 종속성
- 서비스: 로컬 및 마이크로서비스
- 시뮬레이션: 최소 종속성 및 시뮬레이션 구성 요소
- 앱: 서비스 및 UI
- 모두(기본값): 앱 및 시뮬레이션

## <a name="deployment"></a>배포

1. IIoT 플랫폼 배포를 시작하려면 명령 프롬프트 또는 터미널에서 리포지토리를 복제합니다.

    git clone https://github.com/Azure/Industrial-IoT  cd Industrial-IoT

2. 단계별 배포를 시작합니다. 그러면 스크립트에서 필요한 정보(예: Azure 계정, 구독, 대상 리소스 및 그룹/애플리케이션 이름)를 수집합니다.

Windows에서:
    ```
    .\deploy
    ```

Linux 또는 Mac에서:
    ```
    ./deploy.sh
    ```

3. 마이크로서비스와 UI는 인증이 필요한 웹 애플리케이션이며, 이를 위해 AAD에 세 개의 앱 등록이 필요합니다. 필요한 권한이 없는 경우 다음 두 가지 방법을 사용할 수 있습니다.

- AAD 관리자에게 애플리케이션에 대한 테넌트 전체 관리자 동의를 요청합니다.
- AAD 관리자가 AAD 애플리케이션을 만들 수 있습니다. deploy/scripts 폴더에는 배포와 별도로 AAD 등록을 수행하는 aad- register.ps1 스크립트가 포함되어 있습니다. 스크립트 출력은 배포의 일부로 사용할 관련 정보가 포함된 파일이며, - aadConfig 인수를 사용하여 동일한 폴더의 deploy.ps1 스크립트에 전달되어야 합니다.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

준비, 롤백, 크기 조정 및 복원력이 필요한 프로덕션 배포의 경우 플랫폼을 [AKS(Azure Kubernetes Service)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)에 배포할 수 있습니다.

참조:
- [Azure 산업용 IoT 플랫폼 배포](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [올인원 방식으로 배포하는 방법](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [플랫폼을 AKS에 배포하는 방법](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>다음 단계
이제 IIoT 플랫폼을 배포했으므로 구성 요소의 구성을 사용자 지정하는 방법을 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [구성 요소의 구성 사용자 지정](tutorial-configure-industrial-iot-components.md)
