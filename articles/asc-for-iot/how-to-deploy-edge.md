---
title: IoT 에지 모듈을 위한 Azure 보안 센터 배포 | 마이크로 소프트 문서
description: IoT Edge에 IoT 보안 에이전트용 Azure 보안 센터를 배포하는 방법에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: b2af392dc4dc848a099b8297bb58e7d4a7104fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964042"
---
# <a name="deploy-a-security-module-on-your-iot-edge-device"></a>IoT Edge 장치에 보안 모듈 배포


**IoT용 Azure 보안 센터** 모듈은 IoT Edge 장치에 대한 포괄적인 보안 솔루션을 제공합니다.
보안 모듈은 운영 체제 및 컨테이너 시스템의 원시 보안 데이터를 수집, 집계 및 분석하여 실행 가능한 보안 권장 사항 및 경고로 변환합니다.
자세한 내용은 [IoT Edge의 보안 모듈을](security-edge-architecture.md)참조하십시오.

이 문서에서는 IoT Edge 장치에 보안 모듈을 배포하는 방법을 알아봅니다.

## <a name="deploy-security-module"></a>보안 모듈 배포

다음 단계를 사용하여 IoT Edge용 IoT 보안 모듈을 위한 Azure 보안 센터를 배포합니다.

### <a name="prerequisites"></a>사전 요구 사항

1. IoT Hub에서 장치가 [IoT Edge 장치로 등록되어](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)있는지 확인합니다.

1. IoT 에지 용 Azure 보안 센터 모듈에는 [감사D 프레임워크가](https://linux.die.net/man/8/auditd) IoT Edge 장치에 설치되어 있어야 합니다.

    - IoT Edge 장치에서 다음 명령을 실행하여 프레임워크를 설치합니다.
   
    `sudo apt-get install auditd audispd-plugins`

    - 다음 명령을 실행하여 AuditD가 활성 상태인지 확인합니다. 
   
    `sudo systemctl status auditd`<br>
    - 예상 응답은 다음과 입니다.`active (running)` 
        

### <a name="deployment-using-azure-portal"></a>Azure 포털을 사용한 배포

1. Azure 포털에서 **마켓플레이스를**엽니다.

1. **사물 인터넷을**선택한 다음 **IoT용 Azure 보안 센터를** 검색하고 선택합니다.

   ![IoT용 Azure 보안 센터 선택](media/howto/edge-onboarding-8.png)

1. **만들기를** 클릭하여 배포를 구성합니다. 

1. IoT Hub의 Azure **구독을** 선택한 다음 **IoT Hub를**선택합니다.<br>단일 장치를 대상으로 **하는 장치에 배포를** 선택하거나 **확장에서 여러** 장치를 대상으로 배포를 선택하고 **만들기를**클릭합니다. 대규모 배포에 대한 자세한 내용은 [를](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor)참조하십시오. 

    >[!Note] 
    >**규모에서 배포를**선택한 경우 다음 지침의 모듈 추가 탭을 계속하기 전에 장치 이름과 세부 정보를 **추가합니다.**     

각 단계를 완료하여 IoT용 Azure 보안 센터에 대한 IoT 에지 배포를 완료합니다. 

#### <a name="step-1-modules"></a>1단계: 모듈

1. **Azure보안센터리오트** 모듈을 선택합니다.
1. 모듈 **설정** 탭에서 **이름을** **azureiotsecurity로**변경합니다.
1. **Enviroment 변수** 탭에서 필요한 경우 변수를 추가합니다(예: 디버그 수준).
1. 컨테이너 **옵션 만들기** 탭에서 다음 구성을 추가합니다.

    ``` json
    {
        "NetworkingConfig": {
            "EndpointsConfig": {
                "host": {}
            }
        },
        "HostConfig": {
            "Privileged": true,
            "NetworkMode": "host",
            "PidMode": "host",
            "Binds": [
                "/:/host"
            ]
        }
    }    
    ```
    
1. 모듈 **트윈 설정** 탭에서 다음 구성을 추가합니다.
      
    ``` json
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration":{}
    ```

1. **업데이트를**선택합니다.

#### <a name="step-2-runtime-settings"></a>2단계: 런타임 설정

1. **런타임 설정을**선택합니다.
1. **가장자리 허브에서** **이미지를** **mcr.microsoft.com/azureiotedge-hub:1.0.8.3.**
1. **만들기 옵션이** 다음 구성으로 설정되어 있는지 확인합니다. 
         
    ``` json
    { 
       "HostConfig":{ 
          "PortBindings":{ 
             "8883/tcp":[ 
                { 
                   "HostPort":"8883"
                }
             ],
             "443/tcp":[ 
                { 
                   "HostPort":"443"
                }
             ],
             "5671/tcp":[ 
                { 
                   "HostPort":"5671"
                }
             ]
          }
       }
    }
    ```
    
1. **저장**을 선택합니다.
   
1. **다음**을 선택합니다.

#### <a name="step-3-specify-routes"></a>3단계: 경로 지정 

1. 경로 **지정** 탭에서 **azureiotsecurity** 모듈의 메시지를 다음 예제에 따라 **$upstream** 전달하는 경로(명시적 또는 암시적)가 있는지 확인합니다. 경로가 제자리에 있는 경우에만 **다음을**선택합니다.

   예: 경로:

    ~~~Default implicit route
    "route": "FROM /messages/* INTO $upstream" 
    ~~~

    ~~~Explicit route
    "ASCForIoTRoute": "FROM /messages/modules/azureiotsecurity/* INTO $upstream"
    ~~~

1. **다음**을 선택합니다.

#### <a name="step-4-review-deployment"></a>4단계: 배포 검토

- 배포 **검토** 탭에서 배포 정보를 검토한 다음 **만들기를** 선택하여 배포를 완료합니다.

## <a name="diagnostic-steps"></a>진단 단계

문제가 발생하면 컨테이너 로그가 IoT Edge 보안 모듈 장치의 상태에 대해 알아보는 가장 좋은 방법입니다. 이 섹션의 명령 및 도구를 사용하여 정보를 수집합니다.

### <a name="verify-the-required-containers-are-installed-and-functioning-as-expected"></a>필요한 컨테이너가 설치되고 예상대로 작동하는지 확인

1. IoT Edge 장치에서 다음 명령을 실행합니다.
    
    `sudo docker ps`
   
1. 다음 컨테이너가 실행 되고 있는지 확인합니다.
   
   | 이름 | IMAGE |
   | --- | --- |
   | azureiot보안 | mcr.microsoft.com/ascforiot/azureiotsecurity:1.0.2 |
   | 에지 허브 | mcr.microsoft.com/azureiotedge-hub:1.0.8.3 |
   | 가장자리 에이전트 | mcr.microsoft.com/azureiotedge-agent:1.0.1 |
   
   필요한 최소 컨테이너가 없는 경우 IoT Edge 배포 매니페스트가 권장 설정과 일치하는지 확인합니다. 자세한 내용은 [IoT 에지 배포 모듈을](#deployment-using-azure-portal)참조하십시오.

### <a name="inspect-the-module-logs-for-errors"></a>모듈 로그에 오류가 대한 검사
   
1. IoT Edge 장치에서 다음 명령을 실행합니다.

   `sudo docker logs azureiotsecurity`
   
1. 자세한 로그를 보려면 **azureiotsecurity** 모듈 배포에 다음 환경 변수를 `logLevel=Debug`추가합니다.

## <a name="next-steps"></a>다음 단계

구성 옵션에 대해 자세히 알아보려면 모듈 구성에 대한 방법 가이드를 계속 사용하십시오. 
> [!div class="nextstepaction"]
> [모듈 구성 방법 가이드](./how-to-agent-configuration.md)
