---
title: Azure 자동화에서 역할 기반 액세스 제어 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어)를 통해 Azure 리소스에 대한 액세스 관리가 가능합니다. 이 문서에서는 Azure 자동화에서 RBAC를 설정하는 방법을 설명합니다.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn
keywords: 자동화 rbac, 역할 기반 액세스 제어, azure rbac

ms.service: automation
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/12/2016
ms.author: magoedte;sngun

---
# Azure 자동화의 역할 기반 액세스 제어
## 역할 기반 액세스 제어
RBAC(역할 기반 액세스 제어)를 통해 Azure 리소스에 대한 액세스 관리가 가능합니다. [RBAC](../active-directory/role-based-access-control-configure.md)를 사용하여 팀 내에서 업무를 분리하고 사용자, 그룹 및 응용 프로그램에 해당 작업을 수행하는 데에만 필요한 권한을 부여할 수 있습니다. Azure 포털, Azure 명령줄 도구 또는 Azure 관리 API를 사용하여 사용자에게 역할 기반 액세스를 부여할 수 있습니다.

## 자동화 계정의 RBAC
Azure 자동화의 자동화 계정 범위에서 사용자, 그룹 및 응용 프로그램에 적절한 RBAC 역할을 할당하여 액세스를 허용합니다. 다음은 자동화 계정에서 지원하는 기본 제공 역할입니다.

| **역할** | **설명** |
|:--- |:--- |
| 소유자 |소유자 역할을 사용하면 자동화 계정을 관리하기 위해 다른 사용자, 그룹 및 응용 프로그램에 대한 액세스 권한 제공이 포함된 자동화 계정 내에서 모든 리소스 및 동작에 액세스할 수 있습니다. |
| 참여자 |참가자 역할을 사용하면 자동화 계정에 대한 다른 사용자의 액세스 권한 수정을 제외한 모든 사항을 관리할 수 있습니다. |
| 판독기 |읽기 역할을 사용하면 자동화 계정의 모든 리소스를 볼 수 있지만 변경할 수는 없습니다. |
| 자동화 운영자 |자동화 운영자 역할을 사용하면 작업 시작, 중지, 일시 중단, 다시 시작 및 예약 등의 운영 작업을 수행할 수 있습니다. 이 역할은 자격 증명 자산 및 Runnbook 등의 자동화 계정 리소스를 보거나 수정하지 못하도록 보호하며 조직의 구성원이 이러한 Runbook을 여전히 실행하도록 하려는 경우 유용합니다. |
| 사용자 액세스 관리자 |사용자 액세스 관리자 역할을 사용하면 Azure 자동화 계정에 대한 사용자 액세스 권한을 관리할 수 있습니다. |

> [!NOTE]
> 액세스 권한을 특정 runbook에 부여할 수는 없고, 자동화 계정 내의 리소스 및 작업에만 부여할 수 있습니다.
> 
> 

이 문서에서는 Azure 자동화에서 RBAC를 설치하는 방법을 살펴봅니다. 그러나 자동화 계정에 대한 권한을 부여하기 전에 잘 이해할 수 있도록 먼저 참가자, 독자, 자동화 운영자 및 사용자 액세스 관리자에 부여되는 개별 사용 권한을 자세히 살펴보겠습니다. 그렇지 않으면 의도하지 않거나 원하지 않은 결과가 발생할 수 있습니다.

## 참가자 역할 권한
다음 테이블에서는 자동화의 참가자 역할에서 수행할 수 있는 특정 작업을 표시합니다.

| **리소스 종류** | **읽기** | **쓰기** | **삭제** | **다른 작업** |
|:--- |:--- |:--- |:--- |:--- |
| Azure 자동화 계정 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 인증서 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 연결 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 연결 형식 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 자격 증명 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 일정 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 변수 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 필요한 상태 구성 | | | |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |
| Hybrid Runbook Worker 리소스 종류 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure 자동화 작업 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |
| 자동화 작업 스트림 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 작업 일정 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| 자동화 모듈 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |
| Azure 자동화 Runbook |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |
| 자동화 Runbook 초안 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |
| 자동화 Runbook 초안 테스트 작업 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |
| 자동화 Webhook |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |

## 읽기 역할 권한
다음 테이블에서는 자동화의 읽기 역할에서 수행할 수 있는 특정 작업을 표시합니다.

| **리소스 종류** | **읽기** | **쓰기** | **삭제** | **다른 작업** |
|:--- |:--- |:--- |:--- |:--- |
| 클래식 구독 관리자 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 관리 잠금 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 사용 권한 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 공급자 작업 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 역할 할당 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 역할 정의 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |

## 자동화 운영자 역할 권한
다음 테이블에서는 자동화의 자동화 운영자 역할에서 수행할 수 있는 특정 작업을 표시합니다.

| **리소스 종류** | **읽기** | **쓰기** | **삭제** | **다른 작업** |
|:--- |:--- |:--- |:--- |:--- |
| Azure 자동화 계정 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 인증서 자산 | | | | |
| 자동화 연결 자산 | | | | |
| 자동화 연결 형식 자산 | | | | |
| 자동화 자격 증명 자산 | | | | |
| 자동화 일정 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | |
| 자동화 변수 자산 | | | | |
| 자동화 필요한 상태 구성 | | | | |
| Hybrid Runbook Worker 리소스 종류 | | | | |
| Azure 자동화 작업 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |
| 자동화 작업 스트림 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 작업 일정 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | |
| 자동화 모듈 | | | | |
| Azure 자동화 Runbook |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 Runbook 초안 | | | | |
| 자동화 Runbook 초안 테스트 작업 | | | | |
| 자동화 Webhook | | | | |

자세한 내용은 [자동화 운영자 작업](../active-directory/role-based-access-built-in-roles.md#automation-operator)에서 자동화 계정 및 해당 리소스의 자동화 운영자 역할이 지원하는 작업을 나열합니다.

## 사용자 액세스 관리자 역할 권한
다음 테이블에서는 자동화의 사용자 액세스 관리자 역할에서 수행할 수 있는 특정 작업을 표시합니다.

| **리소스 종류** | **읽기** | **쓰기** | **삭제** | **다른 작업** |
|:--- |:--- |:--- |:--- |:--- |
| Azure 자동화 계정 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 인증서 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 연결 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 연결 형식 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 자격 증명 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 일정 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 변수 자산 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 필요한 상태 구성 | | | | |
| Hybrid Runbook Worker 리소스 종류 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure 자동화 작업 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 작업 스트림 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 작업 일정 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 모듈 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Azure 자동화 Runbook |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 Runbook 초안 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 Runbook 초안 테스트 작업 |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |
| 자동화 Webhook |![녹색 상태](media/automation-role-based-access-control/green-checkmark.png) | | | |

## Azure 포털을 사용하여 자동화 계정에 대한 RBAC 구성
1. [Azure 포털](https://portal.azure.com/)에 로그인하고 자동화 계정 블레이드에서 자동화 계정을 엽니다.
2. 오른쪽 위 모서리에 있는 **액세스** 컨트롤을 클릭합니다. 그러면 새 사용자, 그룹, 응용 프로그램을 추가하여 자동화 계정을 관리하고 자동화 계정에 구성 가능한 기존 역할을 볼 수 있는 **사용자** 블레이드가 열립니다.
   
   ![액세스 단추](media/automation-role-based-access-control/automation-01-access-button.png)

> [!NOTE]
> **구독 관리자**가 이미 기본 사용자로 존재합니다. 구독 관리자 Active Directory 그룹에 Azure 구독에 대한 서비스 관리자 및 공동 관리자 역할이 포함되어 있습니다. 서비스 관리자는 Azure 구독 및 해당 리소스의 소유자이며 자동화 계정에 대해 상속된 소유자 역할도 가지고 있습니다. 즉, 액세스 권한은 구독의 **서비스 관리자 및 공동 관리자**에게 **상속**되며 나머지 사용자에게는 **할당**됩니다. 권한에 대해 자세히 알아보려면 **구독 관리자**를 클릭하세요.
> 
> 

### 새 사용자 추가 및 역할 할당
1. 사용자 블레이드에서 **추가**를 클릭하여 사용자, 그룹 또는 응용 프로그램을 추가할 수 있는 **액세스 추가 블레이드**를 열고 역할을 할당합니다.
   
   ![사용자 추가](media/automation-role-based-access-control/automation-02-add-user.png)
2. 사용 가능한 역할 목록에서 역할을 선택합니다. 여기서는 **읽기** 역할을 선택하지만 자동화 계정이 지원하는 기본 제공 역할이나 사용자가 정의한 사용자 지정 역할을 선택할 수 있습니다.
   
   ![역할 선택](media/automation-role-based-access-control/automation-03-select-role.png)
3. **사용자 추가**를 클릭하여 **사용자 추가** 블레이드를 엽니다. 구독을 관리하는 사용자, 그룹 또는 응용 프로그램을 추가했다면 해당 사용자가 나열되므로 선택하여 액세스 권한을 추가할 수 있습니다. 사용자가 목록에 없거나 추가하려는 사용자가 목록에 없을 경우 **초대**를 클릭하여 Outlook.com, OneDrive 또는 Xbox Live Id 등의 유효한 Microsoft 계정 메일 주소를 사용하여 사용자를 초대할 수 있는 **게스트 초대** 블레이드를 엽니다. 사용자의 전자 메일 주소를 입력했으면 **선택**을 클릭하여 사용자를 추가한 다음 **확인**을 클릭합니다.
   
   ![사용자 추가](media/automation-role-based-access-control/automation-04-add-users.png)
   
   이제 **읽기** 역할이 할당되어 **사용자** 블레이드에 추가된 사용자를 볼 수 있습니다.
   
   ![사용자 나열](media/automation-role-based-access-control/automation-05-list-users.png)
   
   또한 **역할** 블레이드에서 사용자에게 역할을 할당할 수도 있습니다.
4. 사용자 블레이드에서 **역할**을 클릭하여 **역할 블레이드**를 엽니다. 이 블레이드에서 역할의 이름, 해당 역할에 할당된 사용자 및 그룹의 수를 볼 수 있습니다.
   
    ![사용자 블레이드에서 역할 할당](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
   > [!NOTE]
   > 역할 기반 액세스 제어는 자동화 계정 수준에서만 설정할 수 있으며 자동화 계정 아래의 리소스에는 설정할 수 없습니다.
   > 
   > 
   
    사용자, 그룹 또는 응용 프로그램에 둘 이상의 역할을 할당할 수 있습니다. 예를 들어 사용자에게 **읽기 역할**과 함께 **자동화 운영자** 역할을 추가하는 경우 사용자는 모든 자동화 리소스를 볼 뿐만 아니라 Runbook 작업을 실행할 수 있습니다. 드롭다운을 확장하여 사용자에게 할당된 역할 목록을 볼 수 있습니다.
   
    ![여러 역할 보기](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)

### 사용자 제거
자동화 계정을 관리하지 않거나 더 이상 조직에서 일하지 않는 사용자에 대한 액세스 권한을 제거할 수 있습니다. 다음은 사용자를 제거하는 단계입니다.

1. **사용자** 블레이드에서 제거할 역할 할당을 선택합니다.
2. 할당 세부 정보 블레이드에서 **제거** 단추를 클릭합니다.
3. **예**를 클릭하여 제거를 확인합니다.
   
   ![사용자 제거](media/automation-role-based-access-control/automation-08-remove-users.png)

## 역할이 할당된 사용자
역할을 할당받은 사용자가 자동화 계정에 로그인하면 **기본 디렉터리** 목록에 소유자 계정이 나열된 것을 볼 수 있습니다. 추가된 자동화 계정을 보려면 기본 디렉터리를 소유자의 기본 디렉터리로 전환해야 합니다.

![기본 디렉터리](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### 자동화 운영자 역할에 대한 사용자 환경
자동화 운영자 역할이 할당된 사용자가 할당된 자동화 계정을 볼 때 사용자는 자동화 계정에서 만든 Runbook 목록, Runbook 작업 및 일정을 볼 수 있지만 해당 정의는 볼 수 없습니다. 사용자는 Runbook 작업을 시작, 중지, 일시 중단, 다시 시작 또는 예약할 수 있습니다. 사용자는 구성, Hybrid Worker 그룹 또는 DSC 노드 등의 다른 자동화 리소스에 액세스할 수 없습니다.

![리소스에 대한 액세스 없음](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

사용자가 Runbook을 클릭할 때 자동화 운영자 역할이 액세스를 허용하지 않기 때문에 원본을 보거나 Runbook을 편집하는 명령이 제공되지 않습니다.

![Runbook 편집에 대한 액세스 없음](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

사용자에게 일정을 보고 만드는 액세스 권한이 있지만 다른 자산 형식에는 액세스할 수 없습니다.

![자산에 대한 액세스 없음](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

이 사용자는 Runbook과 연결된 웹 후크를 볼 액세스 권한도 없습니다.

![Webhook에 대한 액세스 없음](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Azure PowerShell을 사용하여 자동화 계정에 대한 RBAC를 구성합니다.
다음 [Azure PowerShell cmdlet](../active-directory/role-based-access-control-manage-access-powershell.md)을 사용하여 자동화 계정에 역할 기반 액세스를 구성할 수도 있습니다.

• [Get AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx)에는 Azure Active Directory에서 사용할 수 있는 모든 RBAC 역할이 나열됩니다. 이 명령과 **이름** 속성을 함께 사용하여 특정 역할에서 수행하는 모든 작업을 나열할 수 있습니다. **예:** ![역할 정의 가져오기](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx)에는 지정된 범위의 Azure AD RBAC 역할 할당이 나열됩니다. 이 명령은 매개 변수 없이 구독에서 할당한 모든 역할 할당을 반환합니다. **ExpandPrincipalGroups** 매개 변수를 사용하여 지정된 사용자에 대한 액세스 할당 뿐만 아니라 사용자가 멤버인 그룹에 대한 액세스 할당을 나열합니다. **예제:** 다음 명령을 사용하여 자동화 계정 안에 있는 모든 사용자와 해당 역할을 나열합니다.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![역할 할당 가져오기](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx)는 특정 범위에서 사용자, 그룹, 응용 프로그램에 액세스 권한을 할당합니다. **예제:** 다음 명령을 사용하여 자동화 계정 범위의 사용자에 "자동화 운영자" 역할을 할당합니다.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![새 역할 할당](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx)를 사용하여 특정 범위에서 지정된 사용자, 그룹 또는 응용 프로그램의 액세스 권한을 제거합니다. **예제:** 다음 명령을 사용하여 자동화 계정 범위의 "자동화 운영자" 역할에서 사용자를 제거합니다.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

위의 예제에서 **로그인 ID**, **구독 ID**, **리소스 그룹 이름** 및 **자동화 계정 이름**을 계정 세부 정보로 바꿉니다. 사용자 역할 할당을 계속 제거하기 전에 확인하라는 메시지가 표시되면 **예**를 선택합니다.

## 다음 단계
* Azure 자동화에 RBAC를 구성하는 다양한 방법에 대한 자세한 내용은 [Azure PowerShell로 RBAC 관리](../active-directory/role-based-access-control-manage-access-powershell.md)를 참조하세요.
* Runbook을 시작하는 다양한 방법에 대한 자세한 내용은 [Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 다른 runbook 형식에 대한 자세한 내용은 [Azure 자동화 Runbook 형식](automation-runbook-types.md)을 참조하세요.

<!---HONumber=AcomDC_0914_2016-->