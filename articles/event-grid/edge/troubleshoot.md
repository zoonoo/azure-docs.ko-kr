---
title: 문제 해결 - Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge의 Event Grid 문제 해결
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 9b8e5b95b0d1853d81de5a4ec603a3a59563da9d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379803"
---
# <a name="common-issues"></a>일반적인 문제

작업 환경에서 IoT Edge의 Azure Event Grid를 사용하는 동안 문제가 발생할 경우 이 문서에서 문제 해결 방법을 참조하세요.

## <a name="view-event-grid-module-logs"></a>Event Grid 모듈 로그 보기

문제를 해결하려면 Event Grid 모듈 로그에 액세스해야 할 수 있습니다. 모듈이 배포된 VM에서 다음 명령을 실행합니다.

Windows에서

```sh
docker -H npipe:////./pipe/iotedge_moby_engine container logs eventgridmodule
```

Linux에서

```sh
sudo docker logs eventgridmodule
```

## <a name="unable-to-make-https-requests"></a>HTTPS 요청을 수행할 수 없음

* 먼저 Event Grid 모듈의 **inbound:serverAuth:tlsPolicy** 가 **strict** 또는 **enabled** 로 설정되어 있는지 확인합니다.

* 모듈 간 통신의 경우 포트 **4438** 에 대한 호출을 수행하고 모듈의 이름이 배포된 항목과 일치하는지 확인합니다. 

  예를 들어, 이름이 **eventgridmodule** 인 Event Grid 모듈이 배포된 경우 URL은 **https://eventgridmodule:4438** 이어야 합니다. 대/소문자 및 포트 번호가 올바른지 확인합니다.
    
* 예를 들어, 비 IoT 모듈의 경우 배포 중에 Event Grid 포트가 호스트 머신에 매핑되는지 확인합니다.

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

## <a name="unable-to-make-http-requests"></a>HTTP 요청을 수행할 수 없음

* 먼저 Event Grid 모듈의 **inbound:serverAuth:tlsPolicy** 가 **enabled** 또는 **disabled** 로 설정되어 있는지 확인합니다.

* 모듈 간 통신의 경우 포트 **5888** 에 대한 호출을 수행하고 모듈의 이름이 배포된 항목과 일치하는지 확인합니다. 

  예를 들어, 이름이 **eventgridmodule** 인 Event Grid 모듈이 배포된 경우 URL은 **http://eventgridmodule:5888** 이어야 합니다. 대/소문자 및 포트 번호가 올바른지 확인합니다.
    
* 예를 들어, 비 IoT 모듈의 경우 배포 중에 Event Grid 포트가 호스트 머신에 매핑되는지 확인합니다.

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

## <a name="certificate-chain-was-issued-by-an-authority-thats-not-trusted"></a>인증서 체인이 신뢰할 수 없는 인증 기관으로부터 발급됨

기본적으로 Event Grid 모듈은 IoT Edge 보안 디먼에서 발급한 인증서를 사용하여 클라이언트를 인증하도록 구성됩니다. 클라이언트에서 이 체인을 기준으로 하는 인증서를 제공하는지 확인합니다.

[https://github.com/Azure/event-grid-iot-edge](https://github.com/Azure/event-grid-iot-edge)의 **IoTSecurity** 클래스는 IoT Edge 보안 디먼에서 인증서를 검색하고 이를 사용하여 나가는 호출을 구성하는 방법을 보여 줍니다.

비프로덕션 환경인 경우 클라이언트 인증을 해제할 수 있는 옵션이 제공됩니다. 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조하세요.

## <a name="debug-events-not-received-by-subscriber"></a>구독자가 디버그 이벤트를 수신하지 못함

일반적인 이유는 다음과 같습니다.

* 이벤트가 성공적으로 게시되지 않았습니다. 클라이언트는 Event Grid 모듈에 이벤트를 게시할 때 HTTP StatusCode 200(OK)을 수신했어야 하지만 수신하지 못했습니다.

* 이벤트 구독을 통해 다음을 확인하세요.
    * 엔드포인트 URL이 유효합니다.
    * 구독의 필터로 인해 이벤트기 "삭제"되지 않습니다.

* 구독자 모듈이 실행 중인지 확인합니다.

* Event Grid 모듈이 배포된 VM에 로그온한 후 해당 로그를 확인합니다.

* **broker:logDeliverySuccess=true** 를 설정 하 고 Event Grid 모듈을 다시 배포한 후 요청을 다시 시도하여 배달별 로깅을 설정합니다. 배달별 로깅을 켜면 처리량 및 대기 시간에 영향을 줄 수 있으므로 디버깅이 완료되면 **broker:logDeliverySuccess=false** 로 다시 설정하고 Event Grid 모듈을 다시 배포하는 것이 좋습니다.

* **metrics:reportertype=console** 을 설정하여 메트릭을 켜고 Event Grid 모듈을 다시 배포합니다. 그 이후에 작업을 수행하면 추가 디버깅에 사용할 수 있는 메트릭이 Event Grid 모듈의 콘솔에 기록됩니다. 메트릭은 디버깅을 위해서만 켜고, 일단 완료된 후에는 **metrics:reportertype=none** 을 설정하고 Event Grid 모듈을 다시 배포하여 메트릭을 끄는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

IoT Edge의 Event Grid 사용과 관련하여 이슈, 제안 사항이 있는 경우 [https://github.com/Azure/event-grid-iot-edge/issues](https://github.com/Azure/event-grid-iot-edge/issues)에서 보고해 주세요.