<properties
   pageTitle="논리 앱 온-프레미스 데이터 게이트웨이 연결 | Microsoft Azure"
   description="논리 앱에서 온-프레미스 데이터 게이트웨이에 대한 연결을 작성하는 방법에 대한 정보입니다."
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# 논리 앱에 대한 온-프레미스 데이터 게이트웨이에 연결

지원되는 논리 앱 커넥터를 사용하여 온-프레미스 데이터 게이트웨이를 통해 온-프레미스 데이터에 액세스하도록 연결을 구성할 수 있습니다. 다음 단계에서는 논리 앱에서 작동하도록 온-프레미스 데이터 게이트웨이를 설치 및 구성하는 방법을 안내합니다.

## 필수 조건

* Azure에서 회사 또는 학교 전자 메일 주소를 사용하여 온-프레미스 데이터 게이트웨이를 계정(Azure Active Directory 기반 계정)으로 연결해야 합니다.
    * Microsoft 계정(예: @outlook.com, @live.com)을 사용하는 경우 [여기에 나오는 단계에 따라](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal) Azure 계정으로 회사 또는 학교 전자 메일 주소를 만들 수 있습니다.
* 온-프레미스 데이터 게이트웨이가 [로컬 컴퓨터에 설치](app-service-logic-gateway-install.md)되어 있어야 합니다.
* 게이트웨이가 다른 Azure 온-프레미스 데이터 게이트웨이에 의해 요청되면 안 됩니다([아래 2단계의 생성에 따라 클레임 발생](#2-create-an-azure-on-premises-data-gateway-resource)). 단일 설치는 하나의 게이트웨이 리소스에만 연결될 수 있습니다.

## 연결 설치 및 구성

### 1\. 온-프레미스 데이터 게이트웨이 설치

온-프레미스 데이터 게이트웨이 설치에 대한 자세한 내용은 [이 문서](app-service-logic-gateway-install.md)에서 찾을 수 있습니다. 나머지 단계를 계속하기 전에 게이트웨이를 온-프레미스 컴퓨터에 설치해야 합니다.

### 2\. Azure 온-프레미스 데이터 게이트웨이 리소스 만들기

설치되면 Azure 구독을 온-프레미스 데이터 게이트웨이에 연결해야 합니다.

1. 게이트웨이 설치 중에 사용된 동일한 회사 또는 학교 전자 메일 주소를 사용하여 Azure에 로그인합니다.
1. **새로 만들기** 리소스 단추를 클릭합니다.
1. **온-프레미스 데이터 게이트웨이**를 검색 및 선택합니다.
1. 게이트웨이를 계정과 연결하기 위한 정보를 모두 기입합니다. 여기에는 적합한 **설치 이름**을 선택하는 것도 포함됩니다.

    ![온-프레미스 데이터 게이트웨이 연결][1]
1. **만들기** 단추를 클릭하여 리소스를 만듭니다.

### 3\. 디자이너에서 논리 앱 연결 만들기

Azure 구독이 온-프레미스 데이터 게이트웨이 인스턴스와 연결되었으므로 논리 앱 내에서 해당 연결을 만들 수 있습니다.

1. 논리 앱을 열고 온-프레미스 연결을 지원하는 커넥터(이 문서 작성 당시에는 SQL Server)를 선택합니다.
1. **온-프레미스 데이터 게이트웨이를 통해 연결** 확인란을 선택합니다.

    ![논리 앱 디자이너 게이트웨이 만들기][2]
1. 연결할 **게이트웨이**를 선택하고 필요한 기타 연결 정보를 완료합니다.
1. **만들기**를 클릭하여 연결을 만듭니다.

이제 논리 앱에서 사용할 수 있게 연결이 구성되었습니다.

## 다음 단계
- [논리 앱에 대한 일반적인 예제 및 시나리오](app-service-logic-examples-and-scenarios.md)
- [엔터프라이즈 통합 기능](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG

<!---HONumber=AcomDC_0713_2016-->