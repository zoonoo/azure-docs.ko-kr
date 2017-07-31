---
title: "Azure Service Fabric 및 Azure CLI 2.0 시작"
description: "Azure CLI 버전 2.0에서 Azure Service Fabric 명령 모듈을 사용하는 방법에 대해 알아봅니다. 클러스터에 연결하는 방법 및 응용 프로그램 암호를 관리하는 방법을 알아봅니다."
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: ee3302b984ca2f5509755dc17b0a5fd06ace0afe
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="azure-service-fabric-and-azure-cli-20"></a>Azure Service Fabric 및 Azure CLI 2.0

Azure CLI(Azure 명령줄 도구) 버전 2.0에는 Azure Service Fabric 클러스터를 관리할 수 있는 명령이 포함됩니다. Azure CLI 및 Service Fabric을 시작하는 방법을 알아봅니다.

## <a name="install-azure-cli-20"></a>Azure CLI 2.0 설치

Azure CLI 2.0 명령을 사용하여 Service Fabric 클러스터와 상호 작용하고 관리할 수 있습니다. Azure CLI의 최신 버전을 가져오려면 [Azure CLI 2.0 표준 설치 프로세스](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)를 따르세요.

자세한 내용은 [Azure CLI 2.0 개요](https://docs.microsoft.com/en-us/cli/azure/overview)를 참조하세요.

## <a name="azure-cli-syntax"></a>Azure CLI 구문

Azure CLI에서 모든 Service Fabric 명령은 접두사로 `az sf`를 사용합니다. 사용할 수 있는 명령에 대한 일반적인 정보는 `az sf -h`를 사용합니다. 단일 명령의 도움말은 `az sf <command> -h`를 사용합니다.

Azure CLI의 Service Fabric 명령은 다음과 같은 명명 패턴을 따릅니다.

```azurecli
az sf <object> <action>
```

`<object>`는 `<action>`의 대상입니다.

## <a name="select-a-cluster"></a>클러스터 선택

모든 작업을 수행하기 전에 연결할 클러스터를 선택해야 합니다. 예제를 보려면 다음 코드를 참조하세요. 코드는 보안되지 않은 클러스터에 연결합니다.

> [!WARNING]
> 프로덕션 환경에는 보안되지 않은 Service Fabric 클러스터를 사용하지 않습니다.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

클러스터 끝점은 접두사로 `http` 또는 `https`를 사용해야 합니다. HTTP 게이트웨이에 대한 포트를 포함해야 합니다. 포트 및 주소는 Service Fabric Explorer URL와 동일합니다.

인증서로 보호된 클러스터는 암호화되지 않은 .pem 파일 또는 .crt와 .key 파일 중 하나를 사용할 수 있습니다. 예:

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

자세한 내용은 [안전한 Azure Service Fabric 클러스터에 연결](service-fabric-connect-to-secure-cluster.md)을 참조하세요.

> [!NOTE]
> `select` 명령은 반환하기 전에 요청에서 작동하지 않습니다. 클러스터를 올바르게 지정했는지 확인하려면 `az sf cluster health`와 같은 명령을 사용합니다. 명령이 오류를 반환하지 않는지 확인합니다.

## <a name="basic-operations"></a>기본 작업

클러스터 연결 정보는 여러 Azure CLI 세션에서 유지됩니다. Service Fabric 클러스터를 선택한 후에 클러스터에서 모든 Service Fabric 명령을 실행할 수 있습니다.

예를 들어, Service Fabric 클러스터 상태를 가져오려면 다음 명령을 실행합니다.

```azurecli
az sf cluster health
```

명령이 다음과 같은 출력을 발생시킵니다(Azure CLI 구성에서 JSON 출력이 지정된 것으로 간주).

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

Azure CLI에서 Service Fabric 명령을 사용하는 동안 문제가 발생하는 경우 다음 정보가 유용할 수 있습니다.

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>인증서를 PFX에서 PEM 형식으로 변환

Azure CLI는 PEM(확장명 .pem) 파일의 클라이언트 쪽 인증서를 지원합니다. Windows에서 PFX 파일을 사용하는 경우 해당 인증서를 PEM 형식으로 변환해야 합니다. PFX 파일을 PEM 파일로 변환하려면 다음 명령을 사용합니다.

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

자세한 내용은 [OpenSSL 설명서](https://www.openssl.org/docs/)를 참조하세요.

### <a name="connection-issues"></a>연결 문제

일부 작업은 다음과 같은 메시지를 생성할 수 있습니다.

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

지정된 클러스터 끝점을 사용 가능하고 수신 대기하는지를 확인합니다. Service Fabric Explorer UI가 해당 호스트 및 포트에서 사용 가능한지도 확인합니다. 끝점을 업데이트하려면 `az sf cluster select`를 사용합니다.

### <a name="detailed-logs"></a>자세한 로그

자세한 로그는 종종 문제를 디버그하거나 보고하는 경우에 유용합니다. Azure CLI는 로그 파일의 자세한 정보를 증가시키는 전역 `--debug` 플래그를 제공합니다.

### <a name="command-help-and-syntax"></a>명령 도움말 및 구문

Service Fabric 명령을 Azure CLI와 동일한 규칙을 따릅니다. 특정 명령 또는 명령 그룹의 도움말은 `-h` 플래그를 사용합니다.

```azurecli
az sf application -h
```

다른 예제는 다음과 같습니다.

```azurecli
az sf application create -h
```

