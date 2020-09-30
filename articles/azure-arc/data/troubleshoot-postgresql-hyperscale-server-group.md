---
title: PostgreSQL Hyperscale 서버 그룹 문제 해결
description: Jupyter Notebook를 사용 하 여 PostgreSQL Hyperscale 서버 그룹 문제 해결
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 8d1c9027b6a9a7b295ce83e26281832beca1bc33
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531958"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>PostgreSQL Hyperscale 서버 그룹 문제 해결
이 문서에서는 서버 그룹 문제를 해결 하는 데 사용할 수 있는 몇 가지 기법을 설명 합니다. 이 문서 외에도 [Kibana](monitor-grafana-kibana.md) 를 사용 하 여 로그를 검색 하거나 [Grafana](monitor-grafana-kibana.md) 를 사용 하 여 서버 그룹에 대 한 메트릭을 시각화 하는 방법을 읽을 수 있습니다. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Azdata 명령 실행에 대 한 자세한 정보 보기
실행 하는 모든 azdata 명령에 **--debug** 매개 변수를 추가할 수 있습니다. 이렇게 하면 콘솔에 해당 명령의 실행에 대 한 추가 정보가 표시 됩니다. 해당 명령의 동작을 이해 하는 데 도움이 되는 세부 정보를 확인 하는 것이 유용 합니다.
예를 들어 다음을 실행할 수 있습니다.
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

또는
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

또한 azdata 명령에 대해 매개 변수--help를 사용 하 여 특정 명령에 대 한 일부 도움말, 매개 변수 목록을 표시할 수 있습니다. 다음은 그 예입니다.
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>데이터 컨트롤러 및 서버 그룹의 로그 수집
[Azure Arc 사용 데이터 서비스에 대 한 로그 가져오기](troubleshooting-get-logs.md) 에 대 한 문서를 참조 하십시오.



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Azure Data Studio에서 Jupyter 노트북을 사용 하 여 대화형 문제 해결
Notebook은 markdown 콘텐츠를 포함하여 수행할 작업 및 방법을 설명하는 절차를 문서화할 수 있습니다. 또한 절차를 자동화하는 실행 코드를 제공할 수 있습니다.  이 패턴은 표준 운영 절차에서 문제 해결 가이드에 이르기까지 모든 항목에 유용합니다.

예를 들어 Azure Data Studio를 사용 하는 데 몇 가지 문제가 있을 수 있는 PostgreSQL Hyperscale server 그룹의 문제를 해결 해 보겠습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>도구 설치

`kubectl` `azdata` Azure Data Studio에서 노트북을 실행 하는 데 사용 하는 클라이언트 컴퓨터에 Azure Data Studio 및를 설치 합니다. 이렇게 하려면 [클라이언트 도구 설치](install-client-tools.md) 의 지침을 따르세요.

### <a name="update-the-path-environment-variable"></a>PATH 환경 변수 업데이트

이러한 도구는이 클라이언트 컴퓨터의 어디에서 나 호출할 수 있는지 확인 합니다. 예를 들어 Windows 클라이언트 컴퓨터에서 PATH 시스템 환경 변수를 업데이트 하 고 kubectl를 설치한 폴더를 추가 합니다.

### <a name="sign-in-with-azdata"></a>로그인 `azdata`

이 클라이언트 컴퓨터에서 Arc 데이터 컨트롤러에 로그인 하 고 Azure Data Studio를 시작 합니다. 이렇게 하려면 다음과 같은 명령을 실행 합니다.

```console
azdata login --endpoint https://<IP address>:<port>
```

을 `<IP address>` Kubernetes 클러스터의 IP 주소로 바꾸고, `<port>` Kubernetes가 수신 대기 하는 포트를 사용 합니다. 사용자 이름 및 암호를 입력 하 라는 메시지가 표시 됩니다. 자세한 내용을 보려면 다음을 실행 하세요. _

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Kubectl를 사용 하 여 Kubernetes 클러스터에 로그인

이렇게 하려면 [이](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) 블로그 게시물에 제공 된 예제 명령을 사용 하는 것이 좋습니다.
다음과 같은 명령을 실행 합니다.

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>문제 해결 노트북

Azure Data Studio를 시작 하 고 문제 해결 노트북을 엽니다. 

[033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) 에 설명 된 단계를 구현 합니다.

1. Arc 데이터 컨트롤러에 연결
2. Postgres 인스턴스를 마우스 오른쪽 단추로 선택 하 고 **[관리]** 를 선택 합니다.
3. **[문제 진단 및 해결] 대시보드** 를 선택 합니다.
4. **[문제 해결] 링크** 를 선택 합니다.

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio-오픈 PostgreSQL 문제 해결 노트북":::

**TSG100-Azure Arc Enabled PostgreSQL Hyperscale 노트북** 이 열립니다. :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio-오픈 PostgreSQL 문제 해결 노트북"::: 합니다.

#### <a name="run-the-scripts"></a>스크립트 실행
맨 위에 있는 ' 모두 실행 ' 단추를 선택 하 여 노트북을 한꺼번에 실행 하거나 단계별로 실행 하 고 각 코드 셀을 하나씩 실행할 수 있습니다.

잠재적 문제에 대 한 코드 셀 실행의 출력을 확인 합니다.

일반적인 문제를 인식 하 고 해결 하는 방법에 대 한 자세한 내용은 시간 경과에 따라 노트북에 추가 합니다.

## <a name="next-step"></a>다음 단계
- [Azure Arc 사용 데이터 서비스에 대 한 로그 가져오기](troubleshooting-get-logs.md) 에 대 한 자세한 정보
- [Kibana를 사용 하 여 로그 검색](monitor-grafana-kibana.md) 에 대 한 자세한 정보
- [Grafana를 사용한 모니터링](monitor-grafana-kibana.md) 에 대해 읽기
- 나만의 노트북 만들기
