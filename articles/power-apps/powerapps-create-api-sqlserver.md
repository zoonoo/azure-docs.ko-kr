<properties
	pageTitle="PowerApps 엔터프라이즈에 SQL Server API 추가 | Microsoft Azure"
	description="조직의 앱 서비스 환경에서 새 SQL Server API 만들기 또는 구성 및 데이터 온-프레미스에 연결 추가"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwrede"
	editor=""/>


<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>


# 조직의 앱 서비스 환경에서 새 SQL Server API 만들기

## Azure 포털에서 API 만들기

1. [Azure 포털](https://portal.azure.com/)에서 회사 계정으로 로그인합니다. 예를 들어 *yourUserName*@*YourCompany*.com으로 로그인합니다. 그러면 회사 구독에 자동으로 로그인됩니다. 
2. 작업 표시줄에서 **찾아보기**를 선택합니다. ![][14]  
3. 목록을 스크롤하여 PowerApps를 찾거나 *powerapps*를 입력합니다. ![][15]  
4. **PowerApps**에서 **API 관리**를 선택합니다.
5. **API 관리**에서 **추가**를 선택하여 새 API를 추가합니다.
6. API에 대한 설명이 포함된 **이름**을 입력합니다. 예를 들어 데모에 대해 SQL Server API를 추가하고 이름을 *SQLServerDemo*로 지정할 수 있습니다.  	
7. **원본**에서 **사용 가능한 API**를 선택하여 이미 만들어진 API를 선택한 다음 **SQL Server**를 선택합니다. 
8. **확인**을 선택하여 이 단계를 완료합니다.

완료하면 새 SQL Server API가 앱 서비스 환경에 추가됩니다.

## SQL Server 온-프레미스에 대한 연결 구성

SQL Server 온-프레미스에 연결할 수 있습니다. 이 하이브리드 연결을 설정하려면 Azure에서 다음을 포함한 기존 하이브리드 네트워킹 솔루션을 활용할 수 있습니다.

- [Express 경로](../expressroute-introduction.md)
- [사이트 간 VPN](../vpn-gateway-create-site-to-site-rm-powershell.md)
- [지점 및 사이트 간 연결](../vpn-gateway-point-to-site-create.md)  

	> [AZURE.NOTE]모든 앱 서비스 환경에는 가상 네트워크가 연결되어 있습니다. 이 가상 네트워크에 이 네트워크 연결을 설정할 수 있습니다.  
- [하이브리드 연결](../web-sites-hybrid-connection-get-started.md)  

	> [AZURE.NOTE]앱 서비스 환경에 등록된 모든 API에는 해당 웹앱이 있습니다. 다른 웹앱에서와 마찬가지로 이 웹앱에서 하이브리드 연결을 설정할 수 있습니다.
	
다음 예제에서는 하이브리드 연결을 만드는 방법을 보여 줍니다.

1. 방금 만든 SQL Server API를 선택하고 리소스 그룹을 선택합니다. 이 예제에서는 *sqlconnectordemo*라는 API를 선택하고 *DedicatedAses* 리소스 그룹을 선택합니다. ![리소스 그룹](./media/powerapps-create-api-sqlserver/sqlapi.png)

2.  **리소스** 타일을 선택한 다음 SQL Server API와 이름이 같은 웹앱을 선택합니다. 이 예제에서는 *sqlconnectordemo*를 선택합니다. ![Sql 웹앱](./media/powerapps-create-api-sqlserver/sqlwebapp.png)

3.  **설정**에서 **네트워킹**을 선택합니다. **하이브리드 연결 끝점 구성**을 선택하고 [다음 지침](../web-sites-hybrid-connection-get-started.md)을 따라 하이브리드 연결을 만듭니다. ![네트워킹](./media/powerapps-create-api-sqlserver/network.png)

하이브리드 연결을 만들고 연결되면 온-프레미스 서버에 대한 연결을 사용하도록 설정한 것입니다. 그런 다음 데이터에 대한 연결을 만들고 사용자에게 액세스 권한을 제공합니다. ![하이브리드 연결](./media/powerapps-create-api-sqlserver/hybridconn.png)

## SQL Server API에 대한 연결 만들기

1. Azure 포털에서 PowerApps를 열고 **API 관리**를 선택합니다. 구성된 API 목록이 표시됩니다. ![](./media/powerapps-create-api-sqlserver/apilist.png)

2. 원하는 API를 선택합니다. 이 예제에서는 **SQLServerDemo**를 선택하고 **연결**을 선택합니다.

3. 연결에서 **연결 추가**를 선택합니다. ![](./media/powerapps-create-api-sqlserver/addconnection.png)

4. 연결에 대한 이름을 입력하고 연결 문자열을 입력합니다. 연결 문자열을 입력하려면 연결하는 서비스에 대한 특정 속성 일부를 알아야 합니다. 예를 들어 온-프레미스 SQL Server에 연결하려면 성공적으로 연결하는 데 필요한 사용자 이름, 암호 및 기타 속성이 필요합니다.

5. **추가**를 선택하여 변경 내용을 저장합니다.

## 요약 및 다음 단계
이 항목에서는 SQL Server 온-프레미스에 연결하기 위해 SQL Server API를 추가했습니다. 그 다음에는 이 API를 사용자의 앱에 추가할 수 있도록 사용자에게 이 API에 대한 액세스 권한을 부여합니다.

[연결 추가 및 사용자에게 액세스 권한 부여](powerapps-manage-api-connection-user-access.md)


[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png

<!---HONumber=AcomDC_1203_2015-->