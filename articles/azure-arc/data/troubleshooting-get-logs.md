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
ms.openlocfilehash: 625092e0557d40051e1ffd538a496c20edc0222f
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320196"
---
# <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 사용 데이터 서비스 로그 가져오기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

계속 하기 전에 다음이 필요 합니다.

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. [설치 지침](./install-client-tools.md)
* Azure Arc 사용 데이터 서비스 컨트롤러에 로그인 하는 데 사용 되는 관리자 계정입니다.

## <a name="get-azure-arc-enabled-data-services-logs"></a>Azure Arc 사용 데이터 서비스 로그 가져오기

문제 해결을 위해 모든 pod 또는 특정 pod에서 Azure Arc 사용 데이터 서비스 로그를 가져올 수 있습니다. 명령 또는이 문서에서 표준 Kubernetes 도구를 사용 하 여이 작업을 수행할 수 있습니다 `kubectl logs` [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .이 도구를 사용 하면 한 번에 모든 로그를 쉽게 가져올 수 있습니다.

1. 관리자 계정을 사용 하 여 데이터 컨트롤러에 로그인 합니다.

   ```console
   azdata login
   ```

2. 다음 명령을 실행 하 여 로그를 덤프 합니다.

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   예를 들면 다음과 같습니다.

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

데이터 컨트롤러는 라는 하위 디렉터리에 현재 작업 디렉터리에 로그 파일을 만듭니다 `logs` . 

## <a name="options"></a>옵션

`azdata arc dc debug copy-logs` 는 출력을 관리 하기 위해 다음 옵션을 제공 합니다.

* 매개 변수를 사용 하 여 로그 파일을 다른 디렉터리에 출력 합니다 `--target-folder` .
* 매개 변수를 생략 하 여 파일을 압축 합니다 `--skip-compress` .
* 를 생략 하 여 메모리 덤프를 트리거하고 포함 `--exclude-dumps` 합니다. Microsoft 지원에서 메모리 덤프를 요청 하지 않은 경우에는이 방법을 권장 하지 않습니다. 메모리 덤프를 사용 하려면 데이터 컨트롤러 설정이 `allowDumps` 데이터 컨트롤러를 만드는 시간으로 설정 되어 있어야 합니다 `true` .
* 특정 pod ( `--pod` ) 또는 컨테이너 ()에 대 한 로그를 이름으로 수집 하도록 필터링 `--container` 합니다.
* 및 매개 변수를 전달 하 여 특정 사용자 지정 리소스에 대 한 로그를 수집 하도록 필터링 `--resource-kind` `--resource-name` 합니다. `resource-kind`매개 변수 값은 명령으로 검색할 수 있는 사용자 지정 리소스 정의 이름 중 하나 여야 합니다 `kubectl get customresourcedefinition` .

이러한 매개 변수를 사용 하 여 다음 예제에서를 바꿀 수 있습니다 `<parameters>` . 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

예를 들면 다음과 같습니다.

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

폴더 계층 구조의 예입니다. 폴더 계층 구조는 pod 이름, 컨테이너, 컨테이너 내의 디렉터리 계층 구조를 기준으로 구성 됩니다.

```output
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

## <a name="next-steps"></a>다음 단계

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)