---
title: Kibana 및 Grafana를 사용 하 여 로그 및 메트릭 보기
description: Kibana 및 Grafana를 사용 하 여 로그 및 메트릭 보기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d876862d8f41ab8df646bef051629fd45c4d4601
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90939628"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Kibana 및 Grafana를 사용 하 여 로그 및 메트릭 보기

Kibana 및 Grafana 웹 대시보드는 Azure Arc 지원 데이터 서비스에서 사용되는 Kubernetes 네임스페이스에 대한 통찰력 및 명확성을 위해 제공됩니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="retrieve-the-ip-address-of-your-cluster"></a>클러스터의 IP 주소를 검색 합니다.

대시보드에 액세스 하려면 클러스터의 IP 주소를 검색 해야 합니다. 올바른 IP 주소를 검색 하는 방법은 Kubernetes 배포를 선택한 방법에 따라 달라 집니다. 아래 옵션을 단계별로 실행 하 여 올바른 항목을 찾으십시오.

### <a name="azure-virtual-machine"></a>Azure 가상 머신

공용 IP 주소를 검색하려면 다음 명령을 사용합니다.

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

### <a name="kubeadm-cluster"></a>Kubeadm 클러스터

클러스터 ip 주소를 검색 하려면 다음 명령을 사용 합니다.

```console
kubectl cluster-info
```


### <a name="aks-or-other-load-balanced-cluster"></a>AKS 또는 다른 부하 분산 된 클러스터

AKS 또는 다른 부하 분산 된 클러스터에서 환경을 모니터링 하려면 관리 프록시 서비스의 ip 주소를 가져와야 합니다. **외부 ip** 주소를 검색 하려면 다음 명령을 사용 합니다.

```console
kubectl get svc mgmtproxy-svc-external -n <namespace>

#Example:
#kubectl get svc mgmtproxy-svc-external -n arc
NAME                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)           AGE
mgmtproxy-svc-external   LoadBalancer   10.0.186.28   52.152.148.25   30777:30849/TCP   19h
```

## <a name="additional-firewall-configuration"></a>추가 방화벽 구성

Kibana 및 Grafana 끝점에 액세스 하려면 방화벽에서 포트를 열어야 할 수도 있습니다.

다음은 Azure VM에 대해이 작업을 수행 하는 방법의 예입니다. 스크립트를 사용 하 여 Kubernetes를 배포한 경우이 작업을 수행 해야 합니다.

아래 단계는 Kibana 및 Grafana 끝점에 대 한 NSG 규칙을 만드는 방법을 강조 표시 합니다.

### <a name="find-the-name-of-the-nsg"></a>NSG의 이름 찾기

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>NSG 규칙 추가

NSG의 이름이 있으면 다음 명령을 사용 하 여 규칙을 추가할 수 있습니다.

```console
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```

## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Azure Arc에서 Azure SQL 관리 되는 인스턴스 모니터링

이제 IP 주소를 사용 하 고 포트를 노출 했으므로 Grafana 및 Kibana에 액세스할 수 있어야 합니다.

> [!NOTE]
>  사용자 이름 및 암호를 입력 하 라는 메시지가 표시 되 면 Azure Arc 데이터 컨트롤러를 만들 때 제공한 사용자 이름과 암호를 입력 합니다.

> [!NOTE]
>  미리 보기에 사용 되는 인증서가 자체 서명 된 인증서 이므로 인증서 경고가 표시 됩니다.

다음 URL 패턴을 사용 하 여 Azure SQL 관리 되는 인스턴스의 로깅 및 모니터링 대시보드에 액세스 합니다.

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

관련 대시보드는 다음과 같습니다.

* "Azure SQL 관리 되는 인스턴스 메트릭"
* "호스트 노드 메트릭"
* "Host Pod 메트릭"

## <a name="monitor-azure-database-for-postgresql-hyperscale---azure-arc"></a>모니터링 Azure Database for PostgreSQL Hyperscale-Azure Arc

다음 URL 패턴을 사용 하 여 PostgreSQL Hyperscale의 로깅 및 모니터링 대시보드에 액세스 합니다.

```html
https://<external-ip-from-above>:30777/grafana
https://<external-ip-from-above>:30777/kibana
```

관련 대시보드는 다음과 같습니다.

* "Postgres 메트릭"
* "Postgres 테이블 메트릭"
* "호스트 노드 메트릭"
* "Host Pod 메트릭"

## <a name="next-steps"></a>다음 단계
- [Azure Monitor에 메트릭 및 로그 업로드를 시도 합니다](upload-metrics-and-logs-to-azure-monitor.md) .
- Grafana에 대해 읽어 보세요.
   - [시작](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grafana 기본 사항](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Grafana 자습서](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Kibana에 대해 읽기
   - [소개](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana 가이드](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Kibana에서 데이터 시각화를 사용 하 여 대시보드 드릴 다운 소개](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana 대시보드를 빌드하는 방법](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

