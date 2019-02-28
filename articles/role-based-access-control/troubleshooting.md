---
title: Azure 리소스에 대한 RBAC 문제 해결 | Microsoft Docs
description: Azure 리소스에 대한 RBAC(역할 기반 액세스 제어) 문제를 해결합니다.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 7b27c811214def7f5646f886b955d035a50c0725
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342476"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Azure 리소스에 대한 RBAC 문제 해결

이 문서에서는 Azure 리소스에 대한 RBAC(역할 기반 액세스 제어)와 관련된 일반적인 질문에 대답합니다. Azure Portal의 역할을 사용할 때 예상되는 동작을 이해하고 액세스 문제를 해결하는 데 도움이 될 것입니다.

## <a name="problems-with-rbac-role-assignments"></a>RBAC 역할 할당 관련 문제

- **역할 할당 추가** 옵션이 비활성화되었거나 권한 오류로 인해 역할 할당을 추가할 수 없는 경우 역할을 할당하려는 범위에서 `Microsoft.Authorization/roleAssignments/*` 권한이 있는 역할을 사용하고 있는지 확인하세요. 이 권한이 없으면 구독 관리자에게 문의하세요.
- 리소스를 만들려고 할 때 권한 오류가 발생하면 선택한 범위에서 리소스를 만들기 위한 권한이 있는 역할을 사용하고 있는지 확인하세요. 예를 들어, 참가자여야 할 수 있습니다. 이 권한이 없으면 구독 관리자에게 문의하세요.
- 지원 티켓을 만들거나 업데이트하려고 할 때 권한 오류가 발생하면 `Microsoft.Support/*` 권한이 있는 역할(예: [지원 요청 참가자](built-in-roles.md#support-request-contributor))을 사용하고 있는지 확인하세요.
- 역할을 할당하려고 할 때 역할 할당 수가 초과되었다는 오류가 발생하면 그룹에 역할을 할당하여 역할 할당 수를 줄여보세요. Azure는 구독당 최대 **2000**개의 역할 할당을 지원합니다.

## <a name="problems-with-custom-roles"></a>사용자 지정 역할의 문제

- 기존 사용자 지정 역할을 업데이트할 수 없는 경우 `Microsoft.Authorization/roleDefinition/write` 권한이 있는지 여부를 확인합니다.
- 기존 사용자 지정 역할을 업데이트할 수 없는 경우 테넌트에서 하나 이상의 할당 가능한 범위를 삭제했는지 여부를 확인합니다. 또한 사용자 지정 역할의 `AssignableScopes` 속성은 [사용자 지정 역할을 생성, 삭제, 업데이트 또는 볼 수 있는 사용자](custom-roles.md#who-can-create-delete-update-or-view-a-custom-role)를 제어합니다.
- 새 역할을 만들려고 시도하면 역할 정의 제한을 초과했다는 오류가 발생하는 경우 사용하지 않은 사용자 지정 역할을 삭제합니다. 기존 사용자 지정 역할을 통합하거나 다시 사용하려고 할 수도 있습니다. Azure는 테넌트에서 최대 **2000**개의 사용자 지정 역할을 지원합니다.
- 사용자 지정 역할을 삭제할 수 없는 경우 하나 이상의 역할 할당이 해당 사용자 지정 역할을 아직도 사용하는지 여부를 확인합니다.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>테넌트에서 구독이 이동될 때 RBAC 복구

- 구독을 다른 테넌트로 양도하는 방법에 대한 단계가 필요한 경우 [Azure 구독의 소유권을 다른 계정으로 양도](../billing/billing-subscription-transfer.md)를 참조하세요.
- 다른 테넌트에 구독을 전송하는 경우 모든 역할 할당이 원본 테넌트에서 영구적으로 삭제되고 대상 테넌트로 마이그레이션되지 않습니다. 대상 테넌트에서 역할 할당을 다시 만들어야 합니다.
- 전역 관리자이고 구독에 액세스할 수 없는 경우 **Azure 리소스에 대한 액세스 관리** 토글을 사용하여 일시적으로 [액세스 권한을 상승](elevate-access-global-admin.md)하여 구독에 대한 액세스 권한을 얻으면 됩니다.

## <a name="rbac-changes-are-not-being-detected"></a>RBAC 변경 내용이 인식되지 않음

Azure Resource Manager는 경우에 따라 성능 향상을 위해 구성 및 데이터를 캐시합니다. 역할 할당을 만들거나 삭제하는 경우 변경 내용이 적용되는 데 최대 30분이 걸릴 수 있습니다. Azure Portal, Azure PowerShell 또는 Azure CLI를 사용하는 경우 로그아웃 및 로그인하여 역할 할당 변경 내용을 강제로 새로 고칠 수 있습니다. REST API 호출을 사용하여 역할 할당을 변경하는 경우 액세스 토큰을 새로 고쳐 강제로 새로 고칠 수 있습니다.

## <a name="web-app-features-that-require-write-access"></a>쓰기 액세스 권한이 필요한 웹앱 기능

사용자에게 단일 웹앱에 대한 읽기 전용 액세스를 부여하면 예상치 않게 일부 기능을 사용하지 못할 수 있습니다. 다음 관리 기능을 사용하려면 참여자나 소유자에게 제공되는 웹앱에 대한 **쓰기** 권한이 필요하며, 읽기 전용 시나리오에서는 이러한 기능을 사용할 수 없습니다.

* 시작, 중지 등의 명령
* 일반 구성, 규모 설정, 백업 설정, 모니터링 설정 등의 설정 변경
* 게시 자격 증명과 앱 설정, 연결 문자열 등의 기타 암호 액세스
* 스트리밍 로그
* 진단 로그 구성
* 콘솔(명령 프롬프트)
* 활성 및 최근 배포(로컬 Git 연속 배포의 경우)
* 예상 소요 시간
* 웹 테스트
* 가상 네트워크(쓰기 권한이 있는 사용자가 이전에 가상 네트워크를 구성한 경우에만 읽기 권한자에게 표시됨)

이러한 타일에 액세스할 수 없는 경우 관리자에게 웹앱에 대한 참가자 권한을 요청해야 합니다.

## <a name="web-app-resources-that-require-write-access"></a>쓰기 액세스 권한이 필요한 웹앱 리소스

웹앱에서 몇 가지 리소스가 상호 연동되는 경우 웹앱의 구조가 복잡해집니다. 아래에는 웹 사이트 몇 개가 포함된 일반적인 리소스 그룹이 나와 있습니다.

![웹앱 리소스 그룹](./media/troubleshooting/website-resource-model.png)

따라서 사용자에게 웹앱에 대한 권한만 부여하면 Azure Portal에서 웹 사이트 블레이드의 기능을 대부분 사용할 수 없게 됩니다.

다음 항목을 사용하려면 웹 사이트에 해당하는 **App Service 계획**에 대한 **쓰기** 권한이 필요합니다.  

* 웹앱의 가격 책정 계층 보기(무료 또는 표준)  
* 크기 조정 구성(인스턴스 수, 가상 머신 크기, 자동 크기 조정 설정)  
* 할당량(저장소, 대역폭, CPU)  

다음 항목을 사용하려면 웹 사이트를 포함하는 전체 **리소스 그룹**에 대한 **쓰기** 권한이 필요합니다.  

* SSL 인증서 및 바인딩(SSL 인증서는 같은 리소스 그룹과 지리적 위치의 사이트 간에 공유될 수 있음)  
* 경고 규칙  
* 자동 크기 조정 설정  
* Application Insights 구성 요소  
* 웹 테스트  

## <a name="virtual-machine-features-that-require-write-access"></a>쓰기 액세스 권한이 필요한 가상 머신 기능

웹앱과 마찬가지로 가상 머신 블레이드의 일부 기능 역시 가상 머신 또는 리소스 그룹의 기타 리소스에 대한 쓰기 권한이 있어야 사용할 수 있습니다.

가상 머신은 도메인 이름, 가상 네트워크, 저장소 계정 및 경고 규칙과 관련이 있습니다.

다음 항목을 사용하려면 **가상 머신**에 대한 **쓰기** 권한이 필요합니다.

* 엔드포인트  
* IP 주소  
* 디스크  
* 확장  

다음 항목을 사용하려면 **가상 머신**와 가상 머신이 속한 **리소스 그룹**(도메인 이름 포함) 둘 다에 대한 **쓰기** 권한이 필요합니다.  

* 가용성 집합  
* 부하 분산된 집합  
* 경고 규칙  

이러한 타일에 액세스할 수 없는 경우 관리자에게 리소스 그룹에 대한 참가자 권한을 요청합니다.

## <a name="azure-functions-and-write-access"></a>Azure Functions 및 쓰기 액세스

[Azure Functions](../azure-functions/functions-overview.md)의 일부 기능에는 쓰기 액세스 권한이 있어야 합니다. 예를 들어 사용자가 읽기 역할에 할당된 경우 함수 앱 내에서 함수를 볼 수 없습니다. 포털에서는 **(액세스 권한 없음)** 을 표시합니다.

![함수 앱 액세스 권한 없음](./media/troubleshooting/functionapps-noaccess.png)

판독기는 **플랫폼 기능** 탭을 클릭한 다음, **모든 설정**을 클릭하여 함수 앱(웹앱과 유사)에 관련된 일부 설정을 볼 수 있지만 이러한 설정을 수정할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 액세스 관리](role-assignments-portal.md)
* [Azure 리소스에 대한 RBAC 변경 내용의 활동 로그 보기](change-history-report.md)

