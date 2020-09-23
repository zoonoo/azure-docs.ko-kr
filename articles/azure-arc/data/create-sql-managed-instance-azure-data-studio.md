---
title: Azure Data Studio를 사용 하 여 Azure SQL 관리 되는 인스턴스 만들기
description: Azure Data Studio를 사용 하 여 Azure SQL 관리 되는 인스턴스 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 989496885445a8a0a8d3bbc1a789975a2875c6e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939916"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>SQL 관리 되는 인스턴스 만들기-Azure Data Studio를 사용 하 여 Azure Arc

이 문서에서는 Azure Data Studio를 사용 하 여 Azure SQL 관리 되는 인스턴스 (Azure Arc)를 설치 하는 단계를 안내 합니다.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 로그인 합니다.

인스턴스를 만들기 전에 아직 로그인 하지 않은 경우 Azure Arc 데이터 컨트롤러에 로그인 합니다.

```console
azdata login
```

그런 다음 데이터 컨트롤러가 생성 되는 네임 스페이스, 사용자 이름 및 컨트롤러에 로그인 하는 데 사용할 암호를 묻는 메시지가 표시 됩니다.  

> 네임 스페이스의 유효성을 검사 해야 하는 경우를 실행 ```kubectl get pods -A``` 하 여 클러스터에 있는 모든 네임 스페이스의 목록을 가져올 수 있습니다.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc에서 Azure SQL 관리 되는 인스턴스 만들기

- 시작 Azure Data Studio
- 연결 탭에서 왼쪽 위에 있는 세 개의 점을 클릭 하 고 "새 배포"를 선택 합니다.
- 배포 옵션에서 **AZURE SQL 관리 되는 인스턴스-Azure Arc** 를 선택 합니다. 
  > **참고:** Azdata CLI가 현재 설치 되어 있지 않으면 여기에 설치 하 라는 메시지가 표시 될 수 있습니다.
- 개인 정보 및 사용 조건에 동의 하 고 맨 아래에서 **선택** 을 클릭 합니다.



- Azure SQL 관리 되는 인스턴스 배포-Azure Arc 블레이드에서 다음 정보를 입력 합니다.
  - SQL Server 인스턴스의 이름을 입력 하십시오.
  - SQL Server 인스턴스에 대 한 암호를 입력 하 고 확인 합니다.
  - 데이터에 적절 한 저장소 클래스를 선택 합니다.
  - 로그에 적절 한 저장소 클래스를 선택 합니다.

- **배포** 단추를 클릭 합니다.

- 이를 통해 데이터 컨트롤러에서 azure SQL 관리 되는 인스턴스 (Azure Arc) 만들기를 시작 해야 합니다.

- 몇 분 후 만들기가 성공적으로 완료 됩니다.

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Azure SQL 관리 되는 인스턴스에 연결-Azure Data Studio에서 Azure Arc

- 데이터 컨트롤러에 대 한 네임 스페이스, 사용자 이름 및 암호를 제공 하 여 Azure Arc 데이터 컨트롤러에 로그인 합니다. 
```console
azdata login
```

- 다음 명령을 사용 하 여 프로 비전 된 모든 Azure SQL 관리 되는 인스턴스를 확인 합니다.

```console
azdata arc sql mi list
```

출력은 다음과 같습니다. 여기에서 포트 번호를 포함 하 여 ServerEndpoint를 복사 합니다.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Azure Data Studio의 **연결** 탭에서 **서버** 보기의 **새 연결** 을 클릭 합니다.
- **연결** 블레이드에서 Serverendpoint를 서버 텍스트 상자에 붙여넣습니다.
- 인증 유형으로 **SQL 로그인** 을 선택 합니다.
- 사용자 이름으로 *sa* 입력
- 계정에 대 한 암호 입력 `sa`
- 필요에 따라 연결할 특정 데이터베이스 이름을 입력 합니다.
- 필요에 따라 새 서버 그룹을 적절 하 게 선택/추가 합니다.
- **연결** 을 선택 하 여 azure SQL 관리 되는 인스턴스에 연결-azure Arc




## <a name="next-steps"></a>다음 단계

이제 [SQL 인스턴스를 모니터링](monitor-grafana-kibana.md) 해 보세요.
