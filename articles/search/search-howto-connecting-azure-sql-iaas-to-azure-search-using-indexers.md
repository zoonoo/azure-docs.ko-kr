<properties 
	pageTitle="Azure 가상 컴퓨터에서 Azure 검색 인덱서로부터 SQL Server로의 연결 구성 | Microsoft Azure | 인덱서" 
	description="암호화된 연결을 사용하도록 설정하고 Azure 검색의 인덱서에서 Azure VM(가상 컴퓨터)의 SQL Server로의 연결을 허용하도록 방화벽을 구성합니다." 
	services="search" 
	documentationCenter="" 
	authors="jack4it" 
	manager="pablocas" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="09/06/2016" 
	ms.author="jackma"/>

# Azure VM에서 Azure 검색 인덱서로부터 SQL Server로의 연결 구성

[인덱서를 사용하여 Azure 검색에 Azure SQL 데이터베이스 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions)에 설명된 것처럼 Azure 검색에서는 **Azure VM SQL Server**(줄여서 **SQL Azure VM**)에 대해 인덱서를 만드는 것을 지원하지만 먼저 두 가지 보안 관련 필수 구성 요소에 유의해야 합니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 암호화 연결 사용

Azure 검색에는 데이터베이스에서 데이터를 읽기 위해 보안 연결이 필요합니다. 즉, SSL 인증서를 구성하여 SQL Azure VM에서 암호화된 연결을 사용하도록 설정해야 합니다.

SQL Server에 대한 암호화된 연결을 사용하도록 설정하는 단계는 [데이터베이스 엔진에 암호화된 연결 사용](https://msdn.microsoft.com/library/ms191192.aspx)에 설명되어 있지만 Azure 검색에서 SQL Azure VM으로의 연결과 같은 공용 인터넷 연결의 경우에는 몇 가지 추가 요구 사항이 필요합니다.

### SSL 인증서에 FQDN 지정

SSL 인증서의 주체 이름은 SQL Azure VM의 정규화된 도메인 이름(또는 **FQDN**)이어야 합니다. 이 이름은 검색 서비스에서 데이터 원본을 만들 때 데이터베이스 연결에 지정하는 것과 동일한 FQDN입니다. FQDN 형식은 **Resource Manager** VM에 대해 `<your-VM-name>.<region>.cloudapp.azure.com`으로 지정됩니다. 여전히 **클래식** VM에서 작업하는 경우 `<your-cloud-service-name.cloudapp.net>`으로 형식이 지정됩니다. [Azure 포털](https://portal.azure.com/)에서 DNS 이름/레이블로 지정된 SQL Azure VM의 FQDN을 찾을 수 있습니다.

### REGEDIT을 사용하여 SSL 인증서 구성

SQL Server 구성 관리자는 설명서에 지정된 것처럼 **인증서** 드롭다운에 FQDN SSL 인증서를 표시할 수 없습니다. 해결 방법은 다음 레지스트리 키를 편집하여 SSL 인증서를 구성하는 것입니다. **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Microsoft SQL Server[MSSQL13.MSSQLSERVER]\\MSSQLServer\\SuperSocketNetLib\\Certificate** *[MSSQL13.MSSQLSERVER]* 부분은 SQL Server 버전 및 인스턴스 이름에 따라 다릅니다. 이 키는 SQL Azure VM에 설치된 SSL 인증서의 **지문**으로 업데이트해야 합니다.

### 서비스 계정에 권한 부여

SQL Server 서비스 계정에 SSL 인증서의 개인 키에 대한 적절한 권한이 부여되었는지 확인합니다. 이 단계를 무시하면 SQL Server가 시작되지 않습니다.

## Azure 검색에서의 연결을 허용하도록 방화벽 구성

다른 대상에서 Azure VM에 액세스할 수 있게 하기 위해 방화벽 및 해당 Azure 끝점 또는 ACL(액세스 제어 목록)을 구성하는 것은 특별하지 않습니다. 이미 이러한 구성을 수행하여 자체 응용 프로그램 논리가 SQL Azure VM에 연결되도록 했을 것입니다. 이것은 SQL Azure VM에 Azure 검색을 연결하는 것과는 다릅니다. 이 작업을 아직 수행하지 않았으면 다음 보안 방침에 유의하는 것이 좋습니다.

**Resource Manager** VM을 사용하는 경우 [Azure에서 Resource Manager를 사용하여 SQL Server 가상 컴퓨터에 연결](../virtual-machines/virtual-machines-windows-sql-connect.md)을 참조하세요. 아직도 **클래식** VM에 있는 경우 [Azure 클래식에서 SQL Server 가상 컴퓨터에 연결](../virtual-machines/virtual-machines-windows-classic-sql-connect.md)을 참조하세요.

### 검색 서비스 IP 주소로 액세스 제한

어떤 배포 모델을 사용하든, Azure 검색에서의 연결을 구성할 때 모든 연결 요청에 대해 SQL Azure VM이 연결되도록 허용하는 대신, ACL에 포함된 검색 서비스의 IP 주소로 액세스를 제한하는 것이 좋습니다. 검색 서비스의 FQDN(예: `<your-search-service-name>.search.windows.net`)을 ping하여 IP 주소를 쉽게 찾을 수 있습니다.

### IP 주소 범위 구성

검색 서비스에 검색 단위가 하나만 있으면(즉, 하나의 복제본과 하나의 파티션) 라우팅 서비스를 다시 시작하는 동안 IP 주소가 변경될 수 있습니다. 연결 오류를 방지하려면 검색 서비스가 프로비전되는 Azure 지역의 IP 주소 범위를 지정해야 합니다. 공용 IP 주소를 Azure 리소스에 할당할 때 사용되는 IP 범위 목록은 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 게시되어 있습니다.

### Azure 검색 포털 IP 주소 포함

또한 Azure 포털을 사용하여 인덱서를 만드는 경우, 생성 중에 Azure 검색 포털 논리도 SQL Azure VM에 액세스할 수 있어야 합니다. `stamp1.search.ext.azure.com` 및 `stamp2.search.ext.azure.com`ing하여 Azure 검색 포털 IP 주소를 찾을 수 있습니다.

## 다음 단계

위의 구성 요구 사항이 해결되면 이제 Azure VM의 SQL Server를 Azure 검색 인덱서에 대한 데이터 원본으로 지정할 수 있습니다. 자세한 내용은 [인덱서를 사용하여 Azure 검색에 Azure SQL 데이터베이스 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)을 참조하세요.

<!---HONumber=AcomDC_0907_2016-->