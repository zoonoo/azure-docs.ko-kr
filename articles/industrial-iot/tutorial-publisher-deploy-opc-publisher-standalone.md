---
title: Microsoft OPC 게시자 배포
description: 이 자습서에서는 OPC 게시자를 독립 실행형 모드로 배포하는 방법을 알아봅니다.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: a47f9d6a47876f09ca241c30e3443886642e4fd8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137241"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>자습서: OPC 게시자 배포

OPC 게시자는 산업 자산과 Microsoft Azure 클라우드 간의 차이를 극복하기 위해 공개적으로 개발되어 완벽하게 지원되는 Microsoft 제품입니다. 이는 OPC UA 사용 자산 또는 산업용 연결 소프트웨어에 연결하여 수행하고, 원격 분석 데이터를 IEC62541 OPC UA PubSub 표준 형식(버전 2.6 이상)을 포함한 다양한 형식으로 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)에 게시합니다.

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/)에서 모듈로 실행되거나 일반 Docker에서 컨테이너로 실행됩니다. [.NET 플랫폼 간 런타임](/dotnet/core/introduction)을 활용하므로 Linux 및 Windows 10에서도 기본적으로 실행됩니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * OPC 게시자 배포
> * 최신 릴리스 버전의 OPC 게시자를 컨테이너로 실행
> * Azure Portal에서 컨테이너 만들기 옵션 지정

Azure 구독이 없는 경우 평가판 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- IoT Hub를 만들어야 합니다.
- IoT Edge 디바이스를 만들어야 합니다.

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Azure Marketplace에서 OPC 게시자 배포

1. 사용할 Azure 구독을 선택합니다. 사용할 수 있는 Azure 구독이 없으면 하나를 만들어야 합니다.
2. OPC 게시자에서 데이터를 보내야 하는 IoT Hub를 선택합니다. 사용할 수 있는 IoT Hub가 없으면 하나를 만들어야 합니다.
3. OPC 게시자를 실행해야 하는 IoT Edge 디바이스를 선택하거나 새로 만들 IoT Edge 디바이스의 이름을 입력합니다.
4. 만들기를 클릭합니다. 선택한 IoT Edge 디바이스에 대한 "디바이스에서 모듈 설정" 페이지가 열립니다.
5. "OPCPublisher"를 클릭하여 OPC 게시자의 "IoT Edge 모듈 업데이트" 페이지를 연 다음, "컨테이너 만들기 옵션"을 선택합니다.
6. OPC 게시자 사용에 따라 추가 컨테이너 만들기 옵션을 지정합니다. 아래의 다음 섹션을 참조하세요.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>OPC 게시자에 대한 Microsoft Container Registry Docker 컨테이너에 수동으로 액세스

최신 릴리스 버전의 OPC 게시자는 다음을 통해 수동으로 실행할 수 있습니다.

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

여기서 "name"은 컨테이너의 이름입니다.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Azure Portal에서 컨테이너 만들기 옵션 지정
Azure Portal을 통해 OPC 게시자를 배포하는 경우 OPC 게시자의 IoT Edge 모듈 업데이트 페이지에서 컨테이너 만들기 옵션을 지정할 수 있습니다. 이러한 만들기 옵션은 JSON 형식이어야 합니다. OPC 게시자 명령줄 인수는 Cmd 키를 통해 지정할 수 있습니다. 예를 들어 다음과 같습니다.
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

OPC 게시자에 대한 일반적인 일단의 IoT Edge 모듈 컨테이너 만들기 옵션은 다음과 같습니다.
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

이러한 옵션이 지정되면 OPC 게시자에서 `./pn.json` 구성 파일을 읽습니다. OPC 게시자의 작업 디렉터리는 시작할 때 `/appdata`로 설정되므로 OPC 게시자는 해당 Docker 컨테이너 내에 있는 `/appdata/pn.json` 파일을 읽습니다. OPC 게시자의 로그 파일이 `/appdata`에 기록되고, `CertificateStores` 디렉터리(OPC UA 인증서에 사용됨)도 이 디렉터리에 만들어집니다. IoT Edge 호스트 파일 시스템에서 이러한 파일을 사용할 수 있게 하려면 컨테이너 구성에 바인딩 탑재 볼륨이 필요합니다. `/iiotedge:/appdata` 바인딩은 `/appdata` 디렉터리를 `/iiotedge` 호스트 디렉터리(없는 경우 IoT Edge 런타임에서 만들어짐)에 매핑합니다.
**이 바인딩 탑재 볼륨이 없으면 컨테이너를 다시 시작할 때 모든 OPC 게시자 구성 파일이 손실됩니다.**

DNS 서버가 네트워크에 구성되지 않은 호스트 이름을 사용하여 OPC UA 서버에 연결하려면 `ExtraHosts` 항목을 `HostConfig` 섹션에 추가하면 됩니다.

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>다음 단계 
이제 OPC 게시자 Edge 모듈을 배포했으므로 다음 단계에서는 이 모듈을 구성합니다.

> [!div class="nextstepaction"]
> [OPC 게시자 구성](tutorial-publisher-configure-opc-publisher.md)