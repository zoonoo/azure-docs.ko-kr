---
title: Azure Data Studio를 사용 하 여 PostgreSQL 인스턴스 관리
description: Azure Data Studio를 사용 하 여 PostgreSQL 인스턴스 관리
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fc0ad45f575f9190f15b61acdf476c716b7f1638
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940781"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Data Studio를 사용 하 여 Azure Arc enabled PostgreSQL Hyperscale 서버 그룹 관리


이 문서에서는 다음과 같이 방법을 설명합니다.
- 개요, 연결 문자열, 속성, Resource Health 등의 대시보드 보기를 사용 하 여 PostgreSQL 인스턴스를 관리 합니다.
- 데이터 및 스키마 작업

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

- [Azdata, Azure Data Studio 및 Azure CLI 설치](install-client-tools.md)
- **Azure 데이터 CLI** 및 **Azure Arc** 및 **PostgreSQL** 확장 Azure Data Studio에 설치
- [Azure Arc 데이터 컨트롤러](create-data-controller-using-azdata.md) 만들기
- 시작 Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 연결

Azure Data Studio에서 노드 **Azure Arc 컨트롤러** 를 확장 하 고 **연결 컨트롤러** 단추를 선택 합니다.

Azure 데이터 컨트롤러에 대 한 연결 정보를 입력 합니다.

- **컨트롤러 URL:**

    Kubernetes의 컨트롤러에 연결할 URL입니다. `https://<IP_address_of_the_controller>:<Kubernetes_port.`예를 들면 다음과 같은 형식으로 입력 됩니다.

    ```console
    https://12.345.67.890:30080
    ```
- **사용자 이름:**

    컨트롤러에 연결 하는 데 사용 하는 사용자 계정의 이름입니다. 를 실행할 때 일반적으로 사용 하는 이름을 사용 `azdata login` 합니다. 일반적으로 psql에서 PostgreSQL 데이터베이스 엔진에 연결 하는 데 사용 하는 PostgreSQL 사용자의 이름이 아닙니다.
- **암호:** 컨트롤러에 연결 하는 데 사용 하는 사용자 계정의 암호입니다.


Azure data studio는 Arc 데이터 컨트롤러를 표시 합니다. 확장 하 고 관리 하는 PostgreSQL 인스턴스의 목록을 표시 합니다.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹 관리

관리할 PostgreSQL 인스턴스를 마우스 오른쪽 단추로 클릭 하 고 [관리]를 선택 합니다.

PostgreSQL 대시보드 보기:

이 기능은 해당 창의 왼쪽에 나열 된 여러 대시보드를 제공 합니다.

- **개요:** 사용자 인스턴스에 대 한 요약 정보 (예: 이름, Azure 구독 ID, 구성, 데이터베이스 엔진의 버전, Grafana 및 Kibana에 대 한 끝점)를 표시 합니다.
- **연결 문자열:** Psql, Node.js, PHP, Ruby ...와 같이 PostgreSQL 인스턴스에 연결 하는 데 필요할 수 있는 다양 한 연결 문자열을 표시 합니다.
- **속성:** 섀도 리소스에 대 한 PostgreSQL admin 사용자 이름, 연결 된 리소스 그룹 등의 다양 한 속성을 표시 합니다.
- **문제 진단 및 해결:** 문제 해결 노트북을 확장할 때 인스턴스 문제를 해결 하는 데 도움이 되는 다양 한 리소스를 찾을 수 있는 방문 페이지입니다.
- **새 지원 요청:** 지원 서비스에서 공개 미리 보기 알림을 시작 하는 데 도움을 요청할 수 있는 방문 페이지입니다.

## <a name="work-with-your-data-and-schema"></a>데이터 및 스키마 작업

Azure Data Studio 창의 왼쪽에서 노드 **서버**를 확장 합니다.

[연결 추가]를 선택 하 고 PostgreSQL 인스턴스에 대 한 연결 세부 정보를 입력 합니다.
- **연결 형식:** PostgreSQL
- **서버 이름:** PostgreSQL 인스턴스의 이름을 입력 합니다. 예: postgres01
- **인증 유형:** 암호
- **사용자 이름:** 예를 들어 standard/default PostgreSQL admin 사용자 이름을 사용할 수 있습니다. 이 필드는 대/소문자를 구분 합니다.
- **암호:** 명령 출력의 psql 연결 문자열에서 PostgreSQL username의 암호를 찾을 수 있습니다. `azdata postgres server endpoint -n postgres01`
- **데이터베이스 이름:** 연결 하려는 데이터베이스의 이름을 설정 합니다. __기본값으로__ 설정 되도록 할 수 있습니다.
- **서버 그룹:** __기본값으로__ 설정 되도록 할 수 있습니다.
- **Name (선택 사항):** 비워 둘 수 있습니다.
- **고급**
    - **호스트 IP 주소:** Kubernetes 클러스터의 공용 IP 주소입니다.
    - **포트:** PostgreSQL 인스턴스가 수신 대기 중인 포트입니다. 이 포트는 명령 출력의 psql 연결 문자열 끝에서 찾을 수 있습니다 `azdata postgres server endpoint -n postgres01` . Kubernetes에서 수신 대기 하 고 Azure Data Studio의 Azure 데이터 컨트롤러에 연결할 때 입력 한 포트 30080이 아닙니다.
    - **기타 매개 변수:** 이러한 값은 자체 명시적 이어야 하며, 표시 되는 기본/빈 값을 사용할 수 있습니다.

**[확인]을 선택 하 고 [연결]** 을 선택 하 여 서버에 연결 합니다.

연결 되 면 몇 가지 환경을 사용할 수 있습니다.
- **새 쿼리**
- **새 노트북**
- **서버 표시를 확장 하 고 데이터베이스 내 개체를 찾아보거나 작업 합니다.**
- **...**

## <a name="next-step"></a>다음 단계
[서버 그룹 모니터링](monitor-grafana-kibana.md)
