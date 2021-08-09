---
title: Azure Data Studio를 사용하여 Azure SQL Managed Instance 만들기
description: Azure Data Studio를 사용하여 Azure SQL Managed Instance 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: rothja
ms.author: jroth
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1eef749ee28f93948eead10bd38679f522280244
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109838054"
---
# <a name="create-sql-managed-iinstance---azure-arc-using-azure-data-studio"></a>Azure Data Studio를 사용하여 SQL Managed Instance - Azure Arc 만들기

이 문서에서는 Azure Data Studio를 사용하여 Azure SQL Managed Instance - Azure Arc를 설치하는 단계를 안내합니다.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에 로그인합니다.

인스턴스를 만들기 전에 아직 로그인하지 않은 경우 Azure Arc 데이터 컨트롤러에 로그인합니다.

```console
azdata login
```

그러면 컨트롤러에 로그인하기 위해 데이터 컨트롤러를 만든 네임스페이스, 사용자 이름, 암호를 입력하라는 프롬프트가 표시됩니다.  

> 네임스페이스의 유효성을 검사해야 하는 경우 ```kubectl get pods -A```를 실행하여 클러스터에 있는 모든 네임스페이스의 목록을 가져올 수 있습니다.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Azure Arc에서 Azure SQL Managed Instance 만들기

- Azure Data Studio 시작
- 연결 탭에서 왼쪽 위에 있는 점 세 개를 클릭하고 “새 배포”를 선택합니다.
- 배포 옵션에서 **Azure SQL Managed Instance - Azure Arc** 를 선택합니다. 
  > [!NOTE]
  > 현재 설치되어 있지 않으면 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]를 설치할지 묻는 프롬프트가 표시될 수 있습니다.
- 개인정보취급방침 및 사용 조건에 동의하고 맨 아래에서 **선택** 을 클릭합니다.



- Azure SQL Managed Instance - Azure Arc 배포 블레이드에서 다음 정보를 입력합니다.
  - SQL Server 인스턴스의 이름 입력
  - SQL Server 인스턴스에 대한 암호 입력 및 확인
  - 데이터에 적절한 스토리지 클래스 선택
  - 로그에 적절한 스토리지 클래스 선택

- **배포** 단추를 클릭합니다.

- 그러면 데이터 컨트롤러에서 Azure SQL Managed Instance - Azure Arc 만들기가 시작됩니다.

- 몇 분 후에 생성이 완료됩니다.

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Azure Data Studio에서 Azure SQL Managed Instance - Azure Arc 연결

- 데이터 컨트롤러에 대한 네임스페이스, 사용자 이름 및 암호를 제공하여 Azure Arc 데이터 컨트롤러에 로그인합니다. 
```console
azdata login
```

- 다음 명령을 사용하여 프로비저닝된 모든 Azure SQL Managed Instance를 봅니다.

```console
azdata arc sql mi list
```

출력은 다음과 같아야 합니다. 여기에서 ServerEndpoint(포트 번호 포함)를 복사합니다.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Azure Data Studio의 **연결** 탭에서 **서버** 보기의 **새 연결** 을 클릭합니다.
- **연결** 블레이드에서 ServerEndpoint를 서버 텍스트 상자에 붙여넣습니다.
- 인증 유형으로 **SQL 로그인** 을 선택합니다.
- 사용자 이름으로 *sa* 를 입력합니다.
- `sa` 계정의 암호를 입력합니다.
- 필요에 따라 연결할 특정 데이터베이스 이름을 입력합니다.
- 필요에 따라 새 서버 그룹을 적절하게 선택/추가합니다.
- **연결** 을 선택하여 Azure SQL Managed Instance - Azure Arc에 연결합니다.




## <a name="next-steps"></a>다음 단계

이제 [SQL 인스턴스를 모니터링](monitor-grafana-kibana.md)을 시도해보세요.
