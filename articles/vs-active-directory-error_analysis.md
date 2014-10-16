<properties title="Error During Authentication Detection" pageTitle="Error During Authentication Detection" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

#### 인증 검색 중 오류 발생

이전 인증 코드를 검색하는 동안 마법사에서 호환되지 않는 인증 유형을 검색했습니다.

###### 무엇을 확인합니까?

마법사에서는 이전 버전의 Visual Studio를 사용하여 구성된 인증 코드의 버전을 감지하려고 합니다. 이 오류가 발생한 경우에는 인증 코드가 호환되지 않음을 의미합니다. 마법사에서는 이전 버전의 Visual Studio에서 다음과 같은 인증 유형을 감지합니다.

-   Windows 인증
-   개별 사용자 계정
-   조직 계정

자세한 내용은 [Azure AD의 인증 시나리오][Azure AD의 인증 시나리오]를 참조하세요.

  [Azure AD의 인증 시나리오]: http://msdn.microsoft.com/library/azure/dn499820.aspx
