
<properties
    pageTitle="Azure RemoteApp에 대한 사용자 지정 이미지 업로드 | Microsoft Azure"
    description="Azure RemoteApp에 대한 사용자 지정 이미지를 업로드하는 방법을 알아봅니다."
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
    ms.author="ericor" />



# Azure RemoteApp에 대한 사용자 지정 이미지 업로드

사용자 지정 템플릿 이미지를 만들거나 변경 내용으로 업데이트했으므로, Azure RemoteApp 이미지 라이브러리에 해당 이미지를 업로드해야 합니다. 다음 단계를 사용합니다.


## 시작하기 전에

1.      사용자 지정 이미지가 [이미지 요구사항](remoteapp-imagereqs.md) 및 [응용 프로그램 요구사항](remoteapp-appreqs.md)을 충족하는지 확인합니다.
2.      [Azure PowerShell 모듈](../powershell-install-configure.md)을 설치합니다.

## 사용자 지정 이미지를 업로드하는 방법에 단계별 가이드

1.      Azure 관리 포털을 열고 RemoteApp 페이지로 이동합니다.
2.      **템플릿 이미지** 탭에서 페이지의 맨 아래에 있는 **업로드**를 클릭합니다.
4.      이미지의 이름으로 친숙한 이름을 입력하고 저장소 계정 위치를 지정합니다. 위치는 RemoteApp 컬렉션과 동일한 위치 또는 컬렉션을 만들려는 위치인지 확인합니다.
5.      메시지가 표시되면 로컬 PC에 스크립트를 다운로드합니다.
6.      텍스트 상자의 명령 매개 변수를 클립보드에 복사합니다.
7.      관리자 권한 Windows PowerShell 창을 엽니다.
8.      관리자 권한 Windows PowerShell 창에서 스크립트를 다운로드한 동일한 디렉터리로 이동합니다.
9.      복사된 명령을 붙여놓고 **Enter**를 누릅니다.

	업로드 프로세스가 시작되고 네트워크 속도 및 이미지 크기를 포함한 여러 요인에 따라 업로드 시간이 달라질 수 있습니다.

11.    네트워크 중단 또는 그와 유사한 작업으로 인해 업로드가 실패하면, 시작했던 업로드 프로세스를 항상 다시 시작할 수 있습니다. 업로드를 다시 시작하려면 동일한 명령줄을 사용하여 다시 스크립트를 실행합니다.

> [AZURE.WARNING] 업로드 스크립트를 수정하지 마십시오. 이미지가 이미지 요구 사항 및 응용 프로그램 요구사항을 충족하는지 확인하도록 특정 검사가 구현됩니다.

## 일반적인 문제

- Azure PowerShell이 아닌 Windows PowerShell을 사용해야 합니다. 업로드 프로세스 중 특정 모듈이 필요하기 때문에 Azure PowerShell 모듈을 설치해야 합니다.
- 스크립트를 변경하지 않아야 하며, 유효성 검사는 사용자 편의를 위한 것입니다.
- 업로드 중 Vhd 파일이 잠기면, 파일을 복사하거나 새 위치로 이동하여 다시 업로드해봅니다. 업로드를 방지하는 일부 Windows 프로세스가 있을 수 있습니다.

<!---HONumber=AcomDC_0629_2016-->