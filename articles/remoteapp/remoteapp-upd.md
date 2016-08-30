
<properties 
    pageTitle="Azure RemoteApp이 사용자 데이터와 설정을 저장하는 방법 | Microsoft Azure"
	description="Azure RemoteApp에서 사용자 프로필 디스크를 사용하여 사용자 데이터를 저장하는 방법을 알아봅니다."
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

# Azure RemoteApp이 사용자 데이터와 설정을 저장하는 방법

> [AZURE.IMPORTANT]
Azure RemoteApp은 중단되었습니다. 자세한 내용은 [알림](https://go.microsoft.com/fwlink/?linkid=821148)을 읽어보세요.

Azure RemoteApp은 장치와 세션에서 사용자 ID 및 사용자 지정을 저장합니다. 이 사용자 데이터는 사용자 프로필 디스크(UPD)라고 하는 사용자별 컬렉션 디스크에 저장됩니다. 디스크는 사용자를 따르며 사용자가 로그인한 위치와 관계 없이 일관된 환경을 보장합니다.

사용자 프로필 디스크는 사용자에게 완전히 투명합니다. 사용자는 자신의 문서 폴더에 문서를 저장하고(로컬 드라이브처럼 표시된) 일반적인 방법으로 해당 앱 설정을 변경합니다. 동시에 모든 장치에서 Azure RemoteApp에 연결할 때 모든 개인 설정이 유지됩니다. 사용자가 볼 수 있는 것은 같은 위치의 해당 데이터입니다.

각 UPD에는 50GB의 영구 저장소가 있으며 사용자 데이터와 응용 프로그램 설정 모두를 포함합니다.

사용자 프로필 데이터에 대한 구체적인 내용을 읽습니다.

>[AZURE.NOTE] UPD를 비활성화해야 하나요? 바로 가능합니다. 자세한 내용은 Pavithra의 블로그 게시물 [Azure RemoteApp에서 UPD(사용자 프로필 디스크) 비활성화](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)를 참조하세요.


## 관리자는 데이터를 어떻게 얻을 수 있습니까?

사용자 중 하나에 대한 데이터에 액세스해야 하는 경우(재해 복구 또는 사용자가 회사를 떠날 경우), Azure 지원에 컬렉션 및 사용자 ID에 대한 구독 정보를 제공합니다. Azure RemoteApp 팀에서 VHD의 URL을 제공합니다. 해당 VHD를 다운로드하고 필요한 문서 또는 파일을 검색합니다. VHD는 50GB이므로 다운로드하는 데 다소 시간이 걸립니다.


## 데이터를 백업했습니까?

예, 지리적 위치별로 사용자 데이터의 백업을 저장합니다. 주 데이터 센터가 다운된 경우, 데이터는 읽기 전용 및 일반 데이터처럼 동일한 방식으로 액세스할 수 있습니다(Azure RemoteApp에 문의하여 가져옵니다). 데이터는 실시간으로 백업 위치에 복사되며 다른 버전의 사본을 유지하지 않습니다. 따라서 데이터가 손상된 경우 이전에 알려진 버전으로 복원할 수 없지만 주 데이터 센터의 작동이 중단되면 다른 위치에서 사용자 데이터를 가져올 수 있습니다.

## 사용자가 서버측에서 어떻게 UPD를 볼 수 있습니까?

각 사용자는 자신의 UPD: c:\\Users\\username에 매핑되는 서버에 자신의 디렉터리를 갖습니다.

## UPD 및 Outlook을 사용하는 가장 좋은 방법은 무엇입니까?

Azure RemoteApp은 세션 간에 Outlook 상태(사서함, PST)를 저장합니다. 이 기능을 사용하려면, PST를 사용자 프로필 데이터(c:\\users<사용자 이름>)에 저장해야 합니다. 데이터에 대한 기본 위치이며, 위치를 변경하지 않으면 데이터는 세션 간에 유지됩니다.

Outlook에서 "캐시" 모드를 사용하고 검색을 위해 "서버/온라인" 모드를 사용하는 것이 좋습니다.

Outlook 및 Azure RemoteApp 사용에 대한 자세한 내용은 [이 문서](remoteapp-outlook.md)를 참조하세요.

## 리디렉션은 어떻습니까?
[리디렉션](remoteapp-redirection.md)을 설정하여 사용자가 로컬 장치에 액세스할 수 있도록 Azure RemoteApp을 구성할 수 있습니다. 로컬 장치는 UPD의 데이터에 액세스할 수 있습니다.

## 내 UPD를 네트워크 공유로 사용할 수 있습니까?
아니요. UPD를 네트워크 공유로 사용할 수 없습니다. 사용자가 Azure RemoteApp에 연결된 경우에만 UPD를 사용자가 사용할 수 있습니다.

## 컬렉션에서 사용자를 삭제하면 해당 UPD가 삭제됩니까?

아니요, 사용자를 삭제하는 경우 UPD를 자동으로 삭제하지 않으며, 대신 컬렉션을 삭제할 때까지 데이터를 저장합니다. 컬렉션을 삭제 한 후 90일 동안 모든 UPD를 삭제합니다.

컬렉션에서 UPD를 삭제해야 할 경우 Azure RemoteApp에 문의하여 우리쪽에서 UPD를 삭제할 수 있습니다.

## 내 사용자의 UPD(현재 또는 삭제된 사용자)에 액세스할 수 있습니까?

예, [Azure RemoteApp](mailto:remoteappforum@microsoft.com)에 문의하면 데이터에 액세스하기 위한 URL을 설정할 수 있습니다. 액세스가 만료되기 전에 UPD에서 모든 데이터나 파일을 다운로드하려면 약 10시간이 소요됩니다.

## UPD를 오프라인으로 사용할 수 있습니까?

지금 당장은 UPD에 대한 오프라인 액세스를 제공하지 않습니다. 위에서 설명한 10시간이 넘어야 합니다. 감사를 위해 데이터에 액세스하거나 UPD에서 백신 소프트웨어를 실행하는 것과 같이 좀 더 복잡한 작업을 완료하는 데 충분한 액세스 시간을 제공하는 방법이 현재 없음을 의미합니다.

## 레지스트리 키 설정이 유지됩니까?
예, HKEY\_Current\_User로 기록된 내용은 UPD의 일부입니다.

## 컬렉션에 대한 UPD를 비활성화할 수 있습니까?

예, 구독에 UPD를 사용하지 않도록 Azure RemoteApp을 요청할 수 있지만 직접 수행할 수 없습니다. 즉, UPD는 구독의 모든 컬렉션에 대해 사용할 수 없습니다.

다음 상황 중 하나에서 UPD를 비활성화할 수 있습니다.

- 사용자 데이터에 대한 완전한 액세스 및 제어가 필요합니다(금융 기관처럼 감사 및 검토 목적으로).
- 타사 사용자 프로필 관리 솔루션이 온-프레미스에 있고 도메인에 가입된 Azure RemoteApp 배포에서 계속 사용하고 싶습니다. 골드 이미지에 프로필 에이전트가 로드되어야 합니다.
- 로컬 데이터 저장소가 필요하지 않으며 모든 데이터를 클라우드 또는 파일 공유로 유지하고 Azure RemoteApp을 사용하여 데이터를 로컬로 저장하는 것을 제어하려고 합니다.

자세한 내용은 [Azure RemoteApp에서 UPD(사용자 프로필 디스크) 비활성화](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)를 참조하세요.

## 시스템 드라이브에 데이터를 저장하지 않도록 사용자를 제한할 수 있습니까?

예, 하지만 컬렉션을 만들기 전에 템플릿 이미지에 설정해야 합니다. 시스템 드라이브에 대한 액세스를 차단하려면 다음 단계를 사용합니다.

1. 템플릿 이미지에서 **gpedit.msc**를 실행합니다.
2. **사용자 구성 > 관리 템플릿 > Windows 구성 요소 > 탐색기**로 이동합니다.
3. 다음 옵션을 선택합니다.
	- **내 컴퓨터에 지정된 드라이브 숨기기**
	- **내 컴퓨터에서 드라이브에 대한 액세스 방지**

## UPD를 시드할 수 있습니까? 사용자가 처음 로그인할 때 사용할 수 있는 UPD에 일부 데이터를 배치하려고 합니다.

예, 템플릿 이미지를 만들 때 기본 프로필에 정보를 추가할 수 있습니다. 해당 정보는 UPD에 추가됩니다.

## 저장할 데이터 크기에 따라 UPD의 크기를 변경할 수 있습니까?

아니요. 모든 UPD에는 50GB의 저장소가 있습니다. 다양한 양의 데이터를 저장하려는 경우 다음을 시도합니다.

1. 컬렉션에 대한 UPD를 비활성화할 수 있습니다.
2. 사용자가 액세스할 파일 공유를 설정합니다.
3. 시작 스크립트를 사용하여 파일 공유를 로드합니다. Azure RemoteApp의 시작 스크립트에 대한 자세한 내용은 아래를 참조하세요.
4. 사용자에 모든 데이터를 파일 공유에 저장하도록 지시합니다.


## Azure RemoteApp의 시작 스크립트를 어떻게 실행합니까?

시작 스크립트를 실행하려는 경우, 컬렉션에 대해 사용하려는 템플릿 이미지에 예약된 작업을 만들어 시작합니다. (sysprep를 실행하기 *전에* 이를 수행합니다.)

![시스템 작업 만들기](./media/remoteapp-upd/upd1.png)

![사용자가 로그온할 때 실행되는 시스템 작업 만들기](./media/remoteapp-upd/upd2.png)

**일반** 탭에서 보안 아래 **사용자 계정**을 "BUILTIN\\Users"로 변경해야 합니다.

![사용자 계정을 그룹으로 변경](./media/remoteapp-upd/upd4.png)

예약된 작업은 사용자의 자격 증명을 사용하여 시작 스크립트를 시작합니다. 사용자가 로그온할 때마다 작업을 실행하도록 예약합니다.

!["로그온 시" 작업에 대한 트리거 설정](./media/remoteapp-upd/upd3.png)

[그룹 정책 기반 시작 스크립트](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx)를 사용할 수도 있습니다.

## 시작 메뉴에서 시작 스크립트를 어떻게 배치합니까? 작동되나요?

즉, config window 스크립트를 실행하는 .bat 파일을 만들고 c:\\ProgramData\\Microsoft\\Windows\\Start Menu\\Programs\\StartUp 폴더에 저장한 다음, 사용자가 RemoteApp 세션을 시작할 때마다 해당 스크립트를 실행할 수 있습니까?

아니요, Azure RemoteApp에서 지원되지 않으며, 시작 메뉴에서 시작 스크립트를 지원하지 않는 RDSH를 사용합니다.

## mstsc.exe(원격 데스크톱 프로그램)를 사용하여 로그온 스크립트를 구성할 수 있습니까?

아니요, Azure RemoteApp에서 지원되지 않습니다.

## VM에 데이터를 로컬로 저장할 수 있나요?

아니요, UPD 이외의 VM에서 임의 위치에 저장된 데이터가 손실됩니다. 사용자가 다음에 Azure RemoteApp에 로그인할 때 동일한 VM을 얻지 못할 가능성이 높습니다. 사용자-VM의 지속성을 유지하지 않으므로 사용자가 동일한 VM에 로그인하지 않고 데이터가 손실됩니다. 또한 컬렉션을 업데이트할 때 기존 VM이 새 VM 집합으로 바뀝니다. 따라서 VM 자체에 저장된 데이터가 손실됩니다. 권장되는 방법은 UPD, Azure Files과 같은 공유 저장소, VNET 내부의 파일 서버, DropBox와 같은 클라우드 저장소 시스템을 사용하는 클라우드에 데이터를 저장하는 것입니다.

## PowerShell을 사용하여 VM에 Azure File 공유를 탑재하려면 어떻게 하나요?

Net-PSDrive cmdlet을 사용하여 다음과 같이 드라이브를 탑재할 수 있습니다.

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \<storage-account-name>.file.core.windows.net<share-name> -Credential :<storage-account-name>


또한 다음을 실행하여 자격 증명을 저장할 수도 있습니다.

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


이렇게 하면 New-PSDrive cmdlet에서 -Credential 매개 변수를 건너뛸 수 있습니다.

<!---HONumber=AcomDC_0817_2016-->