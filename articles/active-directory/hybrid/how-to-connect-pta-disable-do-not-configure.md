---
title: Azure AD 연결을 사용할 때 PTA를 사용하지 않도록 설정 "구성 안 함" | 마이크로 소프트 문서
description: 이 문서에서는 Azure AD Connect "구성 안 함" 기능을 사용하여 PTA를 사용하지 않도록 설정하는 방법에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726801"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Azure AD Connect를 사용할 때 PTA 를 사용하지 않도록 설정 "구성 안 함"

Azure AD Connect를 사용하여 통과 인증을 사용하고 있고 "구성 안 함"으로 설정한 경우 사용하지 않도록 설정할 수 있습니다. PTA 를 비활성화하면 다음 cmdlet을 사용하여 수행할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항
필요한 필수 구성 요소는 다음과 같습니다.
- PTA 에이전트가 설치된 모든 Windows 컴퓨터입니다. 
- 에이전트는 버전 1.5.1742.0 이상이어야 합니다. 
- PtA를 사용하지 않도록 설정하는 PowerShell cmdlet을 실행하기 위한 Azure 글로벌 관리자 계정입니다.

>[!NOTE]
> 에이전트가 오래된 경우 이 작업을 완료하는 데 필요한 cmdlet이 없을 수 있습니다. Azure Portal에서 새 에이전트를 Windows 컴퓨터에 설치하고 관리자 자격 증명을 제공할 수 있습니다. (에이전트 를 설치해도 클라우드의 PTA 상태에 는 영향을 미치지 않습니다.)

> [!IMPORTANT]
> Azure 정부 클라우드를 사용하는 경우 다음 값으로 ENVIRONMENTNAME 매개 변수를 전달해야 합니다. 
>
>| 환경 이름 | 클라우드 |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>PTA를 사용하지 않도록 설정하려면
PowerShell 세션 내에서 다음을 사용하여 PTA를 사용하지 않도록 설정합니다.
1. PS C:\프로그램 파일\마이크로소프트 Azure AD 연결 인증 에이전트>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` 또는 `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` 또는 `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>에이전트에 액세스할 수 없는 경우

에이전트 컴퓨터가 없는 경우 다음 명령을 사용하여 에이전트를 설치할 수 있습니다.

1. portal.azure.com 최신 인증 에이전트를 다운로드합니다.
2. 이 기능을 `.\AADConnectAuthAgentSetup.exe` 설치합니다.`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 통과 인증으로 사용자 로그인](how-to-connect-pta.md)