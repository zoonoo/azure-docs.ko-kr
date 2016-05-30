<properties 
	pageTitle="BizTalk 서비스의 발급자 이름 및 발급자 키 | Microsoft Azure" 
	description="BizTalk 서비스에서 서비스 버스 또는 액세스 제어(ACS)에 대한 발급자 이름 및 발급자 키를 검색하는 방법에 대해 알아봅니다. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/16/2016" 
	ms.author="mandia"/>




# BizTalk 서비스: 발급자 이름 및 발급자 키

Azure BizTalk 서비스는 서비스 버스 발급자 이름 및 발급자 키와 액세스 제어 발급자 이름 및 발급자 키를 사용합니다. 구체적으로 살펴보면 다음과 같습니다.

작업 | 발급자 이름 및 발급자 키
--- | ---
Visual Studio에서 응용 프로그램 배포 | 액세스 제어 발급자 이름 및 발급자 키
Azure BizTalk 서비스 포털 구성 | 액세스 제어 발급자 이름 및 발급자 키
Visual Studio에서 BizTalk 어댑터 서비스로 LOB 릴레이 만들기 | 서비스 버스 발급자 이름 및 발급자 키

이 항목에서는 발급자 이름 및 발급자 키를 검색하는 단계를 나열합니다.

## 액세스 제어 발급자 이름 및 발급자 키
액세스 제어 발급자 이름 및 발급자 키는 다음에 사용됩니다.

- Visual Studio에서 만든 Azure BizTalk 서비스 응용 프로그램: Visual Studio의 BizTalk 서비스 응용 프로그램을 Azure에 배포하려면 액세스 제어 발급자 이름 및 발급자 키를 입력합니다. 
- Azure BizTalk 서비스 포털: BizTalk 서비스를 만들고 BizTalk 서비스 포털에 로그인하면 배포를 위해 액세스 제어 발급자 이름 및 액세스 제어 발급자 키가 동일한 액세스 제어 값으로 자동으로 등록됩니다.

### 액세스 제어 발급자 이름 및 발급자 키를 복사하여 붙여 넣으려면

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **BizTalk 서비스**를 선택합니다.
3. BizTalk 서비스를 선택합니다. 
4. 작업 표시줄에서 **연결 정보**를 선택합니다. 액세스 제어 네임스페이스, 기본 발급자(발급자 이름) 및 기본 키(발급자 키)가 나열되며 이러한 항목은 복사하여 붙여넣을 수 있습니다.  

요약하면 다음과 같습니다.발급자 이름 = 기본 발급자 발급자 키 = 기본 키


**ACS 관리 포털 열기**를 선택하여 액세스 제어 값을 가져올 수도 있습니다.

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **BizTalk 서비스**를 선택합니다.
3. BizTalk 서비스를 선택합니다.
4. 연결 정보 단추를 선택하고 **ACS 관리 포털 열기**를 선택합니다.
5. 포털의 **서비스 설정**에서 **서비스 ID**를 선택합니다. 그러면 액세스 제어 발급자 이름 값인 서비스 ID가 표시됩니다. 서비스 ID 링크를 선택하여 발급자 키 값인 암호를 표시합니다. 이 값은 복사할 수 있습니다.<br/><br/> 예를 들어, **서비스 ID**에 "owner"가 표시됩니다. "Owner"는 액세스 제어 발급자 이름입니다. "owner" 링크를 클릭하면 **암호**가 표시됩니다. "암호" 링크를 클릭하면 값이 표시됩니다. 이 암호 값은 액세스 제어 발급자 키입니다.  

요약하면 다음과 같습니다.발급자 이름 = 서비스 ID 이름 발급자 키 = 암호 값

왼쪽 탐색 창에서 **Active Directory**를 선택하여 액세스 제어 값을 검색할 수도 있습니다.

> [AZURE.IMPORTANT] **Active Directory**를 사용하여 액세스 제어 네임스페이스를 만들 때 서비스 ID는 자동으로 만들어지지 **않습니다**. BizTalk 서비스를 프로비전할 때 액세스 제어 네임스페이스, "owner"라는 서비스 ID(발급자 이름), 암호(발급자 키) 및 대칭 키가 자동으로 만들어집니다.<br /> [방법: ACS 관리 서비스를 사용하여 서비스 ID 구성](http://go.microsoft.com/fwlink/p/?LinkID=303942)에서 액세스 제어 서비스 ID에 대한 자세한 정보를 제공합니다.


## 서비스 버스 발급자 이름 및 발급자 키
서비스 버스 발급자 이름 및 발급자 키는 BizTalk 어댑터 서비스에 사용됩니다. Visual Studio의 BizTalk 서비스 프로젝트에서 BizTalk 어댑터 서비스를 사용하여 온-프레미스 LOB(기간 업무) 시스템에 연결합니다. 연결하려면 LOB 릴레이를 만들고 LOB 시스템 세부 정보를 입력합니다. 이 작업을 수행할 때에도 서비스 버스 발급자 이름 및 발급자 키를 입력합니다.

### 서비스 버스 발급자 이름 및 발급자 키를 검색하려면

1. [Azure 클래식 포털](http://go.microsoft.com/fwlink/p/?LinkID=213885)에 로그인합니다.
2. 왼쪽 탐색 창에서 **Service Bus**를 선택합니다.
3. 네임스페이스를 선택합니다. 작업 표시줄에서 **연결 정보**를 선택합니다. 그러면 **기본 발급자**(발급자 이름) 및 **기본 키**(발급자 키)가 표시됩니다. 이 값은 복사할 수 있습니다.  

요약하면 다음과 같습니다.발급자 이름 = 기본 발급자 발급자 키 = 기본 키

## 다음
추가 Azure BizTalk 서비스 항목:

-  [Azure BizTalk 서비스 SDK 설치](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [자습서: Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Azure BizTalk 서비스 SDK로 시작하는 방법](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Azure BizTalk 서비스](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>


## 참고 항목
-  [방법: ACS 관리 서비스를 사용하여 서비스 ID 구성](http://go.microsoft.com/fwlink/p/?LinkID=303942)<br/>
- [BizTalk 서비스: Developer, Basic, Standard 및 Premium Editions 차트](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk 서비스: Azure 클래식 포털을 사용하여 프로비전](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk 서비스: 프로비저닝 상태 차트](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk 서비스: 대시보드, 모니터 및 크기 조정 탭](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk 서비스: 백업 및 복원](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk 서비스: 제한](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
 

<!---HONumber=AcomDC_0518_2016-->