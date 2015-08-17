<properties 
    pageTitle="VNET RemoteApp에서 Azure VNET으로 마이그레이션하는 방법"
    description="VNET RemoteApp에서 Azure VNET으로 마이그레이션하는 방법을 알아봅니다." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />



# RemoteApp VNET에서 Azure VNET으로 하이브리드 컬렉션을 마이그레이션하는 방법

기쁘게도 RemoteApp별 VNET(가상 네트워크)을 만드는 대신 기존 Azure VNET에 하이브리드 RemoteApp 컬렉션을 직접 배포하도록 허용했습니다. 이렇게 하면 최신 VNET 기능(예: Express 경로)을 활용하고 하이브리드 컬렉션에서 해당 VNET에 배포된 다른 Azure 서비스 및 가상 컴퓨터에 네트워크로 직접 액세스할 수 있습니다. 따라서 VNET 간 구성보다 성능이 더 우수하고 더 쉽게 설정할 수 있습니다.


*OriginalCollection*이라는 하이브리드 RemoteApp 컬렉션과 *RemoteAppVNET*이라는 RemoteApp VNET을 이미 만들었다고 가정합니다. 이 컬렉션을 *AzureVNET*이라는 새 Azure VNET에 마이그레이션하는 단계는 다음과 같습니다.

1.	[관리 포털](http://manage.windowsazure.com/)의 **네트워크** 탭에서, *AzureVNET* 서브넷 중 하나 이상에 대해 *RemoteAppVNET*에 사용한 것과 동일한 위치, DNS 구성 및 주소 공간을 사용하여 *AzureVNET*이라는 VNET을 만듭니다.
2.	*OriginalCollection* 도메인에 가입된 Active Directory 배포에 대한 네트워크 연결을 설정하거나 호스트하도록 *AzureVNET*을 구성합니다.
3.	**RemoteApps** 탭에서 *New Collection*이라는 새 RemoteApp 컬렉션을 만듭니다. 이때 **빠른 생성** 대신 **VNET으로 만들기** 옵션을 사용합니다.
3.	*AzureVNET*의 서브넷에 배포하도록 *NewCollection*을 구성합니다.
4.	*OriginalCollection*에 사용된 것과 동일한 이미지와 도메인 가입 정보를 사용하도록 *NewCollection*을 구성합니다.
5.	몇 시간 후 *NewCollection*이 컬렉션 목록에 활성 상태로 표시됩니다.

이제 원본 컬렉션에서 새 컬렉션으로 사용자 정보를 마이그레이션할 필요가 없는 경우 다음 단계를 수행합니다.

6.	*OriginalCollection*을 삭제합니다.
7.	*RemoteAppVNET*을 삭제합니다.

완료되었습니다!

또는 원본 컬렉션에서 새 컬렉션으로 사용자 정보를 마이그레이션해야 하는 경우 다음 단계를 수행합니다.

6.	Azure 구독 ID, 원본 컬렉션 이름, 새 컬렉션 이름을 사용하여 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration)에 메일을 보내서, 사용자 정보를 마이그레이션하도록 요청합니다.
7.	업무일 기준 2일 이내에 RemoteApp 팀사용자 액세스 목록과 모든 사용자 문서 및 사용자 설정을 원본 컬렉션에서 새 컬렉션으로 이동합니다.
8.	*OriginalCollection*을 삭제합니다.
9.	*RemoteAppVNET*을 삭제합니다.

완료되었습니다!

궁금한 사항이 있거나 특별한 지원이 필요한 경우 [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help)으로 메일을 보내세요.
 

<!---HONumber=August15_HO6-->