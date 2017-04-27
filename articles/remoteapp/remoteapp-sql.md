---
title: "Azure RemoteApp을 사용하는 SQL Azure | Microsoft Docs"
description: "Azure RemoteApp으로 SQL Azure을 사용하는 방법을 알아봅니다."
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
editor: 
ms.assetid: 35f81d75-bfd7-4980-807e-00339f2cb2a4
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 24d41c7eb6b5bd34c07d21318740ecf4c6db2d22
ms.lasthandoff: 03/31/2017


---
# <a name="sql-azure-with-azure-remoteapp"></a>Azure RemoteApp을 사용하는 SQL Azure 
> [!IMPORTANT]
> Azure RemoteApp은 2017년 8월 31일에 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148) 을 읽어보세요.
> 
> 

또한 고객이 Azure RemoteApp을 사용하여 클라우드에서 Windows 응용 프로그램을 호스트하도록 선택할 경우 전체 클라우드 배포에 대해 SQL server와 같은 데이터를 클라우드로 마이그레이션하려 합니다. 그러면 Azure RemoteApp을 사용하여 어디서나 언제든지 장치에서 액세스할 수 있는 전체 클라우드 호스팅 솔루션이 가능합니다. 이 프로세스를 지원하기 위한 지침과 링크 및 참조는 다음과 같습니다.  

## <a name="migrate-your-sql-data"></a>SQL 데이터 마이그레이션
[SQL Server 데이터베이스를 Azure SQL 데이터베이스로 마이그레이션](../sql-database/sql-database-cloud-migrate.md)을 시작합니다. 

## <a name="configure-azure-remoteapp"></a>Azure RemoteApp 구성
Azure RemoteApp에서 Windows 응용 프로그램을 호스트합니다. 다음은 단계별로 매우 높은 수준입니다.

1. [Azure RemoteApp 템플릿 VM](remoteapp-imageoptions.md)을 만듭니다. 
2. VM에 필요한 응용 프로그램을 설치합니다.
3. 응용 프로그램을 구성하므로 SQL DB에 연결하고 작동하는지 확인합니다.
4. VM Sysprep 및 종료 Azure와 함께 사용하기 위해 이미지 형식으로 캡처합니다. **참고:** 응용 프로그램이 sysprep 프로세스를 통해 DB 연결 정보를 유지할 수 있도록 해야 합니다. 응용 프로그램이 DB 연결 정보를 유지할 수 없는 경우 연결 문자열을 지정할 수 있는 방법을 확인하도록 응용 프로그램의 공급 업체가 참여하길 바랄 수 있습니다.
5. SQL Azure 배포에 있는 적절한 지리적 위치를 선택하여 Azure RemoteApp 라이브러리에 사용자 지정 이미지를 가져옵니다. 
6. 위의 템플릿을 사용하여 SQL Azure 배포와 동일한 데이터 센터에 RemoteApp 컬렉션을 배포하고 응용 프로그램을 게시합니다. SQL Azure와 동일한 데이터 센터에서 Azure RemoteApp을 배포하면 연결 속도가 가장 빨라지고 대기 시간을 줄일 수 있습니다. 

## <a name="app-and-sql-configuration-considerations"></a>앱 및 SQL 구성 고려 사항
RemoteApp과 함께 Azure SQL을 사용하는 경우 고려해야 할 몇가지 사항이 있습니다.

[Azure SQL 데이터베이스 방화벽을 구성하는 방법](../sql-database/sql-database-firewall-configure.md)을 알아봅니다. 문서의 출처에서는 다음과 같이 언급합니다. "먼저 Azure SQL Database 서버로의 모든 액세스는 방화벽에 의해 차단됩니다. Azure SQL 데이터베이스 서버를 사용하려면 클래식 포털로 가서 Azure SQL 데이터베이스 서버로 액세스를 가능하게 하는 하나 이상의 서버 수준 방화벽 규칙을 꼭 지정해야 합니다. 방화벽 규칙을 사용하여 허용되는 인터넷의 IP 주소 범위 및 Azure 응용 프로그램을 Azure SQL Database 서버에 연결 시도 가능 여부를 지정할 수 있습니다."

또한 컴퓨터가 인터넷의 데이터베이스로 연결을 시도할 때, 방화벽은 서버 수준 및 데이터베이스 수준(필요한 경우) 방화벽 규칙의 전체 집합 대응을 요청하는 본래 IP 주소를 확인합니다. "요청된 IP 주소가 서버 수준 방화벽 규칙의 지정된 범위 안에 있을 경우, Azure SQL Database 서버로 연결됩니다." 따라서 개별 원본 IP 주소 뿐만 아니라 IP 범위를 사용할 수 있습니다.

[방법: Azure 포털을 사용하여 SQL 데이터베이스에 방화벽 설정 구성](../sql-database/sql-database-configure-firewall-settings.md) 의 단계별 지침에 따라 IP 범위를 지정합니다. SQL 방화벽 규칙을 구성하는 경우 Azure RemoteApp 컬렉션에 지정된 서브넷의 IP 범위를 제공합니다. 이렇게 하면 ARA 서버에 동적으로 할당된 IP 주소가 있더라도 SQL DB에 연결할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
Azure 또는 온-프레미스에 호스팅되는 SQL 데이터베이스에 연결하는 Azure RemoteApp에서 호스팅되는 클라이언트 응용 프로그램을 사용하는 환경이 느린 경우 몇 가지 이유가 있을 수 있습니다.  

* 장치에서 Azure에 대한 네트워크 대기 시간이 깁니다. 최상의 성능을 위해 가장 좋고 빠른 네트워크 연결로 이동합니다. [azurespeed.com](http://azurespeed.com/) 을 일반적인 도구로 사용하여 Azure 데이터 센터에 장치 대기 시간을 테스트합니다.  
* Azure RemoteApp에서 호스팅되는 클라이언트 앱은 부하가 높습니다. Premium 청구와 같은 다른 요금제를 선택하면 성능이 향상됩니다. 응용 프로그램이 소비하는 리소스를 모니터링하는 다른 방법은 다음과 같습니다. 활성 세션 동안 SAS 화면을 시작하고 작업 관리자를 선택하며 앱에 대한 리소스 사용률을 관찰하는 ctrl-alt-end 키 시퀀스를 수행합니다.
* SQL Server은 부하가 많거나 최적화되지 않습니다. 문제 해결에 대한 SQL 지침을 따릅니다. 


