---
title: 인증 및 권한 부여 문제 해결-Azure Event Hubs
description: 이 문서에서는 Azure Event Hubs 인증 및 권한 부여 문제를 해결 하는 방법에 대 한 정보를 제공 합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1c053f60c877cdd26655948c37ab81a5e4d61cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322419"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>인증 및 권한 부여 문제 해결-Azure Event Hubs
[연결 문제 해결](troubleshooting-guide.md) 문서에서는 Azure Event Hubs 연결 문제 해결에 대 한 팁을 제공 합니다. 이 문서에서는 Azure Event Hubs의 인증 및 권한 부여 문제를 해결 하기 위한 팁과 권장 사항을 제공 합니다. 

## <a name="if-you-are-using-azure-active-directory"></a>Azure Active Directory를 사용 하는 경우
Azure AD (Azure Active Directory)를 사용 하 여 Azure Event Hubs 인증 하 고 권한을 부여 하는 경우, event hub에 액세스 하는 id가 올바른 **리소스 범위** (소비자 그룹, 이벤트 허브, 네임 스페이스, 리소스 그룹 또는 구독)에서 올바른 **RBAC (역할 기반 액세스 제어) 역할** 의 멤버 인지 확인 합니다.

### <a name="rbac-roles"></a>RBAC 역할
- [Azure Event Hubs 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) 는 Event Hubs 리소스에 대 한 전체 액세스 권한을 가집니다.
- 전송 액세스를 위한 [Azure Event Hubs 데이터 발신자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) 입니다.
- 수신 액세스를 위한 [Azure Event Hubs 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) 입니다.

### <a name="resource-scopes"></a>리소스 범위
- **소비자 그룹**:이 범위에서 역할 할당은이 엔터티에만 적용 됩니다. 현재 Azure Portal는이 수준에서 보안 주체에 RBAC 역할을 할당 하는 것을 지원 하지 않습니다. 
- **이벤트 허브**: 역할 할당은 이벤트 허브 엔터티와 그 아래에 있는 소비자 그룹에 적용 됩니다.
- **네임 스페이스**: 역할 할당은 네임 스페이스에 있는 Event Hubs의 전체 토폴로지 및 이와 연결 된 소비자 그룹에 적용 됩니다.
- **리소스 그룹**: 역할 할당은 리소스 그룹 아래의 모든 Event Hubs 리소스에 적용 됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 Event Hubs 리소스에 적용 됩니다.

자세한 내용은 다음 항목을 참조하세요.

- [Azure Active Directory를 사용 하 여 응용 프로그램을 인증 Event Hubs 리소스에 액세스](authenticate-application.md)
- [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>SAS (공유 액세스 서명)를 사용 하는 경우
[SAS](authenticate-shared-access-signature.md)를 사용 하는 경우 다음 단계를 수행 합니다. 

- 사용 중인 SAS 키가 올바른지 확인 하세요. 그렇지 않으면 올바른 SAS 키를 사용 합니다.
- 키에 올바른 사용 권한 (보내기, 받기 또는 관리)이 있는지 확인 합니다. 그렇지 않은 경우 필요한 권한이 있는 키를 사용 합니다. 
- 키가 만료 되었는지 확인 합니다. 만료 되기 전에 SAS를 갱신 하는 것이 좋습니다. 클라이언트와 Event Hubs 서비스 노드 사이에 클록 오차가 있는 경우 클라이언트에서 인식 하기 전에 인증 토큰이 만료 될 수 있습니다. 현재 구현 계정 클록 속도는 5 분이 됩니다. 즉, 클라이언트는 만료 되기까지 5 분 전에 토큰을 갱신 합니다. 따라서 클록 오차가 5 분 보다 큰 경우 클라이언트는 간헐적인 인증 오류를 관찰할 수 있습니다.
- **SAS 시작 시간이** **지금**으로 설정 된 경우 클럭 오차 (다른 컴퓨터에서 현재 시간의 차이)로 인해 처음 몇 분 동안 간헐적 오류가 표시 될 수 있습니다. 시작 시간을 15 분 이상으로 설정 하거나 전혀 설정 하지 마세요. 이는 만료 시간에도 일반적으로 적용 됩니다. 

자세한 내용은 다음 항목을 참조하세요. 

- [SAS (공유 액세스 서명)를 사용 하 여 인증](authenticate-shared-access-signature.md)합니다. 
- [공유 액세스 서명을 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

* [연결 문제 해결](troubleshooting-guide.md)
