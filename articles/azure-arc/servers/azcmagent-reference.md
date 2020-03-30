---
title: Azure Connected Machine Agent CLI 인터페이스
description: Azure 연결된 컴퓨터 에이전트 CLI에 대한 참조 설명서
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513199"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure Connected Machine Agent CLI 인터페이스

`Azcmagent` Azure 연결된 컴퓨터 에이전트) 도구는 Azure가 아닌 컴퓨터 연결을 구성하고 문제를 해결하는 데 사용됩니다.

에이전트 자체는 Linux에서 호출되는 `himdsd` 데몬 프로세스이며 Windows에서 호출된 `himds` Windows 서비스입니다.

일반적인 사용에서는 `azcmagent connect` 이 컴퓨터와 Azure 간의 연결을 설정하는 `azcmagent disconnect` 데 사용되며 더 이상 해당 연결을 원하지 않는다고 결정합니다. 다른 명령은 문제 해결 또는 기타 특수 한 경우에 대 한 것입니다.

## <a name="options"></a>옵션

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>참고 항목

* [azcmagent 연결](#azcmagent-connect) - 이 컴퓨터를 Azure에 연결합니다.
* [azcmagent 연결 끊기](#azcmagent-disconnect) - Azure에서 이 컴퓨터 연결을 끊습니다.
* [azcmagent 다시 연결](#azcmagent-reconnect) - 이 컴퓨터를 Azure에 다시 연결합니다.
* [azcmagent 표시](#azcmagent-show) - 컴퓨터 메타데이터 및 에이전트 상태를 가져옵니다. 이 기능은 주로 문제 해결에 유용합니다.
* [azcmagent 버전](#azcmagent-version) - 하이브리드 관리 에이전트 버전 표시

## <a name="azcmagent-connect"></a>아지에이전트 연결

이 컴퓨터를 Azure에 연결합니다.

### <a name="synopsis"></a>개요

이 컴퓨터를 나타내는 Azure에서 리소스를 만듭니다.

이렇게 하면 제공된 인증 옵션이 제공되며 이 컴퓨터를 나타내는 Azure 리소스 관리자에서 리소스를 만듭니다. 리소스가 요청된 구독 및 리소스 그룹에 있으며 컴퓨터에 대한 데이터는 위치 매개 변수에 의해 지정된 Azure 지역에 저장됩니다.
기본 리소스 이름은 재정의되지 않은 경우 이 컴퓨터의 호스트 이름입니다.

그런 다음 이 컴퓨터의 시스템 할당 ID에 해당하는 인증서를 다운로드하여 로컬로 저장합니다. 이 단계가 완료되면 **Azure 연결된 컴퓨터 메타데이터** 서비스 및 게스트 구성 에이전트가 Azure 클라우드와 동기화를 시작합니다.

인증 옵션:

* 액세스 토큰`azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* 서비스 주체 ID 및 비밀`azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* 장치 로그인(대화형)`azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

### <a name="syntax"></a>구문

```none
azcmagent connect [flags]
```

### <a name="options"></a>옵션

```none
      --access-token string               Access token
  -h, --help                              help for connect
  -l, --location string                   Location of the resource [Required]
      --physical-location string          Physical location of the resource
  -g, --resource-group string             Name of the resource group. [Required]
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id [Required]
  -t, --tags string                       Tags for resource
      --tenant-id string                  Tenant Id
```

## <a name="azcmagent-disconnect"></a>azcmagent 분리

Azure에서 이 컴퓨터 연결을 끊습니다.

### <a name="synopsis"></a>개요

이 서버를 나타내는 Azure의 리소스를 삭제합니다.

이 명령은 제공된 인증 옵션을 사용하여 이 컴퓨터를 나타내는 Azure 리소스 관리자 리소스를 제거합니다. 이 시점이후에 **Azure 연결된 컴퓨터 메타데이터 서비스** 및 게스트 구성 에이전트의 연결이 끊어집니다. 이 명령은 서비스를 중지하거나 제거하지 않습니다.

이 명령은 "Azure 연결된 컴퓨터 온보딩" 역할보다 더 높은 권한이 필요합니다.

컴퓨터의 연결이 끊어지면 `azcmagent connect`Azure에서 새 리소스를 만들려는 경우 가 아니라 `azcmagent reconnect` 를 사용합니다.

인증 옵션:

* 액세스 토큰`azcmagent disconnect --access-token <>`
* 서비스 주체 ID 및 비밀`azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 대화형 장치 로그인`azcmagent disconnect --tenant-id <>`

### <a name="syntax"></a>구문

```none
azcmagent disconnect [flags]
```

### <a name="options"></a>옵션

```none
      --access-token string               Access token
  -h, --help                              help for disconnect
  -r, --resource-group string             Name of the resource group
  -n, --resource-name string              Name of the resource
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
  -t, --tenant-id string                  Tenant Id
```

## <a name="azcmagent-reconnect"></a>azcmagent 다시 연결

이 컴퓨터를 Azure에 다시 연결합니다.

### <a name="synopsis"></a>개요

잘못된 자격 증명을 사용하여 컴퓨터를 Azure에 다시 연결합니다.

컴퓨터에 이미 Azure에 리소스가 있지만 인증할 수 없는 경우 이 명령을 사용하여 다시 연결할 수 있습니다. 인증서가 만료될 수 있을 만큼 컴퓨터가 꺼져 있는 경우(최소 45일) 가능합니다.

컴퓨터연결이 끊어진 `azcmagent disconnect`경우 대신 `azcmagent connect` 사용합니다.

이 명령은 제공된 인증 옵션을 사용하여 이 컴퓨터를 나타내는 Azure 리소스 관리자 리소스에 해당하는 새 자격 증명을 검색합니다.

이 명령은 Azure 연결된 **컴퓨터 온보딩** 역할보다 높은 권한이 필요합니다.

인증 옵션

* 액세스 토큰`azcmagent reconnect --access-token <>`
* 서비스 주체 ID 및 비밀`azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 대화형 장치 로그인`azcmagent reconnect --tenant-id <>`

### <a name="syntax"></a>구문

```none
azcmagent reconnect [flags]
```

### <a name="options"></a>옵션

```none
      --access-token string               Access token
  -h, --help                              help for reconnect
  -l, --location string                   Location of the resource
  -g, --resource-group string             Name of the resource group.
  -n, --resource-name string              Name of the resource. Defaults to Host Name
  -i, --service-principal-id string       Service Principal Id
  -p, --service-principal-secret string   Service Principal Secret
  -s, --subscription-id string            Subscription Id
      --tenant-id string                  tenant id
```

## <a name="azcmagent-show"></a>아즈에이전트 쇼

컴퓨터 메타데이터 및 에이전트 상태를 가져옵니다. 이 기능은 주로 문제 해결에 유용합니다.

### <a name="synopsis"></a>개요

컴퓨터 메타데이터 및 에이전트 상태를 가져옵니다. 이 기능은 주로 문제 해결에 유용합니다.


### <a name="syntax"></a>구문

```
azcmagent show [flags]
```

### <a name="options"></a>옵션

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>아지에이전트 버전

하이브리드 관리 에이전트 버전 표시

### <a name="synopsis"></a>개요

하이브리드 관리 에이전트 버전 표시

### <a name="syntax"></a>구문

```none
azcmagent version [flags]
```

### <a name="options"></a>옵션

```none
  -h, --help   help for version
```
