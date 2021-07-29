---
title: Azure Arc에서 Azure SQL Managed Instance 만들기
description: Azure Arc에서 Azure SQL Managed Instance 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1d4faaaf6b1a459e8af679a43502d816d9c98fbd
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110495889"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc에서 Azure SQL Managed Instance 만들기

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 로그인

인스턴스를 만들기 전에 아직 로그인하지 않은 경우 Azure Arc 데이터 컨트롤러에 로그인합니다.

```console
azdata login
```

그런 다음, 사용자 이름, 암호 및 시스템 네임스페이스를 묻는 메시지가 표시됩니다.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance 만들기

SQL Managed Instance에 사용 가능한 만들기 옵션을 보려면 다음 명령을 사용합니다.
```console
azdata arc sql mi create --help
```

SQL Managed Instance를 만들려면 다음 명령을 사용합니다.

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

예제:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  이름은 13자 미만이어야 하며 [DNS 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)을 준수해야 합니다.
>
>  메모리 할당 및 vCore 할당을 지정할 때 이 수식을 사용하여 성공적으로 만들 수 있습니다. 각 1개 vCore에 대해 SQL 관리형 인스턴스 Pod가 실행되는 Kubernetes 노드에서 4GB 이상의 RAM을 사용할 수 있어야 합니다.
>
>  Azure에서 프로비저닝하는 경우 SQL 인스턴스를 만들 때 이름에 대문자를 사용하지 않습니다.
>
>  Kubernetes 클러스터에서 사용 가능한 스토리지 클래스를 나열하려면 `kubectl get storageclass`를 실행합니다. 


> [!NOTE]
> SQL 인스턴스 만들기를 자동화하고 관리자 암호에 대한 대화식 프롬프트를 피하려면 `azdata arc sql mi create` 명령을 실행하기 전에 `AZDATA_USERNAME` 및 `AZDATA_PASSWORD` 환경 변수를 원하는 사용자 이름 및 암호로 설정하면 됩니다.
> 
>  동일한 터미널 세션에서 AZDATA_USERNAME 및 AZDATA_PASSWORD를 사용하여 데이터 컨트롤러를 만든 경우 AZDATA_USERNAME 및 AZDATA_PASSWORD 값도 SQL 관리형 인스턴스를 만드는 데 사용됩니다.

> [!NOTE]
> Azure SQL Managed Instance를 만들면 Azure에 리소스가 등록되지 않습니다. 리소스를 등록하는 단계는 다음 문서에 나와 있습니다. 
> - [Kibana 및 Grafana를 사용하여 로그 및 메트릭 보기](monitor-grafana-kibana.md)
> - [청구 데이터를 Azure에 업로드하고 Azure Portal에서 보기](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Azure Arc에서 인스턴스 보기

인스턴스를 보려면 다음 명령을 사용합니다.

```console
azdata arc sql mi list
```

출력은 다음과 같습니다.

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

AKS, `kubeadm` 또는 OpenShift 등을 사용하는 경우 여기에서 외부 IP 및 포트 번호를 복사하고 Azure Data Studio 또는 SQL Server Management Studio와 같은 SQL Server/Azure SQL 인스턴스에 연결하기 위해 선호하는 도구를 사용하여 연결할 수 있습니다. 그러나 빠른 시작 VM을 사용하는 경우 특별한 지침은 [Azure Arc 지원 SQL Managed Instance에 연결](connect-managed-instance.md) 문서를 참조하세요.


## <a name="next-steps"></a>다음 단계
- [Azure Arc 지원 SQL Managed Instance에 연결하기](connect-managed-instance.md)
- [Azure에 인스턴스를 등록하고 인스턴스에 대한 메트릭 및 로그 업로드](upload-metrics-and-logs-to-azure-monitor.md)
- [Azure Data Studio를 사용하여 Azure SQL Managed Instance 배포](create-sql-managed-instance-azure-data-studio.md)
