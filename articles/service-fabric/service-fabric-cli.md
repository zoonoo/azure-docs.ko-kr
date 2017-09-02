---
title: "Azure Service Fabric CLI(sfctl) 시작"
description: "Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다. 클러스터에 연결하는 방법 및 응용 프로그램 암호를 관리하는 방법을 알아봅니다."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 5ce9adf6c82e3a5521883c5de1e0689d5bf0d94e
ms.contentlocale: ko-kr
ms.lasthandoff: 08/24/2017

---
# <a name="azure-service-fabric-command-line"></a>Azure Service Fabric 명령줄

Azure Service Fabric CLI(sfctl)는 Azure Service Fabric 엔터티와 상호 작용하고 관리하기 위한 명령줄 유틸리티입니다. Sfctl은 Windows 또는 Linux 클러스터에서 사용할 수 있습니다. Sfctl은 Python이 지원되는 모든 플랫폼에서 실행됩니다.

## <a name="prerequisites"></a>필수 조건

설치 이전에 사용자 환경이 Python 및 PIP를 모두 설치했는지를 확인합니다. 자세한 내용은 [PIP 빠른 시작 설명서](https://pip.pypa.io/en/latest/quickstart/) 및 공식 [Python 설치 설명서](https://wiki.python.org/moin/BeginnersGuide/Download)를 살펴보세요.

Python 2.7 및 3.6이 모두 지원되지만 Python 3.6을 사용하는 것이 좋습니다.

## <a name="install"></a>설치

Azure Service Fabric CLI(sfctl)가 Python 패키지로 패키징됩니다. 최신 버전을 설치하려면 다음을 실행합니다.

```bash
pip install sfctl
```

설치 후에 `sfctl -h`를 실행하여 사용 가능한 명령에 대한 정보를 얻을 수 있습니다.

## <a name="cli-syntax"></a>CLI 구문

명령은 항상 `sfctl`이라는 접두사가 지정됩니다. 사용할 수 있는 모든 명령에 대한 일반적인 정보는 `sfctl -h`를 사용합니다. 단일 명령의 도움말은 `sfctl <command> -h`를 사용합니다.

명령은 동사 또는 동작 앞에 오는 명령의 대상을 포함한 반복 구조를 따릅니다.

```azurecli
sfctl <object> <action>
```

이 예제에서는 `<object>`는 `<action>`의 대상입니다.

## <a name="select-a-cluster"></a>클러스터 선택

모든 작업을 수행하기 전에 연결할 클러스터를 선택해야 합니다. 예를 들어, 다음을 실행하여 `testcluster.com`이라는 클러스터를 선택하고 클러스터에 연결합니다.

> [!WARNING]
> 프로덕션 환경에는 보안되지 않은 Service Fabric 클러스터를 사용하지 않습니다.

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

클러스터 끝점은 접두사로 `http` 또는 `https`를 사용해야 합니다. HTTP 게이트웨이에 대한 포트를 포함해야 합니다. 포트 및 주소는 Service Fabric Explorer URL와 동일합니다.

인증서로 보호된 클러스터에 PEM 인코딩 인증서를 지정할 수 있습니다. 단일 파일이나 인증서 및 키 쌍으로 인증서를 지정할 수 있습니다.

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

자세한 내용은 [안전한 Azure Service Fabric 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요.

## <a name="basic-operations"></a>기본 작업

클러스터 연결 정보는 여러 sfctl 세션에서 유지됩니다. Service Fabric 클러스터를 선택한 후에 클러스터에서 모든 Service Fabric 명령을 실행할 수 있습니다.

예를 들어, Service Fabric 클러스터 상태를 가져오려면 다음 명령을 실행합니다.

```azurecli
sfctl cluster health
```

이 명령은 다음과 같이 출력됩니다.

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>팁 및 문제 해결

일반적인 문제를 해결하기 위한 몇 가지 제안 및 팁입니다.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>인증서를 PFX에서 PEM 형식으로 변환

Service Fabric CLI는 PEM(확장명 .pem) 파일의 클라이언트 쪽 인증서를 지원합니다. Windows에서 PFX 파일을 사용하는 경우 해당 인증서를 PEM 형식으로 변환해야 합니다. PFX 파일을 PEM 파일로 변환하려면 다음 명령을 사용합니다.

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

자세한 내용은 [OpenSSL 설명서](https://www.openssl.org/docs/)를 참조하세요.

### <a name="connection-issues"></a>연결 문제

일부 작업은 다음과 같은 메시지를 생성할 수 있습니다.

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

지정된 클러스터 끝점을 사용 가능하고 수신 대기하는지를 확인합니다. Service Fabric Explorer UI가 해당 호스트 및 포트에서 사용 가능한지도 확인합니다. 끝점을 업데이트하려면 `sfctl cluster select`를 사용합니다.

### <a name="detailed-logs"></a>자세한 로그

자세한 로그는 종종 문제를 디버그하거나 보고하는 경우에 유용합니다. 로그 파일의 자세한 정보를 증가시키는 전역 `--debug` 플래그가 있습니다.

### <a name="command-help-and-syntax"></a>명령 도움말 및 구문

특정 명령 또는 명령 그룹의 도움말은 `-h` 플래그를 사용합니다.

```azurecli
sfctl application -h
```

다른 예제:

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>다음 단계

* [Azure Service Fabric CLI에서 응용 프로그램 배포](service-fabric-application-lifecycle-sfctl.md)
* [Linux에서 Service Fabric 시작](service-fabric-get-started-linux.md)

