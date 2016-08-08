<properties
	pageTitle="액세스 변경 기록 보고서 만들기 | Microsoft Azure"
	description="지난 90일 동안 역할 기반 액세스 제어와 함께 Azure 구독 액세스에 대한 모든 변경 내용을 나열하는 보고서를 생성합니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="04/28/2016"
	ms.author="kgremban"/>

# 액세스 변경 기록 보고서 만들기

Azure 구독의 단독 소유자, 내부 리소스 및 리소스 그룹이 아닌 경우 모든 액세스 변경 내용을 추적할 수 있어야 합니다. 언제든지 누군가가 구독 내부의 액세스를 부여하거나 취소하면 변경 내용이 Azure 이벤트에 기록됩니다. 지난 90일 동안 모든 변경 내용을 보려면 액세스 변경 기록 보고서를 만들 수 있습니다.

## Azure PowerShell에서 보고서 만들기
PowerShell에서 액세스 변경 기록 보고서를 만들려면 다음 명령을 사용합니다.

```
Get-AzureRMAuthorizationChangeLog
```

다음을 비롯하여 나열하려는 할당의 속성을 지정할 수 있습니다.

| 속성 | 설명 |
| -------- | ----------- |
| **작업** | 액세스를 부여 또는 취소했는지 여부 |
| **Caller** | 액세스 변경을 담당하는 소유자 |
| **Date** | 액세스가 변경된 날짜 및 시간 |
| **DirectoryName** | Azure Active Directory 디렉터리 |
| **PrincipalName** | 사용자, 그룹 또는 응용 프로그램의 이름 |
| **PrincipalType** | 사용자, 그룹 또는 응용 프로그램에 대한 할당인지 여부 |
| **RoleId** | 부여되었거나 해지된 역할의 GUID |
| **RoleName** | 부여되었거나 해지된 역할 |
| **ScopeName** | 구독, 리소스 그룹 또는 리소스의 이름 |
| **ScopeType** | 할당이 구독, 리소스 그룹 또는 리소스 범위에서 이루어졌는지 여부 |
| **SubscriptionId** | Azure 구독의 GUID |
| **SubscriptionName** | Azure 구독의 이름 |

이 예제 명령은 지난 7일 동안 구독에서 발생한 모든 액세스 변경 내용을 나열합니다.

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - 스크린샷](./media/role-based-access-control-configure/access-change-history.png)

## Azure CLI에서 보고서 만들기
Azure 명령줄 인터페이스(CLI)에서 액세스 변경 기록 보고서를 만들려면 다음 명령을 사용합니다.
```
azure role assignment changelog list
```

## 스프레드시트로 내보내기
보고서를 저장하거나 데이터를 조작하거나 액세스 변경을 .csv 파일로 내보냅니다. 그런 다음 검토를 위해 스프레드시트에서 보고서를 볼 수 있습니다.

![Changelog가 스크린샷으로 표시됨 - 스크린샷](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## 참고 항목
- [Azure 역할 기반 액세스 제어](role-based-access-control-configure.md) 시작
- [Azure RBAC에서 사용자 지정 역할](role-based-access-control-custom-roles.md) 작업

<!---HONumber=AcomDC_0727_2016-->