<properties
    pageTitle="Azure RemoteApp에서 App-V 앱 사용|Microsoft Azure"
    description="Azure RemoteApp에서 App-V 앱 사용 방법 배우기"
    services="remoteapp"
	documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/12/2016" 
    ms.author="elizapo" />



# Azure RemoteApp에서 App-V 앱 사용

Azure RemoteApp 하이브리드 컬렉션에서 App-V 응용 프로그램을 사용할 수 있으며, 이때 도메인에 가입해야 합니다.

시작하기 전에 최신 업데이트를 통해 App-V 5.1 클라이언트를 설치해야 합니다. App-V 클라이언트가 포함된 [사용자 지정 이미지](remoteapp-create-custom-image.md)를 만들어야 합니다.

Azure RemoteApp를 통해 기존 App-V 인프라를 쉽게 사용할 수 있습니다. 하이브리드 컬렉션은 도메인 컨트롤러에 대한 액세스 권한이 있는 Azure VNET에 배포되고 VM이 도메인에 가입되므로, 기존 App-V 인프라 및 배포 방법을 이용하여 Azure RemoteApp에서 App-V 응용 프로그램을 쉽게 호스팅할 수 있습니다. 다음은 현재 가지고 있는 App-V 배포 유형에 따라 알고 있어야 하는 몇 가지 고려사항입니다.

| 구성 옵션 | | Positive | Negative |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| 배달 방법 | 스트리밍(주문형) | 앱은 언제나 최신의 새 앱임 | 첫 번째 대기 시간 |
| | 탑재됨 | 가장 빠름; 앱이 VM에 이미 있음 | Bloat - 이미지 공간을 차지함(127 GB 한계) |
| 앱 위치 저장소 | 공유 콘텐츠 | 앱이 Azure RemoteApp 인스턴스의 메모리에서 실행됨 | 앱이 상주하는 스트리밍(파일) 서버에 메모리 및 양호한 연결이 소비됨 |
| | 디스크(캐시됨) | 고속 실행. 앱이 콘텐츠 원본의 가용성에 의존하지 않음 | Bloat - 이미지 공간을 차지함(127 GB 한계) |
| 대상 지정 | 사용자 | 전체 독립 실행형 App-V 인프라가 필요함 | |
| | 글로벌(컴퓨터) | 게시 서버를 사용하여 미리 게시 또는 대상 지정 | 앱(대용량)을 업데이트하려면 Azure 이미지를 업데이트해야 합니다. 이미지의 일부 공간을 차지합니다. |

 사용자 지정 이미지 및 하이브리드 컬렉션을 만든 후 응용 프로그램을 게시하고, 사용자를 할당하고, 어디서나 임의 장치에 배달된 Azure RemoteApp에서 호스팅되는 기존 App-V 응용 프로그램을 즐길 수 있습니다.

<!---HONumber=AcomDC_0629_2016-->