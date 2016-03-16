<properties
   pageTitle="Azure AD Connect 동기화: 디렉터리 확장 | Microsoft Azure"
   description="이 항목에서는 Azure AD Connect의 디렉터리 확장 기능을 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect 동기화: 디렉터리 확장
디렉터리 확장을 사용하면 온-프레미스 Active Directory의 사용자 고유 특성을 사용하여 Azure AD에서 스키마를 확장할 수 있습니다. 이를 통해 온-프레미스를 계속 관리하는 특성을 이용하는 LOB 앱을 빌드할 수 있습니다. 이러한 특성은 [Azure AD Graph 디렉터리 확장](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions) 또는 [Microsoft Graph](https://graph.microsoft.io/)를 통해 이용할 수 있습니다. 각각 [Azure AD Graph 탐색기](https://graphexplorer.cloudapp.net) 및 [Microsoft Graph 탐색기](https://graphexplorer2.azurewebsites.net/)를 통해 사용할 수 있는 특성을 볼 수 있습니다.

현재 이 특성을 이용하는 Office 365 워크로드가 없습니다.

설치 마법사의 사용자 지정 설정 경로에서 동기화할 추가 속성을 구성합니다. ![스키마 확장 마법사](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png) 설치에 다음과 같은 특성이 표시됩니다. 이러한 특성은 유효한 후보입니다.

- 사용자 및 그룹 개체 유형
- 단일 값 특성
- 문자열, 정수, 이진

개체에는 최대 100개의 디렉터리 확장 특성이 있을 수 있습니다. 최대 길이는 250자입니다. 특성 값이 더 긴 경우 동기화 엔진에 의해 잘립니다.

Azure AD Connect를 설치하는 동안 이러한 특성을 사용할 수 있는 응용 프로그램이 등록됩니다. Azure 포털에서 다음 응용 프로그램을 볼 수 있습니다. ![스키마 확장 앱](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

이제 이 특성은 Graph를 통해 사용할 수 있습니다. ![그래프](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

특성은 extension\_{AppClientId}\_를 접두사로 사용합니다. AppClientId는 Azure AD 디렉터리의 모든 특성에 대해 동일한 값을 갖습니다.

## 다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0218_2016-->