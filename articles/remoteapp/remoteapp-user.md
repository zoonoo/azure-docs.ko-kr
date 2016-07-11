<properties
    pageTitle="Azure RemoteApp 컬렉션에 사용자 추가 | Microsoft Azure"
    description="Azure RemoteApp 컬렉션에 사용자를 추가하는 방법에 대해 알아봅니다."
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
    ms.date="06/18/2016"
    ms.author="elizapo" />

# Azure RemoteApp 컬렉션에 사용자를 추가하는 방법

먼저 사용자에게 컬렉션에 대한 액세스 권한을 부여해야 사용자가 Azure RemoteApp에서 앱을 보고 사용할 수 있습니다. 이 작업은 간단합니다. **사용자 액세스** 탭에서 사용자의 계정 정보를 입력하고 확인 표시를 클릭하면 됩니다.

어떤 계정 정보가 필요할까요? 필요한 계정 정보는 만든 컬렉션 유형(클라우드 또는 하이브리드) 및 해당 컬렉션에서 Office 365 ProPlus를 사용하는지 여부에 따라 달라집니다.

## 지원되는 사용자 ID

다양한 컬렉션 형식(클라우드와 하이브리드)에서 응용 프로그램에 대한 액세스를 위해 다양한 사용자 ID 사용을 지원합니다.

RemoteApp 하이브리드 컬렉션의 경우 온-프레미스 Active Directory 도메인 인프라와 디렉터리 통합(및 선택적으로 Single Sign-On)이 포함된 Azure Active Directory 테넌트를 설정해야 합니다. 또한 온-프레미스 디렉터리에 일부 Active Directory 개체를 만들어야 합니다.

RemoteApp의 클라우드 컬렉션의 경우, Azure Active Directory 지원 ID가 있는 사용자에게는 Microsoft 계정을 비롯하여 RemoteApp에 대한 사용자 액세스 권한이 부여됩니다. 아래 표를 참조하세요.

Office 365 사용자는 Azure Active Directory 사용자입니다. Azure Active Directory 하이브리드, 디렉터리 동기화된 계정이 있는 경우, RemoteApp 하이브리드 배포에서 사용자 액세스 권한이 부여될 수 있습니다.

이 테이블은 컬렉션에서 지원되는 ID 및 Active Directory 요구 사항에 대한 빠른 참조로 사용할 수 있습니다.

|사용자 계정 |클라우드 |하이브리드|
|--------------|--------|------|
|Microsoft 계정| 	예|	아니요|
|Azure AD(Azure Active Directory)| | |
|Azure AD 클라우드만 해당 |예 |아니요 |
|암호 동기화를 사용하는 ADsync |예 |예 |
|암호 동기화를 사용하지 않는 ADsync|	예 |아니요 |
|AD FS 포함 ADsync |예 |예 |
|[타사 Azure 지원 ID 공급자](https://msdn.microsoft.com/library/azure/jj679342.aspx)(예: Ping) |예 |예|
|Multi-Factor Authentication |예 |예 |

RemoteApp에 대한 Active Directory 구성에 대한 [자세한 내용](remoteapp-ad.md)을 확인하세요.


> [AZURE.NOTE] Azure Active Directory 사용자는 구독과 연결된 테넌트로부터 시작되어야 합니다. (포털의 **설정** 페이지에서 구독을 보고 수정할 수 있습니다. 자세한 내용은 [RemoteApp에서 사용되는 Azure Active Directory 테넌트 변경](remoteapp-changetenant.md)을 참조하세요.)

## Office 365 ProPlus 사용자 계정 정보
컬렉션에서 Office 365 ProPlus 템플릿 이미지를 사용하는 경우 *또는* Office 365를 사용하는 사용자 지정 이미지를 만든 경우 Office 365 구독이 있는 Azure Active Directory 사용자만 구독의 기본 도메인에 대해 추가할 수 있습니다. 자세한 내용은 [Azure RemoteApp과 함께 Office 365 사용](remoteapp-o365.md)을 참조하세요.

<!---HONumber=AcomDC_0629_2016-->