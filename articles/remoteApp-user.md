<properties title="Add a user in RemoteApp" pageTitle="RemoteApp에서 사용자 추가" description="Remoteapp에서 사용자를 추가 하는 방법에 알아봅니다." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#RemoteApp에서 사용자를 추가하는 방법
 
먼저 사용자에게 액세스 권한을 부여해야 사용자가 RemoteApp에서 앱을 보고 사용할 수 있습니다. 이 작업은 간단합니다. **사용자 액세스** 탭에서 이 서비스에 대한 액세스 권한을 부여할 사용자의 계정 정보를 입력하면 됩니다.

어떤 계정 정보가 필요할까요? 필요한 계정 정보는 만든 컬렉션 유형(클라우드 또는 하이브리드) 및 해당 컬렉션에서 Office 365 ProPlus를 사용하는지 여부에 따라 달라집니다.

##클라우드 및 하이브리드 사용자 계정 정보
클라우드 컬렉션은 Microsoft 계정과 디렉터리 동기화된 Azure Active Directory 작업 계정(Office 365 계정이기도 함)을 지원합니다. 

하이브리드 컬렉션은 Windows Server Active Directory 배포에서 DirSync와 같은 도구를 사용하여 동기화된 Azure Active Directory 계정만 지원합니다. 특히 암호 동기화 옵션을 사용하여 동기화되거나 AD FS(Active Directory Federation Services) 페더레이션을 구성하여 동기화됩니다. Azure AD 요구 사항에 대한 자세한 내용은 [Azure RemoteApp에 대해 Active Directory 구성](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-ad/)을 참조하세요.

**참고:** Azure Active Directory 사용자는 구독과 연결된 테넌트의 사용자여야 합니다. 포털의 **설정** 탭에서 구독을 보고 수정할 수 있습니다. 자세한 내용은 [RemoteApp에서 사용되는 Azure Active Directory 테넌트 변경](http://msdn.microsoft.com/ko-kr/3d6c4fd1-c981-4c57-9402-59fe31b11883)을 참조하세요.

##Office 365 ProPlus 사용자 계정 정보
컬렉션에서 Office 365 ProPlus 템플릿 이미지를 사용하는 경우 *또는* Office 365를 사용하는 사용자 지정 이미지를 만든 경우 Office 365 구독이 있는 Azure Active Directory 사용자만 구독의 기본 도메인에 대해 추가할 수 있습니다. 자세한 내용은 [Azure RemoteApp과 함께 Office 365 사용](http://azure.microsoft.com/ko-kr/documentation/articles/remoteapp-o365/)을 참조하세요.

<!--HONumber=35.2-->
