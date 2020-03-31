---
title: 문제 해결 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드에서 문제 해결.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/24/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 95181d0eb23d5956b2c6af52c77f85714b107345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100150"
---
# <a name="common-issues"></a>일반적인 문제

사용자 환경에서 IoT Edge에서 Azure 이벤트 그리드를 사용하는 데 문제가 발생하는 경우 이 문서를 문제 해결 및 해결을 위한 지침으로 사용합니다.

## <a name="view-event-grid-module-logs"></a>이벤트 그리드 모듈 로그 보기

문제를 해결하려면 이벤트 그리드 모듈 로그에 액세스해야 할 수 있습니다. 이렇게 하려면 모듈이 배포된 VM에서 다음 명령을 실행합니다.

윈도우에서,

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

리눅스에서,

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>HTTPS 요청을 할 수 없음

* 먼저 이벤트 그리드 모듈에 **인바운드:serverAuth:tlsPolicy가** **엄격하거나** **활성화되어**있는지 확인합니다.

* 모듈 간 통신의 경우 포트 **4438을** 호출하고 모듈 이름이 배포된 항목과 일치하는지 확인합니다. 

  예를 들어 이벤트 그리드 모듈이 이름 **eventgridmodule과** 함께 배포된 **https://eventgridmodule:4438**경우 URL이 이어야합니다. 대/소문자 와 포트 번호가 올바른지 확인합니다.
    
* IoT가 아닌 모듈의 경우 예를 들어 배포 하는 동안 이벤트 그리드 포트가 호스트 컴퓨터에 매핑되어 있는지 확인합니다.

    ```json
    "HostConfig": {
                "PortBindings": {
                  "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                  ]
                }
     }
    ```

## <a name="unable-to-make-http-requests"></a>HTTP 요청을 할 수 없음

* 먼저 이벤트 그리드 모듈에 **인바운드:serverAuth:tlsPolicy가** **활성화** 또는 **비활성화되도록**설정되어 있는지 확인합니다.

* 모듈 간 통신의 경우 포트 **5888을** 호출하고 모듈 이름이 배포된 항목과 일치하는지 확인합니다. 

  예를 들어 이벤트 그리드 모듈이 이름 **eventgridmodule과** 함께 배포된 **http://eventgridmodule:5888**경우 URL이 이어야합니다. 대/소문자 와 포트 번호가 올바른지 확인합니다.
    
* IoT가 아닌 모듈의 경우 예를 들어 배포 하는 동안 이벤트 그리드 포트가 호스트 컴퓨터에 매핑되어 있는지 확인합니다.

    ```json
    "HostConfig": {
                "PortBindings": {
                  "5888/tcp": [
                    {
                        "HostPort": "5888"
                    }
                  ]
                }
    }
    ```

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>신뢰할 수 없는 기관에서 인증서 체인을 발급했습니다.

기본적으로 Event Grid 모듈은 IoT Edge 보안 데몬에서 발급한 인증서를 사용하여 클라이언트를 인증하도록 구성됩니다. 클라이언트가 이 체인에 루팅된 인증서를 제시하고 있는지 확인합니다.

**IoTSecurity** 클래스의 [https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge) 경우 IoT Edge Security 데몬에서 인증서를 검색하고 이를 사용하여 나가는 호출을 구성하는 방법을 보여 줄 수 있습니다.

비프로덕션 환경인 경우 클라이언트 인증을 해제할 수 있습니다. 이 작업을 수행하는 방법에 대한 자세한 내용은 [보안 및 인증을](security-authentication.md) 참조하십시오.

## <a name="debug-events-not-received-by-subscriber"></a>구독자가 수신하지 않은 이벤트 디버그

일반적인 이유는 다음과 같습니다.

* 이벤트가 성공적으로 게시되지 않았습니다. 이벤트 그리드 모듈에 이벤트를 게시할 때 200(OK)의 HTTP 상태 코드가 수신되어야 합니다.

* 이벤트 구독을 확인하여 다음을 확인합니다.
    * 끝점 URL이 유효합니다.
    * 구독의 필터로 인해 이벤트가 "삭제"되지 않습니다.

* 구독자 모듈이 실행 중인지 확인

* 이벤트 그리드 모듈이 배포된 VM에 로그온하고 로그를 봅니다.

* **브로커:logDeliverySuccess=true를** 설정하고 이벤트 그리드 모듈을 다시 배포하고 요청을 다시 시도하여 배달 당 로깅당 켜십시오. 배달당 로깅을 켜면 처리량과 대기 시간에 영향을 줄 수 있으므로 디버깅이 완료되면 **이를 브로커:logDeliverySuccess=false** 및 다시 배포 이벤트 그리드 모듈로 되돌리는 것이 좋습니다.

* **메트릭:reportertype=콘솔을** 설정하고 이벤트 그리드 모듈을 다시 배포하여 메트릭을 켭니다. 그 후 모든 작업을 하면 메트릭이 Event Grid 모듈의 콘솔에 로그온되어 추가 디버깅에 사용할 수 있습니다. 디버깅에 대해서만 메트릭을 켜고 완료되면 **메트릭:reportertype=없음을** 설정하고 이벤트 그리드 모듈을 다시 배포하여 메트릭을 해제하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

에서 [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)IoT Edge에서 이벤트 그리드를 사용하여 모든 문제, 제안 사항을 보고합니다.