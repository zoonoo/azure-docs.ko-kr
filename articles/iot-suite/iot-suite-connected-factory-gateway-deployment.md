---
title: "연결된 팩터리 게이트웨이 배포 - Azure | Microsoft Docs"
description: "Windows 또는 Linux에서 게이트웨이를 배포하여 연결된 팩터리의 미리 구성된 솔루션에 대한 연결을 사용하도록 설정하는 방법"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 32a62be9578ac802ee8fff1b0aa48e2d39362e63
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>연결된 팩터리의 미리 구성된 솔루션을 위해 Windows 또는 Linux에 게이트웨이 배포

연결된 팩터리 미리 구성된 솔루션의 게이트웨이를 배포하는 데 필요한 소프트웨어에는 구성 요소가 두 개 있습니다.

* *OPC 프록시*는 IoT Hub에 대한 연결을 설정합니다. *OPC 프록시*는 연결된 공장 솔루션 포털에서 실행되는 통합된 OPC 브라우저의 명령 및 제어 메시지를 기다립니다.
* *OPC 게시자*는 기존 온-프레미스 OPC UA 서버에 연결하고 해당 서버의 원격 분석 메시지를 IoT Hub에 전달합니다.

두 구성 요소는 오픈 소스이며 GitHub의 원본 및 Docker 컨테이너로 사용할 수 있습니다.

| GitHub | DockerHub |
| ------ | --------- |
| [OPC 게시자][lnk-publisher-github] | [OPC 게시자][lnk-publisher-docker] |
| [OPC 프록시][lnk-proxy-github] | [OPC 프록시][lnk-proxy-docker] |

각 구성 요소에는 게이트웨이 방화벽에 공용 IP 주소 또는 빈 영역이 필요합니다. OPC 프록시와 OPC 게시자는 443, 5671 및 8883 아웃바운드 포트만 사용합니다.

이 문서의 단계에서는 [Windows](#windows-deployment) 또는 [Linux](#linux-deployment)에서 Docker를 사용하여 게이트웨이를 배포하는 방법을 보여 줍니다. 게이트웨이를 사용하면 연결된 팩터리의 미리 구성된 솔루션에 대한 연결을 사용하도록 설정할 수 있습니다.

> [!NOTE]
> Docker 컨테이너에서 실행되는 게이트웨이 소프트웨어는 [Azure IoT Edge]입니다.

## <a name="windows-deployment"></a>Windows 배포

> [!NOTE]
> 게이트웨이 장치가 아직 없는 경우 파트너로부터 상용 게이트웨이를 구입하는 것이 좋습니다. [Azure IoT 장치 카탈로그]에서 연결된 팩터리 솔루션과 호환되는 게이트웨이 장치 목록을 확인하세요. 장치와 함께 제공되는 지침에 따라 게이트웨이를 설정합니다. 또는 다음 지침을 사용하여 기존 게이트웨이 중 하나를 수동으로 설정합니다.

### <a name="install-docker"></a>Docker 설치

Windows 기반 게이트웨이 장치에 [Windows용 Docker]를 설치합니다. Windows Docker 설치 동안 Docker와 공유할 호스트 컴퓨터의 드라이브를 선택합니다. 다음 스크린샷은 Windows 시스템에서 D 드라이브를 공유 과정을 보여 합니다.

![Docker 설치][img-install-docker]

그런 다음 공유 드라이브의 루트에 **docker**라는 폴더를 만듭니다.
또한 **설정** 메뉴에서 docker를 설치한 후 이 단계를 수행할 수도 있습니다.

### <a name="configure-the-gateway"></a>게이트웨이 구성

1. 게이트웨이 배포를 완료하려면 Azure IoT Suite 연결된 팩터리 배포의 **iothubowner** 연결 문자열이 필요합니다. [Azure Portal]에서 연결된 팩터리 솔루션을 배포할 때 만든 리소스 그룹의 IoT Hub로 이동합니다. **공유 액세스 정책**을 클릭하여 **iothubowner** 연결 문자열에 액세스합니다.

    ![IoT Hub 연결 문자열 찾기][img-hub-connection]

    **연결 문자열 - 기본 키** 값을 복사합니다.

1. 다음을 사용하여 명령 프롬프트에서 두 게이트웨이 모듈을 **한번에** 실행하여 IoT Hub에 대한 게이트웨이를 구성합니다.

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;**은 **게시자.&lt;정규화된 도메인 이름&gt;** 형식으로 OPC UA 게시자에 지정하는 이름입니다. 예를 들어 팩터리 네트워크 이름이 **myfactorynetwork.com**일 경우 **ApplicationName** 값은 **publisher.myfactorynetwork.com**입니다.
    * **&lt;IoTHubOwnerConnectionString&gt;**은 이전 단계에서 복사한 **iothubowner** 연결 문자열입니다. 이 연결 문자열은 이 단계에서만 사용하며 다음 단계에서 다시 필요하지 않습니다.

    매핑된 D:\\docker 폴더(`-v` 인수)는 게이트웨이 모듈에서 사용되는 두 X.509 인증서를 유지하기 위해 나중에 사용됩니다.

### <a name="run-the-gateway"></a>게이트웨이 실행

1. 다음 명령을 사용하여 게이트웨이를 다시 시작합니다.

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -D /mapped/cs.db`

1. 보안상의 이유로 D:\\docker 폴더에 유지되는 두 X.509 인증서는 개인 키를 포함합니다. 이 폴더에 대한 액세스는 Docker 컨테이너를 실행하는 데 사용된 자격 증명(일반적으로 **Administrators**)으로 제한됩니다. D:\\docker 폴더를 마우스 오른쪽 단추로 클릭하고 **속성**, **보안**, **편집**을 차례로 선택합니다. **Administrators**에 모든 권한을 제공하고 다른 모든 사용자를 제거합니다.

    ![Docker 공유에 대한 권한 부여][img-docker-share]

1. 네트워크 연결을 확인합니다. 명령 프롬프트에서 명령 `ping publisher.<your fully qualified domain name>`을 입력하여 게이트웨이를 ping합니다. 대상에 연결할 수 없는 경우 게이트웨이의 호스트 파일에 게이트웨이의 IP 주소와 이름을 추가합니다. 호스트 파일은 **Windows\\System32\\drivers\\etc** 폴더에 있습니다.

1. 다음으로 게이트웨이에서 실행되는 로컬 OPC UA 클라이언트를 사용하여 게시자에 연결하려고 합니다. OPC UA 끝점 URL은 `opc.tcp://publisher.<your fully qualified domain name>:62222`입니다. OPC UA 클라이언트가 없으면 [오픈 소스 OPC UA 클라이언트]를 다운로드하여 사용할 수 있습니다.

1. 이러한 로컬 테스트를 성공적으로 완료한 경우 연결된 팩터리 솔루션 포털에서 **자체 OPC UA 서버 연결** 페이지로 이동합니다. 게시자 끝점 URL(`tcp://publisher.<your fully qualified domain name>:62222`)을 입력하고 **연결**을 클릭합니다. 인증서 경고가 표시되면 **계속**을 클릭합니다. 다음으로 게시자가 UA 웹 클라이언트를 신뢰하지 않는다는 오류가 표시됩니다. 이 오류를 해결하려면 **D:\\docker\\Rejected Certificates\\certs** 폴더의 **UA 웹 클라이언트** 인증서를 게이트웨이의 **D:\\docker\\UA Applications\\certs** 폴더로 복사합니다. 게이트웨이를 다시 시작할 필요는 없습니다. 이 단계를 반복합니다. 이제 클라우드에서 게이트웨이로 연결할 수 있으며 솔루션에 OPC UA 서버를 추가할 준비가 되었습니다.

### <a name="add-your-opc-ua-servers"></a>OPC UA 서버 추가

1. 연결된 팩터리 솔루션 포털에서 **자체 OPC UA 서버 연결** 페이지로 이동합니다. 이전 섹션과 동일한 단계를 수행하여 연결된 팩터리 포털과 OPC UA 서버 간에 트러스트를 설정합니다. 이 단계를 수행하면 연결된 팩터리 포털 및 OPC UA 서버에서의 인증서 상호 트러스트가 설정되고 연결이 만들어집니다.

1. OPC UA 서버의 OPC UA 노드 트리로 이동한 후 OPC 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 이렇게 게시하려면 OPC UA 서버 및 게시자가 동일한 네트워크에 있어야 합니다. 예를 들어 게시자의 정규화된 도메인 이름이 **publisher.mydomain.com**이면 OPC UA 서버의 정규화된 도메인 이름은 **myopcuaserver.mydomain.com**이어야 합니다. 설정이 다른 경우 **D:\\docker** 폴더에 있는 publishesnodes.json 파일에 수동으로 노드를 추가할 수 있습니다. OPC 노드가 처음으로 성공적으로 게시되면 publishesnodes.json 파일이 자동으로 생성됩니다.

1. 이제 원격 분석이 게이트웨이 장치로 진행됩니다. **새 팩터리** 아래에 있는 연결된 팩터리 포털의 **팩터리 위치** 보기에서 원격 분석을 볼 수 있습니다.

## <a name="linux-deployment"></a>Linux 배포

> [!NOTE]
> 게이트웨이 장치가 아직 없는 경우 파트너로부터 상용 게이트웨이를 구입하는 것이 좋습니다. [Azure IoT 장치 카탈로그]에서 연결된 팩터리 솔루션과 호환되는 게이트웨이 장치 목록을 확인하세요. 장치와 함께 제공되는 지침에 따라 게이트웨이를 설정합니다. 또는 다음 지침을 사용하여 기존 게이트웨이 중 하나를 수동으로 설정합니다.

Linux 게이트웨이 장치에서 [Docker를 설치]합니다.

### <a name="configure-the-gateway"></a>게이트웨이 구성

1. 게이트웨이 배포를 완료하려면 Azure IoT Suite 연결된 팩터리 배포의 **iothubowner** 연결 문자열이 필요합니다. [Azure Portal]에서 연결된 팩터리 솔루션을 배포할 때 만든 리소스 그룹의 IoT Hub로 이동합니다. **공유 액세스 정책**을 클릭하여 **iothubowner** 연결 문자열에 액세스합니다.

    ![IoT Hub 연결 문자열 찾기][img-hub-connection]

    **연결 문자열 - 기본 키** 값을 복사합니다.

1. 다음을 사용하여 셸에서 두 게이트웨이 모듈을 **한번에** 실행하여 IoT Hub에 대한 게이트웨이를 구성합니다.

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * **&lt;ApplicationName&gt;** 은 게이트웨이가 **게시자.&lt;정규화된 도메인 이름&gt;** 형식으로 만드는 OPC UA 응용 프로그램의 이름입니다. 예를 들면 **publisher.microsoft.com**과 같습니다.
    * **&lt;IoTHubOwnerConnectionString&gt;**은 이전 단계에서 복사한 **iothubowner** 연결 문자열입니다. 이 연결 문자열은 이 단계에서만 사용하며 다음 단계에서 다시 필요하지 않습니다.

    **/shared** 폴더(`-v` 인수)는 게이트웨이 모듈에서 사용되는 두 X.509 인증서를 유지하기 위해 나중에 사용됩니다.

### <a name="run-the-gateway"></a>게이트웨이 실행

1. 다음 명령을 사용하여 게이트웨이를 다시 시작합니다.

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 --rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:2.1.1 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:1.0.2 -D /mapped/cs.db`

1. 보안상의 이유로 **/shared** 폴더에 유지되는 두 X.509 인증서는 개인 키를 포함합니다. 이 폴더에 대한 액세스는 Docker 컨테이너를 실행하는 데 사용된 자격 증명으로 제한됩니다. **루트**에 대해서만 사용 권한을 설정하려면 해당 폴더에 대해 `chmod` 셸 명령을 사용합니다.

1. 네트워크 연결을 확인합니다. 셸에서 명령 `ping publisher.<your fully qualified domain name>`을 입력하여 게이트웨이를 ping합니다. 대상에 연결할 수 없는 경우 게이트웨이의 hosts 파일에 게이트웨이의 IP 주소와 이름을 추가합니다. 호스트 파일은 **/etc** 폴더에 있습니다.

1. 다음으로 게이트웨이에서 실행되는 로컬 OPC UA 클라이언트를 사용하여 게시자에 연결하려고 합니다. OPC UA 끝점 URL은 `opc.tcp://publisher.<your fully qualified domain name>:62222`입니다. OPC UA 클라이언트가 없으면 [오픈 소스 OPC UA 클라이언트]를 다운로드하여 사용할 수 있습니다.

1. 이러한 로컬 테스트를 성공적으로 완료한 경우 연결된 팩터리 솔루션 포털에서 **자체 OPC UA 서버 연결** 페이지로 이동합니다. 게시자 끝점 URL(`tcp://publisher.<your fully qualified domain name>:62222`)을 입력하고 **연결**을 클릭합니다. 인증서 경고가 표시되면 **계속**을 클릭합니다. 다음으로 게시자가 UA 웹 클라이언트를 신뢰하지 않는다는 오류가 표시됩니다. 이 오류를 해결하려면 **/shared/Rejected Certificates/certs** 폴더의 **UA 웹 클라이언트** 인증서를 게이트웨이의 **/shared/UA Applications/certs** 폴더로 복사합니다. 게이트웨이를 다시 시작할 필요는 없습니다. 이 단계를 반복합니다. 이제 클라우드에서 게이트웨이로 연결할 수 있으며 솔루션에 OPC UA 서버를 추가할 준비가 되었습니다.

### <a name="add-your-opc-ua-servers"></a>OPC UA 서버 추가

1. 연결된 팩터리 솔루션 포털에서 **자체 OPC UA 서버 연결** 페이지로 이동합니다. 이전 섹션과 동일한 단계를 수행하여 연결된 팩터리 포털과 OPC UA 서버 간에 트러스트를 설정합니다. 이 단계를 수행하면 연결된 팩터리 포털 및 OPC UA 서버에서의 인증서 상호 트러스트가 설정되고 연결이 만들어집니다.

1. OPC UA 서버의 OPC UA 노드 트리로 이동한 후 OPC 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 이렇게 게시하려면 OPC UA 서버 및 게시자가 동일한 네트워크에 있어야 합니다. 예를 들어 게시자의 정규화된 도메인 이름이 **publisher.mydomain.com**이면 OPC UA 서버의 정규화된 도메인 이름은 **myopcuaserver.mydomain.com**이어야 합니다. 설정이 다른 경우 **/shared** 폴더에 있는 publishesnodes.json 파일에 수동으로 노드를 추가할 수 있습니다. OPC 노드가 처음으로 성공적으로 게시되면 publishesnodes.json이 자동으로 생성됩니다.

1. 이제 원격 분석이 게이트웨이 장치로 진행됩니다. **새 팩터리** 아래에 있는 연결된 팩터리 포털의 **팩터리 위치** 보기에서 원격 분석을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

연결된 팩터리 미리 구성된 솔루션의 아키텍처에 대한 자세한 내용은 [연결된 팩터리 미리 구성된 솔루션 연습][lnk-walkthrough]을 참조하세요.

[OPC 게시자 참조 구현](iot-suite-connected-factory-publisher.md)에 대해 살펴봅니다.

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[Windows용 Docker]: https://www.docker.com/docker-windows
[Azure IoT 장치 카탈로그]: https://catalog.azureiotsuite.com/?q=opc
[Azure Portal]: http://portal.azure.com/
[오픈 소스 OPC UA 클라이언트]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[Docker를 설치]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy