---
title: 대시보드 Azure Data Studio
description: 대시보드 Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107575"
---
# <a name="azure-data-studio-dashboards"></a>대시보드 Azure Data Studio

[Azure Data Studio](/sql/azure-data-studio/what-is) 는 Azure Arc 리소스에 대 한 정보를 보기 위한 Azure Portal와 유사한 환경을 제공 합니다.  이러한 보기를 **대시보드** 라고 하며, Azure Portal에서 지정 된 리소스에 대해 볼 수 있는 것과 유사한 레이아웃 및 옵션을 제공 하지만, Azure에 사용할 수 있는 연결이 없는 경우 사용자 환경에서 로컬로 해당 정보를 볼 수 있는 유연성을 제공 합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>데이터 컨트롤러에 연결

### <a name="prerequisites"></a>사전 요구 사항

- 다운로드 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- Azure Arc 확장이 설치 되었습니다.

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>데이터 컨트롤러 서버 API 끝점 URL 확인

먼저 Azure Data Studio를 데이터 컨트롤러 서비스 API 끝점 URL에 연결 해야 합니다.

이 끝점을 가져오려면 다음 명령을 실행할 수 있습니다.

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

다음과 같은 출력이 표시 됩니다.

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

LoadBalancer 유형을 사용 하는 경우 외부 IP 주소와 포트 번호를 복사 합니다. NodePort를 사용 하는 경우 Kubernetes API 서버의 IP 주소와 포트 열 아래에 나열 된 포트 번호를 사용 합니다.

이제 다음과 같이이 정보를 결합 하 여 끝점에 대 한 URL을 생성 합니다.

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

다음 단계에서 사용할 때 사용 하는 IP 주소를 기록해 둡니다.

### <a name="connect"></a>연결

1. Azure Data Studio 열기

1. 왼쪽의 **연결** 탭을 선택 합니다.

아래쪽을 향해 **Azure Arc 컨트롤러**라는 패널을 확장 합니다.

+ 아이콘을 클릭 하 여 새 데이터 컨트롤러 연결을 추가 합니다.

명령 팔레트의 화면 위쪽에서 1 단계에서 만든 URL을 입력 하 고 enter 키를 클릭 합니다.
데이터 컨트롤러에 대 한 사용자 이름을 입력 합니다.  이 값은 데이터 컨트롤러 배포 중에 전달 된 사용자 이름입니다.  Enter 키를 누릅니다.
데이터 컨트롤러에 대 한 암호를 입력 합니다.  이는 데이터 컨트롤러 배포 중에 전달 된 암호 값입니다. Enter 키를 누릅니다.

이제 데이터 컨트롤러에 연결 되었으므로 데이터 컨트롤러 및 모든 SQL 관리 되는 인스턴스 또는 PostgreSQL Hyperscale server 그룹 리소스에 대 한 대시보드를 볼 수 있습니다.

## <a name="view-the-data-controller-dashboard"></a>데이터 컨트롤러 대시보드 보기

**Arc controller** 확장 가능 패널의 연결 패널에서 데이터 컨트롤러를 마우스 오른쪽 단추로 클릭 하 고 **관리**를 선택 합니다.

여기에서 이름, 지역, 연결 모드, 리소스 그룹, 구독, 컨트롤러 끝점 및 네임 스페이스와 같은 데이터 컨트롤러 리소스에 대 한 세부 정보를 볼 수 있습니다.  데이터 컨트롤러에서 관리 하는 모든 관리 되는 데이터베이스 리소스의 목록도 볼 수 있습니다.

레이아웃은 Azure Portal에서 볼 수 있는 것과 비슷합니다.

' + 새 인스턴스 ' 단추를 클릭 하 여 SQL 관리 되는 인스턴스 또는 PostgreSQL Hyperscale 서버 그룹 만들기를 편리 하 게 시작할 수 있습니다.

Azure Portal에서 열기 단추를 클릭 하 여이 데이터 컨트롤러에 대 한 컨텍스트에서 Azure Portal를 열 수도 있습니다.

## <a name="view-the-sql-managed-instance-dashboards"></a>SQL 관리 되는 인스턴스 대시보드 보기

일부 SQL 관리 되는 인스턴스를 만든 경우 해당 인스턴스를 관리 하는 데이터 컨트롤러 아래에 있는 Azure Data controller 확장 가능 패널의 연결 패널에 나열 된 것을 볼 수 있습니다.

지정 된 인스턴스에 대 한 SQL 관리 되는 인스턴스 대시보드를 보려면 인스턴스를 마우스 오른쪽 단추로 클릭 하 고 관리를 선택 합니다.

연결 패널이 오른쪽에 표시 되 고 해당 SQL 인스턴스에 연결 하는 데 사용할 로그인/암호를 묻는 메시지가 표시 됩니다. 연결 정보를 알고 있는 경우 해당 정보를 입력 하 고 연결을 클릭 합니다.  잘 모르는 경우 취소를 클릭할 수 있습니다.  어느 쪽이 든 연결 패널을 닫으면 대시보드로 이동 합니다.

개요 탭에서 SQL 관리 되는 인스턴스 (예: 리소스 그룹, 데이터 컨트롤러, 구독 ID, 상태, 지역 등)에 대 한 세부 정보를 볼 수 있습니다.  Grafana 또는 Kibana 대시보드로 이동 하 여 해당 SQL 관리 되는 인스턴스로 이동 하는 링크를 볼 수도 있습니다.

SQL 관리 인스턴스에 연결할 수 있는 경우 여기에서 추가 정보를 볼 수 있습니다.

여기에서 SQL 관리 되는 인스턴스를 삭제 하거나 Azure Portal을 열어 Azure Portal에서 SQL 관리 되는 인스턴스를 볼 수 있습니다.

왼쪽의 연결 문자열 탭을 클릭 하면 해당 SQL 관리 되는 인스턴스에 대해 미리 생성 된 연결 문자열의 목록을 확인 하 여 다양 한 다른 응용 프로그램 또는 코드에 쉽게 복사 하 고 붙여 넣을 수 있습니다.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>PostgreSQL Hyperscale 서버 그룹 대시보드 보기

PostgreSQL 하이퍼 확장 서버 그룹을 만든 경우 해당 그룹을 관리 하는 데이터 컨트롤러 아래의 Azure Data controller 확장 가능 패널에서 연결 패널에 나열 된 것을 볼 수 있습니다.

지정 된 서버 그룹에 대 한 PostgreSQL Hyperscale server 그룹 대시보드를 보려면 서버 그룹을 마우스 오른쪽 단추로 클릭 하 고 관리를 선택 합니다.

개요 탭에서 리소스 그룹, 데이터 컨트롤러, 구독 ID, 상태, 지역 등과 같은 서버 그룹에 대 한 세부 정보를 볼 수 있습니다.  또한 클릭 하 여 해당 서버 그룹에 대 한 컨텍스트에서 Grafana 또는 Kibana 대시보드로 이동할 수 있는 링크를 볼 수 있습니다.

여기에서 서버 그룹을 삭제 하거나 Azure Portal을 열어 Azure Portal에서 서버 그룹을 볼 수 있습니다.

왼쪽에 있는 연결 문자열 탭을 클릭 하면 해당 서버 그룹에 대해 미리 생성 된 연결 문자열의 목록을 확인 하 여 다양 한 다른 응용 프로그램 또는 코드에 쉽게 복사 하 고 붙여 넣을 수 있습니다.

왼쪽의 속성 탭을 클릭 하면 추가 세부 정보를 볼 수 있습니다.

왼쪽의 리소스 상태 탭을 클릭 하면 해당 서버 그룹의 현재 상위 수준 상태를 볼 수 있습니다.

왼쪽의 문제 진단 및 해결 탭을 클릭 하면 PostgreSQL 문제 해결 노트북을 시작할 수 있습니다.

왼쪽에서 새 지원 요청 탭을 클릭 하면 서버 그룹에 대 한 컨텍스트에서 Azure Portal를 시작 하 고 여기에서 Azure 지원 요청을 만들 수 있습니다.