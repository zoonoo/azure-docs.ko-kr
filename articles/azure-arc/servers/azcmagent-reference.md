---
title: Azure 연결 된 컴퓨터 에이전트 CLI 인터페이스
description: Azure 연결 된 컴퓨터 에이전트 CLI에 대 한 참조 설명서
author: bobbytreed
manager: carmonm
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
ms.topic: reference
ms.date: 11/04/2019
ms.author: robreed
ms.openlocfilehash: d35c5e283f2e1e2f8afd431d83775167dc2a531a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513199"
---
# <a name="azure-connected-machine-agent-cli-interface"></a>Azure 연결 된 컴퓨터 에이전트 CLI 인터페이스

Azure에 대 한 비 azure 컴퓨터 연결을 구성 하 고 문제를 해결 하는 데 사용 되는 `Azcmagent` (Azure 연결 된 컴퓨터 에이전트) 도구입니다.

에이전트 자체는 Linux에서 `himdsd` 이라는 디먼 프로세스이 고 Windows에서 `himds` 라는 Windows 서비스입니다.

일반적인 사용에서는이 컴퓨터와 Azure 간의 연결을 설정 하는 데 `azcmagent connect`를 사용 하며, 더 이상 해당 연결을 원하지 않는 경우 `azcmagent disconnect` 합니다. 다른 명령은 문제 해결 또는 기타 특수 한 경우에 해당 합니다.

## <a name="options"></a>옵션

```none
  -h, --help      help for azcmagent
  -v, --verbose   Increase logging verbosity to show all logs
```

## <a name="see-also"></a>참고 항목

* [azcmagent connect](#azcmagent-connect) -이 컴퓨터를 Azure에 연결 합니다.
* [azcmagent 연결 끊기](#azcmagent-disconnect) -Azure에서이 컴퓨터의 연결을 끊습니다.
* [azcmagent 다시 연결](#azcmagent-reconnect) -이 컴퓨터를 Azure에 다시 연결 합니다.
* [azcmagent show](#azcmagent-show) -컴퓨터 메타 데이터 및 에이전트 상태를 가져옵니다. 이는 주로 문제 해결에 유용 합니다.
* [azcmagent version](#azcmagent-version) -하이브리드 관리 에이전트 버전을 표시 합니다.

## <a name="azcmagent-connect"></a>azcmagent 연결

이 컴퓨터를 Azure에 연결 합니다.

### <a name="synopsis"></a>개요

Azure에서이 컴퓨터를 나타내는 리소스를 만듭니다.

이는 제공 된 인증 옵션을 사용 하 여이 컴퓨터를 나타내는 Azure Resource Manager에 리소스를 만듭니다. 리소스는 요청 된 구독 및 리소스 그룹에 있으며, 컴퓨터에 대 한 데이터는 location 매개 변수에 지정 된 Azure 지역에 저장 됩니다.
기본 리소스 이름은 재정의 되지 않은 경우이 컴퓨터의 호스트 이름입니다.

그런 다음이 컴퓨터의 시스템 할당 Id에 해당 하는 인증서를 다운로드 하 여 로컬로 저장 합니다. 이 단계가 완료 되 면 **Azure 연결 된 컴퓨터 메타 데이터** 서비스 및 게스트 구성 에이전트가 azure 클라우드와의 동기화를 시작 합니다.

인증 옵션:

* 액세스 토큰 `azcmagent connect --access-token <> --subscription-id <> --resource-group <> --location <>`
* 서비스 사용자 ID 및 암호 `azcmagent connect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid> --subscription-id <> --resource-group <> --location <>`
* 장치 로그인 (대화형) `azcmagent connect --tenant-id <> --subscription-id <> --resource-group <> --location <>`

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

## <a name="azcmagent-disconnect"></a>azcmagent 연결 끊기

Azure에서이 컴퓨터의 연결을 끊습니다.

### <a name="synopsis"></a>개요

이 서버를 나타내는 Azure의 리소스를 삭제 합니다.

이 명령은 제공 된 인증 옵션을 사용 하 여이 컴퓨터를 나타내는 Azure Resource Manager 리소스를 제거 합니다. 이 시점 이후에는 **Azure 연결 된 컴퓨터 Metadata Service** 및 게스트 구성 에이전트가 끊어집니다. 이 명령은 서비스를 중지 하거나 제거 하지 않습니다 .이를 위해 패키지를 제거 합니다.

이 명령에는 "Azure 연결 된 컴퓨터 온 보 딩" 역할 보다 높은 권한이 필요 합니다.

컴퓨터의 연결이 끊어지면 Azure에서 새 리소스를 만들려는 경우에는 `azcmagent reconnect` 하지 않고 `azcmagent connect`을 사용 합니다.

인증 옵션:

* 액세스 토큰 `azcmagent disconnect --access-token <>`
* 서비스 사용자 ID 및 암호 `azcmagent disconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 대화형 장치 로그인 `azcmagent disconnect --tenant-id <>`

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

이 컴퓨터를 Azure에 다시 연결 합니다.

### <a name="synopsis"></a>개요

잘못 된 자격 증명을 사용 하 여 컴퓨터를 Azure에 다시 연결 합니다.

컴퓨터에 이미 Azure의 리소스가 있지만 인증을 할 수 없는 경우이 명령을 사용 하 여 다시 연결할 수 있습니다. 이는 해당 인증서가 만료 될 때까지 (최소 45 일) 컴퓨터가 꺼져 있는 경우에 발생할 수 있습니다.

컴퓨터의 연결을 `azcmagent disconnect`하는 경우 대신 `azcmagent connect`를 사용 합니다.

이 명령은 제공 된 인증 옵션을 사용 하 여이 컴퓨터를 나타내는 Azure Resource Manager 리소스에 해당 하는 새 자격 증명을 검색 합니다.

이 명령에는 **Azure 연결 된 컴퓨터 온 보 딩** 역할 보다 높은 권한이 필요 합니다.

인증 옵션

* 액세스 토큰 `azcmagent reconnect --access-token <>`
* 서비스 사용자 ID 및 암호 `azcmagent reconnect --service-principal-id <> --service-principal-secret <> --tenant-id <tenantid>`
* 대화형 장치 로그인 `azcmagent reconnect --tenant-id <>`

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

## <a name="azcmagent-show"></a>azcmagent show

컴퓨터 메타 데이터 및 에이전트 상태를 가져옵니다. 이는 주로 문제 해결에 유용 합니다.

### <a name="synopsis"></a>개요

컴퓨터 메타 데이터 및 에이전트 상태를 가져옵니다. 이는 주로 문제 해결에 유용 합니다.


### <a name="syntax"></a>구문

```
azcmagent show [flags]
```

### <a name="options"></a>옵션

```
  -h, --help   help for show
```

## <a name="azcmagent-version"></a>azcmagent 버전

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
