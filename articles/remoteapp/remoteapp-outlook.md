<properties
    pageTitle="Azure RemoteApp에서 Outlook 사용 | Microsoft Azure" 
    description="Azure RemoteApp에서 Outlook을 구성 및 사용하는 방법 알아보기 | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/18/2016"
    ms.author="elizapo" />

# Azure RemoteApp에서 Microsoft Outlook 사용

Azure RemoteApp은 Microsoft Outlook O365를 지원합니다. [Azure RemoteApp에서 Office가 작동하는 방식](remoteapp-officesubscription.md)에 대해 자세히 알아보세요. Azure RemoteApp에서 사용하는 경우 Outlook에 대한 몇 가지 권장되는 설정이 있습니다.

## 캐시 모드
캐시 모드는 Azure RemoteApp에서 Outlook을 사용할 때 권장되는 구성입니다. 캐시된 Exchange 모드를 사용하도록 Outlook 2013 계정을 구성하는 경우 Outlook 2013은 OAB(오프라인 주소록)와 함께 사용자 컴퓨터의 오프라인 데이터 파일(OST 파일)에 저장된 사용자의 Microsoft Exchange 사서함 로컬 복사본에서 작동합니다. 캐시된 사서함 및 OAB는 O365 서비스에서 주기적으로 업데이트됩니다. [캐시 및 온라인 모드의 차이점](https://technet.microsoft.com/library/jj683103.aspx)에 대해 자세히 알아보세요.

사용자는 계정 설정 중 또는 계정 설정을 변경하여 **캐시된 Exchange 모드** 또는 **온라인 모드**를 선택할 수 있습니다. 또한 Office 사용자 지정 도구(OCT) 또는 그룹 정책을 사용하여 둘 중 하나의 모드를 배포할 수 있습니다.

[캐시 모드를 활성화하는 단계별 지침](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc)을 확인하세요.

## 검색
Azure RemoteApp에서 Outlook 내의 검색을 사용하는 데는 제한 사항이 있습니다. Azure RemoteApp은 사용자 세션을 수용하기 위해 풀링된 VM을 사용합니다. 검색 인덱싱은 VM마다 다른 컴퓨터 ID에 따라 달라집니다. 사용자가 Azure RemoteApp에 로그인할 때마다 새 VM에 전달될 수 있습니다. 따라서 로컬 검색을 사용하는 경우 컴퓨터 ID가 변경될 때마다 인덱서가 실행됩니다(사용자가 다른 VM에 있는 경우). .OST 파일의 크기에 따라 인덱서를 완료하는 데 시간이 오래 걸릴 수 있으며 다른 앱에 필요한 리소스까지도 소진할 수 있습니다. 검색 속도도 느리고 결과가 생성되지 않을 수도 있습니다. 이 문제를 해결하는 방법은 기본적으로 온라인 검색을 사용하도록 설정하는 것입니다. 아쉽게도 Outlook 2013에서는 기본적으로 인덱싱된/로컬 검색을 비활성화할 수 없으며 온라인 검색을 활성화할 수 없습니다.

Outlook 2016은 Exchange 2016에서 호스트(또는 Office 365에서 호스트)되는 사서함에 대해 새로운 온라인 검색 환경을 제공하여 이를 해결하는 솔루션을 포함합니다. 여기서는 로컬 캐시(OST)에 대해 서버 검색 결과를 사용합니다. Outlook은 일부 시나리오에서 검색 인덱서를 사용하도록 대체할 수 있으나 대부분의 검색은 온라인 모드를 사용합니다. 메일 검색이 매우 중요한 시나리오인 경우 Azure RemoteApp에서 권장되는 방법은 Outlook 2016을 사용하는 것입니다.

<!---HONumber=AcomDC_0525_2016-->