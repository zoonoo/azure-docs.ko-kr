---
title: Azure Arc 사용 데이터 컨트롤러 문제를 해결 하기 위한 로그 가져오기
description: 서비스 로그를 가져와 Azure Arc 사용 데이터 컨트롤러 문제를 해결 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 71c84b35c001be7fafdc2df53014050ae21dec63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939114"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 사용 데이터 서비스 로그 가져오기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

Azure Arc 사용 가능 데이터 서비스 로그를 검색 하려면 Azure 데이터 CLI 도구가 필요 합니다. [설치 지침](./install-client-tools.md)

관리자 권한으로 Azure Arc 사용 데이터 서비스 컨트롤러 서비스에 로그인 할 수 있어야 합니다.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 사용 데이터 서비스 로그 가져오기

문제 해결을 위해 모든 pod 또는 특정 pod에서 Azure Arc 사용 데이터 서비스 로그를 가져올 수 있습니다.  이 작업을 수행 하려면 명령과 같은 표준 Kubernetes 도구를 사용 `kubectl logs` 하거나,이 문서에서 모든 로그를 한 번에 쉽게 가져올 수 있는 Azure 데이터 CLI 도구를 사용 하 게 됩니다.

먼저 데이터 컨트롤러에 로그인 했는지 확인 합니다.

```console
azdata login
```

그런 후 다음 명령을 실행 하 여 로그를 덤프 합니다.
```console
azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress

#Example:
#azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
```

로그 파일은 기본적으로 ' logs ' 라는 하위 디렉터리에 현재 작업 디렉터리에 생성 됩니다.  매개 변수를 사용 하 여 다른 디렉터리에 로그 파일을 출력할 수 있습니다 `--target-folder` .

매개 변수를 생략 하 여 파일을 압축 하도록 선택할 수 있습니다 `--skip-compress` .

를 생략 하 여 메모리 덤프를 트리거하고 포함할 수 `--exclude-dumps` 있지만 Microsoft 지원 메모리 덤프를 요청 하지 않는 한이는 권장 되지 않습니다.  메모리 덤프를 사용 하려면 데이터 컨트롤러 설정이 `allowDumps` 데이터 컨트롤러를 만드는 시간으로 설정 되어 있어야 합니다 `true` .

필요에 따라 특정 pod ( `--pod` ) 또는 컨테이너 ()에 대 한 로그를 이름으로 수집 하도록 필터링 하도록 선택할 수 있습니다 `--container` .

및 매개 변수를 전달 하 여 특정 사용자 지정 리소스에 대 한 로그를 수집 하도록 필터링 하도록 선택할 수도 있습니다 `--resource-kind` `--resource-name` .  `resource-kind`매개 변수 값은 명령으로 검색할 수 있는 사용자 지정 리소스 정의 이름 중 하나 여야 합니다 `kubectl get customresourcedefinition` .

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>

#Example
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

폴더 계층 구조의 예입니다.  폴더 계층 구조는 pod 이름 이름 및 컨테이너를 기준으로 한 다음 컨테이너 내의 디렉터리 계층 구조에 따라 구성 됩니다.

```console
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```