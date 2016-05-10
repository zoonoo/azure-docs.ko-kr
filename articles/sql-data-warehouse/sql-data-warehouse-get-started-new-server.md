<properties
   pageTitle="Azure 포털에서 SQL 데이터 웨어하우스 데이터베이스 만들기 | Microsoft Azure"
   description="Azure 포털에서 SQL 데이터 웨어하우스를 만드는 방법을 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="lodipalm;"/>

# 새로운 논리 서버 만들기

SQL 데이터베이스 및 SQL 데이터 웨어하우스에에서 각 데이터베이스는 서버에 할당되고 각 서버는 지리적 위치에 할당됩니다. 이 서버를 논리적 SQL server라고 합니다.

> [AZURE.NOTE] <a name="note"></a>논리적 SQL server:
  >
  > + 지리적으로 같은 위치 내에서 여러 데이터베이스를 구성하는 일관된 방법을 제공합니다.
  > + 온-프레미스 서버처럼 실제 하드웨어는 아닙니다. 서비스 소프트웨어의 일부입니다. 이 때문에 *논리적 서버*라고 합니다.
  > + 데이터베이스의 성능에 영향을 주지 않고 여러 데이터베이스를 호스트할 수 있습니다.
  > + 이름에 소문자 *s*를 사용합니다. SQL **s**erver는 Azure 논리적 서버인 반면 SQL **S**erver는 Microsoft의 온-프레미스 데이터베이스 제품입니다.

1. **서버** > **새 서버 만들기**를 클릭합니다. 서버에 대한 요금은 부과되지 않습니다. 사용할 V12 논리 SQL server가 이미 있는 경우 기존 서버를 선택하고 다음 단계로 이동합니다.

    ![새 서버 만들기](./media/sql-data-warehouse-get-started-provision/create-server.png)

2. **새 서버** 정보를 입력합니다.

	- **서버 이름**: 논리적 서버에 대한 이름을 입력합니다. 각 지리적 위치에 대해 고유합니다.
	- **서버 관리자 이름**: 서버 관리자 계정에 대한 사용자 이름을 입력합니다.
	- **암호**: 서버 관리자 암호를 입력합니다.
	- **위치**: 서버에 대한 지리적 위치를 선택합니다. 데이터 전송 시간을 줄이려면 이 데이터베이스가 액세스할 다른 데이터 리소스와 지리적으로 가까운 서버를 찾는 것이 좋습니다.
	- **V12 서버 만들기**: 예는 SQL 데이터 웨어하우스에 대한 유일한 옵션입니다.
	- **Azure 서비스가 서버에 액세스할 수 있도록 허용**: SQL 데이터 웨어하우스에 대해 항상 선택되어 있습니다.

    >[AZURE.NOTE] 서버 이름, 서버 관리자 이름 및 암호를 어딘가에 저장해야 합니다. 서버에 로그온하려면 다음 정보가 필요합니다.

3. **확인**을 클릭하여 논리 SQL server 구성 설정을 저장하고 SQL 데이터 웨어하우스 블레이드로 돌아갑니다.

    ![새 서버 구성](./media/sql-data-warehouse-get-started-provision/configure-server.png)

<!---HONumber=AcomDC_0504_2016-->