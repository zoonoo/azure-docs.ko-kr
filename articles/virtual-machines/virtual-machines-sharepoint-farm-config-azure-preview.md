<properties
	pageTitle="SharePoint Server 팜 구성 | Microsoft Azure"
	description="Azure Preview 포털의 SharePoint Server 팜 기능을 사용하는 경우 SharePoint 팜의 기본 구성에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/07/2015"
	ms.author="josephd"/>


# SharePoint 서버 팜 구성 세부 정보

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다.

SharePoint 서버 팜은 미리 구성된 SharePoint Server 2013 팜을 자동으로 만드는 Azure Preview 포털의 기능입니다. 다음과 같은 두 가지 팜 구성이 있습니다.

- Basic
- 고가용성

다음 섹션에서는 각 팜의 구성을 자세히 설명합니다.

자세한 내용은 [SharePoint Server 팜](virtual-machines-sharepoint-farm-azure-preview.md)을 참조하세요.

## 기본 SharePoint 팜

기본 SharePoint 팜은 다음 구성의 3가지 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png)

구성 세부 정보는 다음과 같습니다.

-	Azure 구독: 초기 구성 중에 지정됩니다.
-	Azure 도메인 이름(클라우드 서비스라고도 함): 각 가상 컴퓨터에 대해 별도의 도메인 이름이 자동으로 만들어집니다.
-	저장소 계정: 초기 구성 중에 지정됩니다.
-	가상 네트워크:
	-   형식: 클라우드 전용
    -	주소 공간: 10.0.0.0/26

- 가상 컴퓨터:
	-	*HostNamePrefix*-DC (AD DS 도메인 컨트롤러)
	-	*HostNamePrefix*-SQL (SQL Server 2014 서버)
	-	*HostNamePrefix*-SP (SharePoint 2013 서버)

- 도메인 컨트롤러:
	-	가상 컴퓨터 이미지: Windows Server 2012 R2.
	-	호스트 이름 접두사: 초기 구성 중에 지정됩니다.
	-	크기: A1(기본값)
	-	도메인 이름: contoso.com(기본값)
	-	도메인 관리자 계정 이름: 초기 구성 중에 지정됩니다.
	-	도메인 관리자 계정 암호: 초기 구성 중에 지정됩니다.

- 데이터베이스 서버
	-	가상 컴퓨터 이미지: Windows Server 2012 R2의 SQL Server 2014 RTM Enterprise
	-	호스트 이름 접두사: 초기 구성 중에 지정됩니다.
	-	크기: A5(기본값)
	-	데이터베이스 액세스 계정 이름: 초기 구성 중에 지정됩니다.
	-	데이터베이스 액세스 계정 암호: 초기 구성 중에 지정됩니다.
	-	SQL Server 서비스 계정 이름: sqlservice(기본값)
	-	SQL 서버 서비스 계정 암호: 초기 구성 중에 지정됩니다.

- SharePoint Server
	-	가상 컴퓨터 이미지: SharePoint Server 2013 평가판입니다.
	-	호스트 이름 접두사: 초기 구성 중에 지정됩니다.
	-	크기: A2(기본값)
	-	SharePoint 팜 계정 이름: sp\_farm(기본값)
	-	SharePoint 팜 계정 암호: 초기 구성 중에 지정됩니다.
	-	SharePoint 팜 암호: 초기 구성 중에 지정됩니다.


## 고가용성 SharePoint 팜

고가용성 SharePoint 팜은 다음과 같은 구성으로 9개의 가상 컴퓨터로 구성됩니다.

![sharepointfarm](./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png)

구성 세부 정보는 다음과 같습니다.

-	Azure 구독: 초기 구성 중에 지정됩니다.
-	Azure 도메인 이름(클라우드 서비스라고도 함): 위의 그림에 따라 별도의 도메인 이름이 만들어집니다.
-	저장소 계정: 초기 구성 중에 지정됩니다.
-	가상 네트워크:
	-	형식: 클라우드 전용
	-	주소 공간: 10.0.0.0/26

-	가상 컴퓨터:
	-	*HostNamePrefix*-DC1 (AD DS 도메인 컨트롤러)
	-	*HostNamePrefix*-DC2 (AD DS 도메인 컨트롤러)
	-	*HostNamePrefix*-SQL1 (SQL Server 2014 서버)
	-	*HostNamePrefix*-SQL2 (SQL Server 2014 서버)
	-	*HostNamePrefix*-SQL0 (Windows Server 2012 R2 서버)
	-	*HostNamePrefix*-WEB1 (SharePoint 2013 서버)
	-	*HostNamePrefix*-WEB2 (SharePoint 2013 서버)
	-	*HostNamePrefix*-APP1 (SharePoint 2013 서버)
	-	*HostNamePrefix*-APP2 (SharePoint 2013 서버)

-	도메인 컨트롤러:
	-	가상 컴퓨터 이미지: Windows Server 2012 R2.
	-	호스트 이름 접두사: 초기 구성 중에 지정됩니다.
	-	크기: A1(기본값)
	-	도메인 이름: contoso.com(기본값)
	-	도메인 관리자 계정 이름: 초기 구성 중에 지정됩니다.
	-	도메인 관리자 계정 암호: 초기 구성 중에 지정됩니다.

-	데이터베이스 서버:
	-	가상 컴퓨터 이미지: Windows Server 2012 R2의 SQL Server 2014 RTM Enterprise
	-	호스트 이름 접두사: 초기 구성 중에 지정됩니다.
	-	크기: 데이터베이스 서버의 경우 A5(기본값), 파일 공유 감시의 경우 A0(기본값)
	-	데이터베이스 액세스 계정 이름: 초기 구성 중에 지정됩니다.
	-	데이터베이스 액세스 계정 암호: 초기 구성 중에 지정됩니다.
	-	SQL Server 서비스 계정 이름: sqlservice(기본값)
	-	SQL 서버 서비스 계정 암호: 초기 구성 중에 지정됩니다.

-	SharePoint 서버:
	-	가상 컴퓨터 이미지: SharePoint Server 2013 평가판입니다.
	-	호스트 이름 접두사: 초기 구성 중에 지정됩니다.
	-	크기: A2(기본값)
	-	SharePoint 팜 계정 이름: sp\_farm(기본값)
	-	SharePoint 팜 계정 암호: 초기 구성 중에 지정됩니다.
	-	SharePoint 팜 암호: 초기 구성 중에 지정됩니다.

> [AZURE.NOTE]SharePoint 서버는 SharePoint Server 2013 평가판 이미지에서 만들어집니다. 평가판 만료 이후에 가상 컴퓨터를 계속 사용하려면 SharePoint Server 2013의 Standard 또는 Enterprise 버전용 일반 정품 또는 볼륨 라이선스 키를 사용하도록 설치를 전환해야 합니다.

## Azure 리소스 관리자

Azure Preview 포털의 SharePoint 서버 팜 기능은 서비스 관리에서 가상 컴퓨터를 만듭니다. Azure 리소스 관리자에서 SharePoint Server 2013 팜을 만들려면 [Azure 리소스 관리자 템플릿을 사용하여 SharePoint 팜 배포](virtual-machines-workload-template-sharepoint.md)를 참조하세요.

## 추가 리소스

[SharePoint 서버 팜](virtual-machines-sharepoint-farm-azure-preview.md)

[Azure 가상 컴퓨터의 SharePoint](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[테스트용 하이브리드 클라우드에 SharePoint 인트라넷 팜 설치](../virtual-network/virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

<!---HONumber=Sept15_HO3-->