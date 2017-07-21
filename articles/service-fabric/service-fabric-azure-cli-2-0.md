---
title: "Service Fabric 및 Azure CLI 2.0 시작"
description: "클러스터에 연결 및 응용 프로그램 관리 등 Azure CLI 버전 2.0에서Service Fabric 모듈 명령을 사용하는 방법"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 06/21/2017
ms.author: edwardsa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: c5cc6e54acf27456185eeb48858c4d981aa46b4b
ms.contentlocale: ko-kr
ms.lasthandoff: 07/01/2017

---
# Service Fabric 및 Azure CLI 2.0
<a id="service-fabric-and-azure-cli-20" class="xliff"></a>

새 Azure CLI 2.0은 이제 Service Fabric 클러스터를 관리하는 명령을 포함하게 되었습니다. 이 설명서에는 Azure CLI를 시작하는 단계가 있습니다.

## Azure CLI 2.0 설치
<a id="install-azure-cli-20" class="xliff"></a>

Azure CLI는 이제 Service Fabric 클러스터와 상호 작용하고 관리하는 명령을 포함하게 되었습니다. [표준 설치 프로세스](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)를 따라 최신 Azure CLI를 받을 수 있습니다.

자세한 내용은 [Azure CLI 2.0 설명서](https://docs.microsoft.com/en-us/cli/azure/overview)를 참조하세요.

## CLI 구문
<a id="cli-syntax" class="xliff"></a>

모든 Azure Service Fabric 명령은 Azure CLI에서 `az sf`가 앞에 있어야 합니다. 사용할 수 있는 명령에 대한 자세한 내용은 `az sf -h`를 실행하여 일반 정보를 확인할 수 있습니다. 또는 `az sf <command> -h`를 실행하여 단일 명령에 대한 자세한 도움말을 확인할 수 있습니다.

CLI의 Azure Service Fabric 명령은 명명 패턴을 따릅니다.

```azurecli
az sf <object> <action>
```

여기에서 `<object>`는 `<action>`의 대상이 됩니다.

## 클러스터 선택
<a id="selecting-a-cluster" class="xliff"></a>

모든 작업을 수행하기 전에 연결할 클러스터를 선택해야 합니다. 예를 들어, 보안되지 않은 클러스터에 연결하려면 다음 코드 조각을 참조하세요.

> [!WARNING]
> 프로덕션 환경에는 보안되지 않은 Service Fabric 클러스터를 사용하지 마세요.

```azurecli
az sf cluster select --endpoint http://testcluster.com:19080
```

클러스터 끝점에는 `http` 또는 `https`가 앞에 있어야 하며, HTTP 게이트웨이용 포트를 포함해야 합니다. 이 포트와 주소는 Service Fabric Explorer URL와 같습니다.

인증서가 있는 클러스터 보안의 경우, 암호화되지 않은 `pem` 또는 `crt`와 `key` 파일이 지원됩니다.

```azurecli
az sf cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

자세한 내용은 [보안 클러스터 연결에 대한 자세한 문서](service-fabric-connect-to-secure-cluster.md)를 참조하세요.

> [!NOTE]
> select 명령은 반환 전의 모든 요청을 수행하지 않습니다. 클러스터를 올바르게 지정했는지 확인하려면 `az sf cluster health`와 같은 명령을 실행하고, 명령이 오류 없이 반환되는지 확인합니다.

## 기본 작업 수행
<a id="performing-basic-operations" class="xliff"></a>

클러스터 연결 정보는 서로 다른 Azure CLI 세션에서 유지됩니다. Service Fabric 클러스터가 선택되면 모든 Service Fabric 명령을 실행할 수 있습니다.

예를 들어, Service Fabric 클러스터 상태를 가져오려면 다음 명령을 실행합니다.

```azurecli
az sf cluster health
```

명령 실행 결과 다음과 같이 출력되면 Azure CLI 구성에서 JSON 출력이 지정된 것으로 간주됩니다.

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

## 팁 및 FAQ
<a id="tips-and-faq" class="xliff"></a>

여기에는 Azure CLI에서 Service Fabric 명령을 사용하여 문제가 발생했을 때 도움이 될 수 있는 몇 가지 정보가 나와 있습니다.

### 인증서를 PFX에서 PEM로 변환
<a id="converting-a-certificate-from-pfx-to-pem" class="xliff"></a>

Azure CLI는 PEM(확장명 `.pem`) 파일의 클라이언트 쪽 인증서를 지원합니다. Windows에서 PFX 파일을 사용하는 경우 이러한 인증서를 PEM 형식으로 변환해야 합니다. PFX 파일에서 PEM 파일로 변환하려면 다음 명령을 사용합니다.

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

자세한 내용은 [OpenSSL 설명서](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)를 참조하세요.

### 연결 문제
<a id="connection-issues" class="xliff"></a>

작업을 수행할 때 다음과 같은 오류가 발생할 수 있습니다.

> `Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

이 경우 지정된 클러스터 끝점이 연결 가능하고 수신 중인지 다시 확인하세요. Service Fabric Explorer UI가 해당 호스트 및 포트에 연결 가능한지도 확인합니다. `az sf cluster select`를 사용하여 끝점을 업데이트합니다.

### 자세한 로그 가져오기
<a id="getting-detailed-logs" class="xliff"></a>

문제를 디버깅하거나 보고할 때 자세한 로그를 포함하는 것이 유용합니다. Azure CLI는 로그의 세부 정도를 높이는 전역 `--debug` 플래그를 포함합니다.

### 명령 도움말 및 구문
<a id="command-help-and-syntax" class="xliff"></a>

Service Fabric 명령을 Azure CLI와 동일한 규칙을 따릅니다. `-h` 플래그를 지정하여 특정 명령 또는 명령 그룹에 대한 도움말을 가져옵니다. 예:

```azurecli
az sf application -h
```

또는

```azurecli
az sf application create -h
```

