---
title: Azure 산업용 IoT 구성 요소 구성
description: 이 자습서에서는 구성의 기본값을 변경하는 방법을 알아봅니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 348276a71b2227063374da6455445118fcb00fcf
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787292"
---
# <a name="tutorial-configure-the-industrial-iot-components"></a>자습서: 산업용 IoT 구성 요소 구성

배포 스크립트는 기본값을 사용하여 서로 작동하도록 모든 구성 요소를 자동으로 구성합니다. 그러나 기본값 설정은 요구 사항에 맞게 변경할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 구성 요소의 구성 사용자 지정


구성 요소와 관련된 몇 가지 사용자 지정 설정은 다음과 같습니다.
* IoT Hub
    * 네트워킹→ 퍼블릭 액세스: 인터넷 액세스(예: IP 필터)를 구성합니다
    * 네트워킹 → 프라이빗 엔드포인트 연결: 인터넷을 통해 액세스할 수 없고 다른 Azure 서비스 또는 온-프레미스 디바이스에서 내부적으로(예: VPN 연결을 통해) 사용할 수 있는 엔드포인트를 만듭니다.
    * IoT Edge: OPC UA 서버에 연결된 에지 디바이스의 구성을 관리합니다. 
* Cosmos DB
    * 전역적으로 데이터 복제: 데이터 중복성을 구성합니다.
    * 방화벽 및 가상 네트워크: 인터넷/VNET 액세스 및 IP 필터를 구성합니다.
    * 프라이빗 엔드포인트 연결: 인터넷을 통해 액세스할 수 없는 엔드포인트를 만듭니다. 
* Key Vault
    * 비밀: 플랫폼 설정을 관리합니다.
    * 액세스 정책: Key Vault의 데이터에 액세스할 수 있는 애플리케이션/사용자 및 네트워크, 방화벽, VNET 및 프라이빗 엔드포인트에서 수행할 수 있는 작업(예: 읽기, 쓰기, 목록, 삭제)을 관리합니다.
* AAD(Azure Active Directory) → 앱을 등록합니다.
    * <APP_NAME>-web → 인증: 인증에 성공한 후 방문 페이지로 사용할 수 있는 URI 목록인 회신 URI를 관리합니다. 배포 스크립트는 AAD 관리자 권한 부족과 같은 특정 시나리오에서 이를 자동으로 구성하지 못할 수 있습니다. 웹앱의 호스트 이름을 변경할 때 URI를 추가하거나 수정할 수 있습니다(예: 디버깅을 위해 localhost에서 사용하는 포트 번호).
* App Service
    * 구성: 서비스 또는 UI를 제어하는 환경 변수를 관리합니다.
* 가상 머신
    * 네트워킹: 지원되는 네트워크 및 방화벽 규칙을 구성합니다.
    * 직렬 콘솔: 인사이트를 얻거나 디버깅을 위해 SSH에 액세스하거나, 배포 스크립트의 출력에서 자격 증명을 가져오거나, 암호를 다시 설정합니다.
* IoT Hub → IoT Edge
    * 허브에 액세스할 수 있는 IoT Edge 디바이스의 ID를 관리하고, 설치된 모듈 및 사용하는 구성(예: OPC 게시자에 대한 인코딩 매개 변수)을 구성합니다.
* IoT Hub → IoT Edge → \<DEVICE> → 모듈 설정 → OpcPublisher(독립 실행형 OPC 게시자 작업에만 해당)


## <a name="configuration-options"></a>구성 옵션

|구성 옵션(축약/전체 이름)    |    Description   |
|----------------------------------------------|------------------|
pf/publishfile |게시할 노드를 구성하는 파일 이름입니다. 이 옵션을 지정하면 OPC 게시자가 독립 실행형 모드로 전환됩니다.
lf/logfile |사용할 로그 파일의 파일 이름입니다.
ll/loglevel |사용할 로그 수준입니다(허용되는 값: fatal, error, warn, info, debug, verbose).
me/messageencoding |나가는 메시지에 대한 메시징 인코딩입니다(허용되는 값: Json, Uadp).
mm/messagingmode |나가는 메시지에 대한 메시징 모드입니다(허용되는 값: PubSub, Samples).
fm/fullfeaturedmessage |메시지에 대한 전체 기능을 갖춘 모드(모든 필드가 채워짐)입니다. 기본값은 'true'이며, 레거시 호환성의 경우 'false'를 사용합니다.
aa/autoaccept |게시자에서 연결된 모든 서버를 신뢰합니다.
bs/batchsize |일괄 처리를 위해 캐시할 OPC UA 데이터 변경 메시지 수입니다.
si/iothubsendinterval |트리거 일괄 처리 간격(초)입니다.
ms/iothubmessagesize |(IoT D2C) 메시지의 최대 크기입니다.
om/maxoutgressmessages |(IoT D2C) 메시지 송신 버퍼의 최대 크기입니다.
di/diagnosticsinterval |게시자 진단 정보를 지정된 간격(초)으로 표시합니다(로그 수준 정보 필요). -1은 원격 진단 로그 및 진단 출력을 사용하지 않도록 설정합니다.
lt/logflugtimespan |로그 파일을 플러시해야 하는 시간 범위(초)입니다.
ih/iothubprotocol |허브와의 통신에 사용할 프로토콜입니다. 허용되는 값: AmqpOverTcp, AmqpOverWebsocket, MqttOverTcp, MqttOverWebsocket, Amqp, Mqtt, Tcp, Websocket, Any
hb/heartbeatinterval |게시자에서 이 값을 하트비트 간격 설정이 없는 노드의 하트비트 간격 설정에 대한 기본값(초)으로 사용합니다.
ot/operationtimeout |게시자 OPC UA 클라이언트의 작업 시간 제한(밀리초)입니다.
ol/opcmaxstringlen |전송/수신할 수 있는 opc 문자열의 최대 길이입니다.
oi/opcsamplinginterval |값을 샘플링하도록 서버에 요청하는 기본값(밀리초)입니다.
op/opcpublishinginterval |OPC UA 서버에 대한 구독의 게시 간격 설정에 대한 기본값(밀리초)입니다.
ct/createsessiontimeout |게시자에서 연결 유지 메시지를 연결된 엔드포인트의 OPC 서버에 보내는 간격(초)입니다.
kt/keepalivethresholt |세션 연결이 끊어지기 전에 서버에서 놓칠 수 있는 연결 유지 패킷 수를 지정합니다.
tm/trustmyself |게시자 인증서가 신뢰할 수 있는 저장소에 자동으로 저장됩니다.
at/appcertstoretype |고유한 애플리케이션 인증서 저장소 유형입니다(허용되는 값: Directory, X509Store).


## <a name="next-steps"></a>다음 단계
이제 구성의 기본값을 변경하는 방법을 알아보았으므로 다음을 수행할 수 있습니다. 

> [!div class="nextstepaction"]
> [ADX로 IIoT 데이터 끌어오기](tutorial-industrial-iot-azure-data-explorer.md)

> [!div class="nextstepaction"]
> [Time Series Insights를 사용하여 데이터 시각화 및 분석](tutorial-visualize-data-time-series-insights.md)
