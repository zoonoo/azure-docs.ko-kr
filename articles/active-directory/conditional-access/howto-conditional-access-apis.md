---
title: 조건부 액세스 Api 및 PowerShell-Azure Active Directory
description: Azure AD 조건부 액세스 Api 및 PowerShell을 사용 하 여 코드와 같은 정책 관리
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: videor, jeevanb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33f3766e02316eccb519bea15246541531750ed1
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90008028"
---
# <a name="conditional-access-programmatic-access"></a>조건부 액세스: 프로그래밍 방식 액세스

대부분의 조직에서는 코드와 같이 최대한 많은 환경을 관리 해야 하는 필요성을 표현 했습니다. Microsoft Graph 사용 하 여 사용자 환경에서 다른 코드 조각과 같은 조건부 액세스 정책을 처리할 수 있습니다.

Microsoft Graph는 조직에서 Microsoft 365, Windows 10 및 Enterprise Mobility + Security의 데이터와 상호 작용 하는 데 사용할 수 있는 통합 프로그래밍 모델을 제공 합니다. Microsoft Graph에 대 한 자세한 내용은 [Microsoft Graph 개요](/graph/overview)문서를 참조 하세요.

![그래프의 일부인 기본 리소스 및 관계를 보여 주는 이미지](./media/howto-conditional-access-apis/microsoft-graph.png)

다음 예는 지원 되지 않는 그대로 제공 됩니다. 이러한 예제를 조직의 도구에 대 한 기반으로 사용할 수 있습니다. 

다음의 많은 예제에서는 [관리 되는 id](../managed-identities-azure-resources/overview.md), [Logic Apps](../../logic-apps/logic-apps-overview.md), [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage), [팀](https://www.microsoft.com/microsoft-365/microsoft-teams/group-chat-software/), [Azure Key Vault](../../key-vault/general/overview.md)등의 도구를 사용 합니다.

## <a name="configure"></a>구성

### <a name="powershell"></a>PowerShell

많은 관리자의 경우 PowerShell은 이미 이해 된 스크립팅 도구입니다. 다음 예제에서는 [AZURE AD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD) 을 사용 하 여 조건부 액세스 정책을 관리 하는 방법을 보여 줍니다.

- [Azure AD PowerShell 명령을 사용 하 여 조건부 액세스 정책 구성](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/powershell)

### <a name="graph-api"></a>그래프 API

이 예제에서는 조건부 액세스 그래프 Api에서 사용할 수 있는 기본 CRUD (만들기, 읽기, 업데이트 및 삭제) 옵션을 보여 줍니다. 예제에는 몇 가지 샘플 정책을 만드는 데 사용할 수 있는 몇 가지 JSON 템플릿도 포함 되어 있습니다.

- [Microsoft Graph API 호출을 사용 하 여 조건부 액세스 정책 구성](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/graphapi)

### <a name="configure-using-templates"></a>템플릿을 사용 하 여 구성

조건부 액세스 Api를 사용 하 여 사전 프로덕션 환경에서 템플릿을 사용 하 여 조건부 액세스 정책을 배포할 수 있습니다.

- [Microsoft Graph API 템플릿을 사용 하 여 조건부 액세스 정책 구성](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/01-configure/templates)

## <a name="test"></a>테스트

이 예제에서는 프로덕션 환경과 같이 사전 프로덕션 등의 한 환경에서 조건부 액세스 정책을 복사할 수 있는 승인 워크플로를 사용 하 여 더 안전한 배포 사례를 모델링 합니다.

- [테스트 환경에서 조건부 액세스 정책 승격](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/02-test)

## <a name="deploy"></a>배포

이 예제에서는 사용자 인구에 대 한 단계적 배포 조건부 액세스 정책을 점차적으로 수행 하는 메커니즘을 제공 하 여 지원 영향과 조기에 발생 하는 문제를 조기에 파악할 수 있습니다.

- [승인 워크플로를 사용 하 여 프로덕션 환경에 조건부 액세스 정책 배포](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/03-deploy)

## <a name="monitor"></a>Monitor

이 예제에서는 시간에 따라 조건부 액세스 정책 변경을 모니터링 하 고 키 정책이 변경 될 때 경고를 트리거할 수 있는 메커니즘을 제공 합니다.

- [변경 내용에 대 한 배포 된 조건부 액세스 정책을 모니터링 하 고 경고를 트리거합니다.](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/04-monitor)

## <a name="manage"></a>관리

### <a name="backup-and-restore"></a>백업 및 복원

이 예제를 사용 하 여 팀의 승인을 통해 조건부 액세스 정책의 백업 및 복원을 자동화 합니다.

- [Microsoft Graph API 호출을 사용 하 여 조건부 액세스 정책의 백업 및 복원 프로세스 관리](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/01-backup-restore)

### <a name="emergency-access-accounts"></a>응급 액세스 계정

여러 관리자가 조건부 액세스 정책을 만들 수 있으며 해당 정책에 대 한 제외로 [응급 액세스 계정을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) 추가 하는 것을 잊은 경우 이 예에서는 지정 된 응급 액세스 계정을 포함 하도록 모든 정책이 업데이트 되도록 합니다.

- [Microsoft Graph API 호출을 사용 하 여 조건부 액세스 정책에 대 한 응급 액세스 계정 할당 관리](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/02-emergency-access)

### <a name="contingency-planning"></a>대체 계획

이러한 상황이 발생 하는 경우에는 작업을 계속할 수 있는 상태로 돌아갈 수 있는 방법이 필요 합니다. 다음 예에서는 정책을 알려진 좋은 대체 계획으로 되돌리고 다른 조건부 액세스 정책을 사용 하지 않도록 설정 하는 방법을 제공 합니다.

- [Microsoft Graph API 호출을 사용 하 여 조건부 액세스 대체 정책 활성화 관리](https://github.com/Azure-Samples/azure-ad-conditional-access-apis/tree/main/05-manage/03-contingency)

## <a name="community-contribution"></a>커뮤니티 기여

이러한 샘플은 [GitHub 리포지토리에서](https://github.com/Azure-Samples/azure-ad-conditional-access-apis)사용할 수 있습니다. 커뮤니티 기여에 대 한 상세한 GitHub 문제 및 끌어오기 요청을 지원 하기 위해 노력 하 고 있습니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft Graph 개요](/graph/overview)

- [조건부 액세스 API](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-1.0)

- [명명 된 위치 API](/graph/api/resources/namedlocation?view=graph-rest-1.0)
