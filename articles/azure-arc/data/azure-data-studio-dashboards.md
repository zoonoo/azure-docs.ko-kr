---
title: Azure Data Studio 대시보드
description: Azure Data Studio 대시보드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 94e7b6b351d13a85a516b4a4bc6c54c31754bc12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536405"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio 대시보드

[Azure Data Studio](/sql/azure-data-studio/what-is)는 Azure Arc 리소스에 대한 정보를 볼 수 있는 Azure Portal과 유사한 환경을 제공합니다.  이러한 보기를 **대시보드** 라고 하며, Azure Portal에서 지정된 리소스에 대해 볼 수 있는 것과 유사한 레이아웃 및 옵션을 제공하지만, Azure에 사용할 수 있는 연결이 없는 경우 사용자 환경에서 로컬로 해당 정보를 볼 수 있는 유연성을 제공합니다.


## <a name="connecting-to-a-data-controller"></a>데이터 컨트롤러에 연결

### <a name="prerequisites"></a>필수 구성 요소

- [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) 다운로드
- Azure Arc 확장이 설치됨

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>데이터 컨트롤러 서버 API 엔드포인트 URL 확인

먼저 Azure Data Studio를 데이터 컨트롤러 서비스 API 엔드포인트 URL에 연결해야 합니다.

이 엔드포인트를 가져오려면 다음 명령을 실행할 수 있습니다.

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

다음과 같은 출력이 표시됩니다.

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

LoadBalancer 유형을 사용하는 경우 외부 IP 주소와 포트 번호를 복사합니다. NodePort를 사용하는 경우 Kubernetes API 서버의 IP 주소와 PORT(S) 열 아래에 나열된 포트 번호를 사용합니다.

이제 다음과 같이 이 정보를 결합하여 엔드포인트에 대한 URL을 생성합니다.

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

다음 단계에서 사용할 IP 주소를 기록해 둡니다.

### <a name="connect"></a>연결

1. Azure Data Studio 열기

1. 왼쪽의 **연결** 탭 선택

아래쪽으로 **Azure Arc 컨트롤러** 라는 패널을 확장합니다.

\+ 아이콘을 클릭하여 새 데이터 컨트롤러 연결을 추가합니다.

명령 팔레트의 화면 위쪽에 1단계에서 생성한 URL을 입력하고 Enter 키를 누릅니다.
데이터 컨트롤러에 대한 사용자 이름을 입력합니다.  이 값은 데이터 컨트롤러 배포 중에 전달된 사용자 이름입니다.  Enter 키를 누릅니다.
데이터 컨트롤러에 대한 암호를 입력합니다.  이 값은 데이터 컨트롤러 배포 중에 전달된 암호입니다. Enter 키를 누릅니다.

이제 데이터 컨트롤러에 연결되었으므로 데이터 컨트롤러 및 모든 SQL 관리형 인스턴스 또는 PostgreSQL 하이퍼스케일 서버 그룹 리소스에 대한 대시보드를 볼 수 있습니다.

## <a name="view-the-data-controller-dashboard"></a>데이터 컨트롤러 대시보드 보기

**Arc 컨트롤러** 확장 가능 패널의 연결 패널에서 데이터 컨트롤러를 마우스 오른쪽 단추로 클릭하고 **관리** 를 선택합니다.

여기에서 이름, 지역, 연결 모드, 리소스 그룹, 구독, 컨트롤러 엔드포인트 및 네임 스페이스와 같은 데이터 컨트롤러 리소스에 대한 세부 정보를 볼 수 있습니다.  데이터 컨트롤러에서 관리하는 모든 관리 데이터베이스 리소스 목록도 볼 수 있습니다.

레이아웃은 Azure Portal의 레이아웃과 비슷하다는 것을 알 수 있습니다.

간편하게 + 새 인스턴스 단추를 클릭하여 SQL 관리형 인스턴스 또는 PostgreSQL 하이퍼스케일 서버 그룹 만들기를 시작할 수 있습니다.

Azure Portal에서 열기 단추를 클릭하여 이 데이터 컨트롤러에 대한 컨텍스트에서 Azure Portal를 열 수도 있습니다.

## <a name="view-the-sql-managed-instance-dashboards"></a>SQL 관리형 인스턴스 대시보드 보기

일부 SQL 관리형 인스턴스를 만든 경우 해당 인스턴스를 관리하는 데이터 컨트롤러 아래에 있는 Azure 데이터 컨트롤러 확장 가능 패널의 연결 패널에 인스턴스가 나열되는 것을 볼 수 있습니다.

지정된 인스턴스에 대한 SQL 관리형 인스턴스 대시보드를 보려면 인스턴스를 마우스 오른쪽 단추로 클릭하고 관리를 선택합니다.

연결 패널이 오른쪽에 표시되고 해당 SQL 인스턴스에 연결하는 데 사용할 로그인/암호를 묻는 메시지가 표시됩니다. 연결 정보를 알고 있는 경우 해당 정보를 입력하고 연결을 클릭합니다.  모르는 경우 취소를 클릭할 수 있습니다.  어느 쪽이든 연결 패널을 닫으면 대시보드로 이동합니다.

개요 탭에서 리소스 그룹, 데이터 컨트롤러, 구독 ID, 상태, 지역 등 SQL 관리형 인스턴스에 대한 세부 정보를 볼 수 있습니다.  해당 SQL 관리형 인스턴스에 대한 컨텍스트에서 Grafana 또는 Kibana 대시 보드로 이동할 수 있는 링크도 볼 수 있습니다.

SQL 관리형 인스턴스에 연결할 수 있는 경우 여기에서 추가 정보를 볼 수 있습니다.

여기에서 SQL 관리형인스턴스를 삭제하거나 Azure Portal을 열어 Azure Portal에서 SQL 관리형 인스턴스를 볼 수 있습니다.

왼쪽의 연결 문자열 탭을 클릭하면 해당 SQL 관리형 인스턴스에 대해 미리 생성된 연결 문자열 목록을 볼 수 있으므로 다양한 다른 애플리케이션 또는 코드에 쉽게 복사하고 붙여넣을 수 있습니다.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>PostgreSQL 하이퍼스케일 서버 그룹 대시보드 보기

일부 PostgreSQL 하이퍼스케일 서버 그룹을 만든 경우 해당 인스턴스를 관리하는 데이터 컨트롤러 아래에 있는 Azure 데이터 컨트롤러 확장 가능 패널의 연결 패널에 인스턴스가 나열되는 것을 볼 수 있습니다.

지정된 서버 그룹에 대한 PostgreSQL 하이퍼스케일 서버 그룹 대시보드를 보려면 서버 그룹을 마우스 오른쪽 단추로 클릭하고 관리를 선택합니다.

개요 탭에서 리소스 그룹, 데이터 컨트롤러, 구독 ID, 상태, 지역 등 서버 그룹에 대한 세부 정보를 볼 수 있습니다.  해당 서버 그룹에 대한 컨텍스트에서 Grafana 또는 Kibana 대시 보드로 이동할 수 있는 링크도 볼 수 있습니다.

여기에서 서버 그룹을 삭제하거나 Azure Portal을 열어 Azure Portal에서 서버 그룹을 볼 수 있습니다.

왼쪽의 연결 문자열 탭을 클릭하면 해당 서버 그룹에 대해 미리 생성된 연결 문자열 목록을 볼 수 있으므로 다양한 다른 애플리케이션 또는 코드에 쉽게 복사하고 붙여넣을 수 있습니다.

왼쪽의 속성 탭을 클릭하면 추가 세부 정보를 볼 수 있습니다.

왼쪽의 리소스 상태 탭을 클릭하면 해당 서버 그룹의 현재 상위 수준 상태를 볼 수 있습니다.

왼쪽의 문제 진단 및 해결 탭을 클릭하면 PostgreSQL 문제 해결 노트북을 시작할 수 있습니다.

왼쪽의 새 지원 요청 탭을 클릭하면 서버 그룹에 대한 컨텍스트에서 Azure Portal을 시작하고 거기에서 Azure 지원 요청을 만들 수 있습니다.