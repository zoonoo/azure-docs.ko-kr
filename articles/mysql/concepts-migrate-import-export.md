---
title: "MySQL용 Azure 데이터베이스의 가져오기 및 내보내기 | Microsoft Docs"
description: "MySQL용 Azure 데이터베이스의 데이터베이스 내보내기 및 가져오기 소개"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 582f83e3e80d855049abece42726748fcd113ab7
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="migrate-your-mysql-database-using-import-and-export"></a>가져오기 및 내보내기를 사용하여 MySQL Database 마이그레이션
이 자습서에서는 MySQL Workbench를 사용하여 Azure MySQL 데이터베이스에서 데이터를 가져오고 내보내는 가장 일반적인 방법을 보여 줍니다. 

## <a name="before-you-begin"></a>시작하기 전에
이 방법 가이드를 단계별로 실행하려면 다음을 수행해야 합니다.
- MySQL용 Azure 데이터베이스 서버 [첫 번째 Azure MySQL Database 디자인](quickstart-create-mysql-server-database-using-azure-portal.md)
- MySQL Workbench [MySQL Workbench 다운로드](https://dev.mysql.com/downloads/workbench/)

## <a name="use-common-tools"></a>일반 도구 사용
MySQL Workbench, Toad 또는 Navicat과 같은 일반 도구를 사용하여 원격으로 연결하고 MySQL용 Azure 데이터베이스에서 데이터를 가져오거나 내보냅니다. 인터넷에 연결된 클라이언트 컴퓨터에서 이러한 도구를 사용하여 MySQL용 Azure 데이터베이스에 데이터에 연결합니다. 최상의 보안을 위해 SSL 암호화 연결을 사용합니다. [MySQL용 Azure 데이터베이스에서 SSL 연결 구성](concepts-ssl-connection-security.md)을 참조하세요. MySQL용 Azure 데이터베이스로 마이그레이션할 때 가져오기 및 내보내기 파일을 특수한 클라우드 위치로 이동할 필요가 없습니다. 

## <a name="create-a-database-on-the-target-azure-database-for-mysql-service"></a>대상 MySQL용 Azure 데이터베이스 서비스에서 데이터베이스 만들기
MySQL Workbench, Toad, Navicat 또는 MySQL용 타사 도구를 사용하여 데이터를 마이그레이션하려는 대상 MySQL용 Azure 데이터베이스 서버에서 빈 데이터베이스를 만들어야 합니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 갖을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

![MySQL용 Azure 데이터베이스 연결 문자열](./media/concepts-migrate-import-export/p5.png)

![MySQL Workbench 연결 문자열](./media/concepts-migrate-import-export/p4.png)

## <a name="import-and-export-using-mysql-workbench"></a>MySQL Workbench를 사용하여 내보내기 및 가져오기
MySQL Workbench에는 각각 다른 용도로 사용되는 데이터 내보내기 및 가져오기 방법이 2가지 있습니다. 

## <a name="table-data-export-and-import-wizard-using-object-browser-context-menu"></a>개체 브라우저 상황에 맞는 메뉴를 사용하는 테이블 데이터 내보내기 및 가져오기 마법사
![개체 브라우저 상황에 맞는 메뉴를 사용하여 MySQL Workbench 가져오기/내보내기](./media/concepts-migrate-import-export/p1.png)

이 마법사는 CSV 및 JSON 파일을 사용하는 가져오기 및 내보내기 작업을 지원하고 몇 가지 구성 옵션(구분 기호, 열 선택, 인코딩 선택 등)을 포함합니다. 이 마법사는 로컬 또는 원격으로 연결된 MySQL 서버에 대해 수행할 수 있으며 가져오기 작업에는 테이블, 열 및 형식 매핑이 포함됩니다. 이 마법사는 테이블을 마우스 오른쪽 단추로 클릭하여 표시되는 개체 브라우저의 상황에 맞는 메뉴에서 **테이블 데이터 내보내기 마법사** 또는 **테이블 데이터 가져오기 마법사**를 선택하여 액세스할 수 있습니다. 

## <a name="table-data-export-wizard"></a>테이블 데이터 내보내기 마법사
다음 예제에서는 테이블을 CSV 파일로 내보냅니다. 
- 내보낼 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다. 
- **테이블 데이터 내보내기 마법사**를 선택합니다. 내보낼 열, 행 오프셋(있는 경우), 개수(있는 경우)를 선택합니다. 
- '내보낼 데이터 선택' 창에서 **다음**을 클릭합니다. 파일 경로, CSV 또는 JSON 파일 형식, 줄 구분 기호, 포함할 문자열 및 필드 구분 기호를 선택합니다. 
- '출력 파일 위치 선택' 창에서 **다음**을 선택하고 '데이터 내보내기' 창에서 다음을 선택합니다.


## <a name="table-data-import-wizard"></a>테이블 데이터 가져오기 마법사
다음 예제에서는 CSV 파일에서 테이블을 가져옵니다.
- 가져올 데이터베이스의 테이블을 마우스 오른쪽 단추로 클릭합니다. 
- 가져올 CSV 파일을 찾아서 선택한 후 다음 단추를 선택합니다. 
- 대상 테이블(신규 또는 기존)을 선택하고 '가져오기 전에 테이블 자르기' 확인란의 선택하거나 선택 취소하고 다음 단추를 클릭합니다.
- 가져올 인코딩 및 열을 선택하고 다음 단추를 선택합니다. 
- 데이터 가져오기 창에서 다음을 선택합니다. 그러면 데이터가 가져와집니다.

## <a name="sql-data-export-and-import-wizard-from-management-navigator"></a>관리 탐색 창의 SQL 데이터 내보내기 및 가져오기 마법사
이 마법사를 사용하여 MySQL Workbench 또는 mysqldump 명령을 사용하여 생성된 SQL을 내보내거나 가져옵니다. 탐색 창 패널에서 또는 주 메뉴에서 서버를 선택한 다음 데이터 가져오기 또는 데이터 내보내기 중 하나를 선택하여 이러한 마법사에 액세스합니다. 

## <a name="data-export"></a>데이터 내보내기
![관리 탐색 창을 사용하여 MySQL Workbench 데이터 내보내기](./media/concepts-migrate-import-export/p2.png)

이 탭에서 MySQL 데이터를 내보낼 수 있습니다. 
- 내보내려는 각 스키마를 선택하고, 필요에 따라 각 스키마에서 특정 스키마 개체/테이블을 선택하고, 내보내기를 생성합니다. 구성 옵션에는 프로젝트 폴더 또는 자체 포함된 SQL 파일로 내보내기가 포함되고, 경우에 따라 저장된 루틴 및 이벤트를 덤프하거나 테이블 데이터를 건너뛰는 작업도 포함됩니다. 
- 또는 **결과 집합 내보내기**를 사용하여 SQL 편집기에서 CSV, JSON, HTML 및 XML 등의 다른 형식으로 특정 결과 집합을 내보냅니다. 
- 내보낼 데이터베이스 개체를 선택하고 관련 옵션을 구성합니다. 
- **새로 고침**을 클릭하여 현재 개체를 로드합니다. 
- 필요에 따라 내보내기 작업을 미세 조정할 수 있는 고급 옵션 탭을 엽니다. 예를 들어 테이블 잠금을 추가하고, insert 문 대신 replace를 사용하고, 억음 부호로 식별자를 묶는 등의 작업을 수행할 수 있습니다. 
- **내보내기 시작**을 클릭하여 내보내기 프로세스를 시작합니다. 


## <a name="data-import"></a>데이터 가져오기
![관리 탐색 창을 사용하여 MySQL Workbench 데이터 가져오기](./media/concepts-migrate-import-export/p3.png)

이 탭에서 데이터 내보내기 작업에서 내보낸 데이터 또는 mysqldump 명령에서 내보낸 다른 데이터를 가져오거나 복원할 수 있습니다. 
- 프로젝트 폴더 또는 자체 포함된 SQL 파일을 선택하거나, 데이터를 가져올 스키마를 선택하거나, 새로 만들기를 선택하여 새 스키마를 정의합니다. 
- **가져오기 시작**을 클릭하여 가져오기 프로세스를 시작하면 데이터가 가져오기됩니다.

## <a name="next-steps"></a>다음 단계
이 데이터베이스 서비스를 시작하는 방법을 잘 모를 경우 다음을 검토합니다.
-  [Azure Portal을 사용한 MySQL용 Azure 데이터베이스 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md) 
- [Azure CLI를 사용한 MySQL용 Azure 데이터베이스 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)

