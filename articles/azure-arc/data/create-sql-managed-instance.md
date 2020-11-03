---
title: Azure Arc에서 Azure SQL 관리 되는 인스턴스 만들기
description: Azure Arc에서 Azure SQL 관리 되는 인스턴스 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280407"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc에서 Azure SQL 관리 되는 인스턴스 만들기

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 로그인 합니다.

인스턴스를 만들기 전에 아직 로그인 하지 않은 경우 Azure Arc 데이터 컨트롤러에 로그인 합니다.

```console
azdata login
```

그러면 사용자 이름, 암호 및 시스템 네임 스페이스를 묻는 메시지가 표시 됩니다.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Azure SQL Managed Instance 만들기

Sql Managed Instance에서 사용 가능한 만들기 옵션을 보려면 다음 명령을 사용 합니다.
```console
azdata arc sql mi create --help
```

SQL Managed Instance를 만들려면 다음 명령을 사용 합니다.

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

예제:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  이름 길이는 13 자 미만 이어야 하 고 [DNS 명명 규칙](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names) 을 준수 해야 합니다.
>
>  메모리 할당 및 vCore 할당을 지정 하는 경우이 수식을 사용 하 여 생성이 성공적으로 수행 되었는지 확인 합니다. 1 개 vCore 마다 SQL 관리 되는 인스턴스 pod가 실행 되는 Kubernetes 노드에서 4GB 이상의 용량을 사용할 수 있어야 합니다.
>
>  Azure에서 프로비저닝하는 경우 SQL 인스턴스를 만들 때 이름에 대문자를 사용하지 않습니다.
>
>  Kubernetes 클러스터 실행에서 사용 가능한 저장소 클래스를 나열 하려면 `kubectl get storageclass` 


> [!NOTE]
> SQL 인스턴스 만들기를 자동화 하 고 관리자 암호에 대 한 대화형 프롬프트가 표시 되지 않도록 하려면 `AZDATA_USERNAME` `AZDATA_PASSWORD` 명령을 실행 하기 전에 및 환경 변수를 원하는 사용자 이름과 암호로 설정 하면 `azdata arc sql mi create` 됩니다.
> 
>  AZDATA_USERNAME를 사용 하 여 데이터 컨트롤러를 만들고 동일한 터미널 세션에서 AZDATA_PASSWORD 하는 경우 AZDATA_USERNAME 및 AZDATA_PASSWORD 값이 SQL 관리 되는 인스턴스를 만드는 데 사용 됩니다.

> [!NOTE]
> Azure SQL Managed Instance를 만들면 Azure에서 리소스를 등록 하지 않습니다. 리소스를 등록 하는 단계는 다음 문서에 나와 있습니다. 
> - [Kibana 및 Grafana를 사용 하 여 로그 및 메트릭 보기](monitor-grafana-kibana.md)
> - [청구 데이터를 Azure에 업로드 하 고 Azure Portal에서 확인](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Azure Arc에서 인스턴스 보기

인스턴스를 보려면 다음 명령을 사용 합니다.

```console
azdata arc sql mi list
```

출력은 다음과 같습니다.

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

AKS 또는 `kubeadm` OpenShift 등을 사용 하는 경우 여기에서 외부 IP 및 포트 번호를 복사 하 고 즐겨 사용 하는 도구를 사용 하 여 Azure Data Studio 또는 SQL Server Management Studio와 같은 Sql Sever/AZURE sql 인스턴스에 연결할 수 있습니다. 그러나 빠른 시작 VM을 사용 하는 경우 특별 한 지침은 [Azure Arc 사용 SQL Managed Instance에 연결](connect-managed-instance.md) 문서를 참조 하세요.


## <a name="next-steps"></a>다음 단계
- [Azure Arc 사용 SQL Managed Instance에 연결](connect-managed-instance.md)
- [Azure에 인스턴스를 등록 하 고 인스턴스에 대 한 메트릭 및 로그를 업로드 합니다.](upload-metrics-and-logs-to-azure-monitor.md)
- [Azure Data Studio를 사용 하 여 Azure SQL 관리 되는 인스턴스 배포](create-sql-managed-instance-azure-data-studio.md)
