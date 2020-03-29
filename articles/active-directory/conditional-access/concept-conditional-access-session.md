---
title: 조건부 액세스 정책의 세션 제어 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 세션 컨트롤이란 무엇입니까?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671897"
---
# <a name="conditional-access-session"></a>조건부 액세스: 세션

조건부 액세스 정책 내에서 관리자는 세션 컨트롤을 사용하여 특정 클라우드 응용 프로그램 내에서 제한된 환경을 활성화할 수 있습니다.

![다단계 인증이 필요한 권한 부여 컨트롤이 있는 조건부 액세스 정책](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>응용 프로그램 적용 제한

조직은 이 컨트롤을 사용하여 Azure AD가 선택한 클라우드 앱에 장치 정보를 전달하도록 요구할 수 있습니다. 디바이스 정보를 사용하면 클라우드 앱이 규정 준수 또는 도메인 조인 디바이스에서 연결이 초기화되는지 여부를 알 수 있습니다. 이 컨트롤은 선택한 클라우드 앱으로 SharePoint Online 및 Exchange Online만 지원합니다. 선택하면 클라우드 앱에서는 디바이스 정보를 사용하여 사용자에게 디바이스 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.

앱 적용 제한의 사용 및 구성에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [SharePoint Online을 사용하여 제한된 액세스를 사용하도록 설정](/sharepoint/control-access-from-unmanaged-devices)
- [Exchange Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>조건부 액세스 응용 프로그램 제어

조건부 액세스 앱 제어는 역방향 프록시 아키텍처를 사용하며 Azure AD 조건부 액세스와 고유하게 통합됩니다. Azure AD 조건부 액세스를 사용하면 특정 조건에 따라 조직의 앱에 대한 액세스 제어를 적용할 수 있습니다. 조건은 조건부 액세스 정책이 누구에게(사용자 또는 사용자 그룹), 무엇에(클라우드 앱), 어디에(위치 및 네트워크) 적용되는지 정의합니다. 조건을 결정한 후 사용자를 [Microsoft Cloud App Security로](/cloud-app-security/what-is-cloud-app-security) 라우팅하여 액세스 및 세션 컨트롤을 적용하여 조건부 액세스 앱 제어를 통해 데이터를 보호할 수 있습니다.

조건부 액세스 앱 제어를 통해 사용자는 액세스 및 세션 정책에 따라 실시간으로 앱 액세스 및 세션을 모니터링하고 제어할 수 있습니다. 액세스 및 세션 정책은 Cloud App Security 포털에서 사용되어 필터를 자세히 구체화하고 사용자에 대해 수행할 작업을 설정합니다. 액세스 및 세션 정책을 사용하면 다음을 수행할 수 있습니다.

- 데이터 유출 방지: 관리되지 않는 장치와 같은 중요한 문서의 다운로드, 잘라내기, 복사 및 인쇄를 차단할 수 있습니다.
- 다운로드 시 보호: 중요한 문서의 다운로드를 차단하는 대신 Azure 정보 보호를 사용하여 문서의 레이블을 지정하고 보호하도록 요구할 수 있습니다. 이렇게 하면 문서가 보호되고 잠재적으로 위험한 세션에서 사용자 액세스가 제한됩니다.
- 레이블이 지정되지 않은 파일 업로드 방지: 다른 사용자가 중요한 파일을 업로드, 배포 및 사용하려면 파일에 올바른 레이블과 보호 기능이 있는지 확인하는 것이 중요합니다. 중요한 콘텐츠가 포함된 레이블이 지정되지 않은 파일은 사용자가 콘텐츠를 분류할 때까지 업로드가 차단되도록 할 수 있습니다.
- 사용자 세션의 준수 여부를 모니터링: 위험한 사용자가 앱에 로그인할 때 모니터링되고 세션 내에서 해당 작업이 기록됩니다. 사용자 동작을 조사하고 분석하여 나중에 세션 정책을 적용해야 하는 위치와 조건을 이해할 수 있습니다.
- 액세스 차단: 여러 위험 요소에 따라 특정 앱 및 사용자에 대한 액세스를 세부적으로 차단할 수 있습니다. 예를 들어 클라이언트 인증서를 장치 관리의 한 형태로 사용하는 경우 차단할 수 있습니다.
- 사용자 지정 활동 차단: 일부 앱에는 Microsoft Teams 또는 Slack과 같은 앱에서 중요한 콘텐츠가 포함된 메시지를 보내는 등의 위험이 있는 고유한 시나리오가 있습니다. 이러한 종류의 시나리오에서는 메시지를 검색하여 중요한 콘텐츠를 검색하고 실시간으로 차단할 수 있습니다.

자세한 내용은 [주요 앱에 대한 조건부 액세스 앱 제어 배포](/cloud-app-security/proxy-deployment-aad)문서를 참조하십시오.

## <a name="sign-in-frequency-preview"></a>로그인 빈도(미리 보기)

로그인 빈도는 사용자가 리소스에 액세스하려고 할 때 다시 로그인하라는 메시지가 표시되기 전의 기간을 정의합니다.

로그인 빈도 설정은 표준에 따라 OAUTH2 또는 OIDC 프로토콜을 구현한 앱과 함께 작동합니다. 다음 웹 응용 프로그램을 포함하여 Windows, Mac 및 모바일용 대부분의 Microsoft 기본 앱은 설정을 준수합니다.

- 워드, 엑셀, 파워 포인트 온라인
- 원노트 온라인
- Office.com
- O365 관리 포털
- Exchange Online
- SharePoint 및 OneDrive
- 팀 웹 클라이언트
- Dynamics CRM Online
- Azure portal

자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)문서를 참조 합니다.

## <a name="persistent-browser-session-preview"></a>영구 브라우저 세션(미리 보기)

영구 브라우저 세션을 사용하면 브라우저 창을 닫고 다시 연 후에도 로그인 상태를 유지할 수 있습니다.

자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)문서를 참조 합니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)
