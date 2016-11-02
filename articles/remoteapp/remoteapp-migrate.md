
<properties
    pageTitle="Azure RemoteApp에서 사용자 데이터 마이그레이션 | Microsoft Azure"
    description="Azure RemoteApp 내부/외부로 사용자 데이터를 마이그레이션하는 방법을 알아봅니다."
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
    ms.date="08/15/2016"
    ms.author="elizapo" />



# Azure RemoteApp 내부/외부로 데이터를 마이그레이션하는 방법

> [AZURE.IMPORTANT]
Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.

[사용자 데이터](remoteapp-upd.md)를 Azure RemoteApp 내부/외부로 전송하기 위해 여러 다양한 도구 및 방법을 사용할 수 있습니다. 몇 가지 방법은 다음과 같습니다.

- 클립보드 공유를 사용하여 복사 및 붙여넣기
- 파일 및 데이터를 파일 서버에 복사
- 브라우저를 통해 비즈니스용 OneDrive에 파일 복사
- 리디렉션을 사용하여 파일 복사

>[AZURE.NOTE] 
비즈니스용 OneDrive 또는 소비자 동기화 에이전트는 사용하도록 설정할 수 없습니다. 이러한 두 에이전트는 Azure RemoteApp에서 [지원되지 않습니다](remoteapp-onedrive.md).

## 파일 탐색기에서 복사 및 붙여넣기 사용

클립보드를 사용하는 복사 및 붙여넣기는 [기본적으로](remoteapp-redirection.md) RemoteApp 배포에서 사용되도록 설정되어 있습니다. 따라서 로컬 PC와 RemoteApp 앱 간에 파일을 복사할 수 있습니다. 종종 RemoteApp에서 앱을 사용하는 일반적인 과정을 통해 파일이 UPD에 저장됩니다. 따라서 RemoteApp에서 데이터를 쉽게 이동할 수 있습니다.

1. RemoteApp 컬렉션에서 [파일 탐색기를 앱으로 게시](remoteapp-publish.md)합니다. (이 작업은 관리 작업입니다.)
2. 사용자에게 게시한 파일 탐색기 앱을 시작하고 자신의 UPD 내부/외부로 파일을 복사하고 붙여넣는 데 사용하도록 지시합니다.

## 표준 네트워크 파일 복사를 사용하여 파일 서버에 파일 및 데이터 업로드

조직은 종종 파일 서버를 사용하여 일반 데이터를 저장합니다. 서버 이름이나 위치를 알고 있는 경우 사용자는 로컬 네트워크에서 서버를 검색한 다음, 위에 나온 것처럼 파일을 복사할 수 있습니다. 다시 RemoteApp에 파일 탐색기를 게시하고 사용자와 공유할 수 있습니다.

>[AZURE.NOTE] 
파일 서버는 RemoteApp이 배포된 라우팅 가능 네트워크에 있어야 합니다.

## 비즈니스용 OneDrive에 파일 복사
RemoteApp에서 비즈니스용 OneDrive 동기화 에이전트를 사용하도록 설정할 수 없으나 여전히 브라우저를 통해.UPD의 파일을 비즈니스용 OneDrive에 복사할 수 있습니다.

1. 파일 탐색기를 RemoteApp에 게시하고 사용자에게 해당 앱을 통해 파일에 액세스하도록 지시할 수 있습니다.
2. 파일은 압축했을 때 전송하기가 가장 쉬우므로 사용자는 비즈니스용 OneDrive로 이동할 모든 파일을 포함하는 .zip 파일을 만들어야 합니다.
3. 사용자에게 Office 365 포털로 이동한 후 OneDrive로 간 다음 .zip 파일을 업로드하도록 요청합니다.

## 드라이브 리디렉션을 사용하여 파일 복사

[드라이브 리디렉션](remoteapp-redirection.md)을 사용하도록 설정했으면 사용자에 대해 매핑된 드라이브를 이미 만들었을 것입니다. 이 경우 리디렉션된 드라이브에 파일을 zip으로 압축한 다음 로컬 PC에 저장할 수 있습니다.

<!---HONumber=AcomDC_0817_2016-->