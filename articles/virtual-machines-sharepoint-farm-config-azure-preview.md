<properties title="SharePoint Server 팜 구성 정보" pageTitle="SharePoint Server 팜 구성 정보" description="SharePoint 팜의 기본 구성에 대해 설명합니다." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-sharepoint" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="josephd" />

# SharePoint Server 팜 구성 정보

SharePoint Server 팜은 미리 구성된 SharePoint Server 2013 팜을 자동으로 만드는 Microsoft Azure 미리 보기 포털의 기능입니다. 다음과 같은 두 가지 팜 구성이 있습니다.

-   Basic
-   고가용성

다음 섹션에서는 각 팜의 구성을 자세히 설명합니다.

자세한 내용은 [SharePoint Server 팜][SharePoint Server 팜]을 참조하세요.

## 기본 SharePoint 팜

기본 SharePoint 팜은 다음 구성의 3가지 가상 컴퓨터로 구성됩니다.

![sharepointfarm][sharepointfarm]

구성 세부 정보는 다음과 같습니다.

-   Azure 구독: 초기 구성 중에 지정됩니다.
-   Azure 도메인 이름(클라우드 서비스라고도 함): 각 가상 컴퓨터에 대해 별도의 도메인 이름이 자동으로 만들어집니다.
-   저장소 계정: 초기 구성 중에 지정됩니다.
-   가상 네트워크

    -   형식: 클라우드 전용
    -   주소 공간: 192.168.16.0/26
-   가상 컴퓨터

    -   HostNamePrefix-DC(AD DS 도메인 컨트롤러)
    -   HostNamePrefix-SQL(SQL Server 2014 서버)
    -   HostNamePrefix-SP(SharePoint 2013 서버)
-   도메인 컨트롤러

    -   호스트 이름 접두사: 초기 구성 중에 지정됩니다.
    -   크기: A1(기본값)
    -   도메인 이름: contoso.com(기본값)
    -   도메인 관리자 계정 이름: 초기 구성 중에 지정됩니다.
    -   도메인 관리자 계정 암호: 초기 구성 중에 지정됩니다.
-   SQL Server

    -   호스트 이름 접두사: 초기 구성 중에 지정됩니다.
    -   크기: A5(기본값)
    -   데이터베이스 액세스 계정 이름: 초기 구성 중에 지정됩니다.
    -   데이터베이스 액세스 계정 암호: 초기 구성 중에 지정됩니다.
    -   SQL Server 서비스 계정 이름: 초기 구성 중에 지정됩니다.
    -   SQL Server 서비스 계정 암호: 초기 구성 중에 지정됩니다.
-   SharePoint Server

    -   호스트 이름 접두사: 초기 구성 중에 지정됩니다.
    -   크기: A2(기본값)
    -   SharePoint 팜 계정 이름: 초기 구성 중에 지정됩니다.
    -   SharePoint 팜 계정 암호: 초기 구성 중에 지정됩니다.
    -   SharePoint 팜 암호: 초기 구성 중에 지정됩니다.

## 고가용성

고가용성 SharePoint 팜은 다음과 같은 구성으로 9개의 가상 컴퓨터로 구성됩니다.

![sharepointfarm][1]

구성 세부 정보는 다음과 같습니다.

-   Azure 구독: 초기 구성 중에 지정됩니다.
-   Azure 도메인 이름(클라우드 서비스라고도 함): 각 가상 컴퓨터에 대해 별도의 도메인 이름이 자동으로 만들어집니다.
-   저장소 계정: 초기 구성 중에 지정됩니다.
-   가상 네트워크

    -   형식: 클라우드 전용
    -   주소 공간: 192.168.16.0/26
-   가상 컴퓨터

    -   HostNamePrefix-DC1(AD DS 도메인 컨트롤러)
    -   HostNamePrefix-DC2(AD DS 도메인 컨트롤러)
    -   HostNamePrefix-SQL1(SQL Server 2014 서버)
    -   HostNamePrefix-SQL2(SQL Server 2014 서버)
    -   HostNamePrefix-SQL0(SQL Server 2014 서버)
    -   HostNamePrefix-WEB1(SharePoint 2013 서버)
    -   HostNamePrefix-WEB2(SharePoint 2013 서버)
    -   HostNamePrefix-APP1(SharePoint 2013 서버)
    -   HostNamePrefix-APP2(SharePoint 2013 서버)
-   도메인 컨트롤러

    -   호스트 이름 접두사: 초기 구성 중에 지정됩니다.
    -   크기: A1(기본값)
    -   도메인 이름: contoso.com(기본값)
    -   도메인 관리자 계정 이름: 초기 구성 중에 지정됩니다.
    -   도메인 관리자 계정 암호: 초기 구성 중에 지정됩니다.
-   SQL Server

    -   호스트 이름 접두사: 초기 구성 중에 지정됩니다.
    -   크기: A5(기본값)
    -   데이터베이스 액세스 계정 이름: 초기 구성 중에 지정됩니다.
    -   데이터베이스 액세스 계정 암호: 초기 구성 중에 지정됩니다.
    -   SQL Server 서비스 계정 이름: 초기 구성 중에 지정됩니다.
    -   SQL Server 서비스 계정 암호: 초기 구성 중에 지정됩니다.
-   SharePoint Server

    -   호스트 이름 접두사: 초기 구성 중에 지정됩니다.
    -   크기: A2(기본값)
    -   SharePoint 팜 계정 이름: 초기 구성 중에 지정됩니다.
    -   SharePoint 팜 계정 암호: 초기 구성 중에 지정됩니다.
    -   SharePoint 팜 암호: 초기 구성 중에 지정됩니다.

  [SharePoint Server 팜]: ../virtual-machines-sharepoint-farm-azure-preview/
  [sharepointfarm]: ./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_Basic.png
  [1]: ./media/virtual-machines-sharepoint-farm-config-azure-preview/SPFarm_HighAvail.png
