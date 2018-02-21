---
title: "Azure Active Directory에서 그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 안전하게 마이그레이션하는 방법 | Microsoft Docs"
description: "그룹 기반 라이선스를 사용하여 서로 다른 제품 라이선스(예: Office 365 E1 및 E3) 간에 사용자를 마이그레이션하는 데 권장되는 프로세스에 대해 설명합니다."
services: active-directory
keywords: "Azure AD 라이선스"
documentationcenter: 
author: piotrci
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2018
ms.author: piotrci
ms.openlocfilehash: 97654673b395fd5b8cb41afdcdeaa21aba44f61d
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-safely-migrate-users-between-product-licenses-using-group-based-licensing"></a>그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 안전하게 마이그레이션하는 방법

이 문서에서는 그룹 기반 라이선스를 사용할 때 제품 라이선스 간에 사용자를 이동하는 데 권장되는 방법에 대해 설명합니다. 이 방법의 목적은 마이그레이션 중에 서비스 또는 데이터 손실이 없도록 하는 것입니다. 사용자가 제품 간에 원활하게 전환해야 합니다. 마이그레이션 프로세스의 다음 두 가지 변형에 대해 설명합니다.

-   단순: 충돌하는 서비스 계획을 포함하지 않는 제품 라이선스 간(예: *Office 365 Enterprise E3* 및 *Office 365 Enterprise E5*)

-   더 복잡: 일부 충돌 서비스 계획을 포함하는 제품 간(예: *Office 365 Enterprise E1* 및 *Office 365 Enterprise E3*) 충돌에 대한 자세한 내용은 [여기](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans) 및 [여기](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-product-and-service-plan-reference#service-plans-that-cannot-be-assigned-at-the-same-time)에서 확인할 수 있습니다.

이 문서에는 마이그레이션 및 확인 단계를 수행하는 데 사용할 수 있는 샘플 PowerShell 코드가 포함되어 있습니다. 이 코드는 수동으로 단계를 수행할 수 없는 대규모 작업에 특히 유용합니다.

## <a name="before-you-begin"></a>시작하기 전에
마이그레이션 프로세스를 실행하기 전에, 마이그레이션되는 모든 사용자에 대해 다음 가정이 true인지 확인하는 것이 중요합니다. true가 아니면 일부 사용자에 대해 마이그레이션이 실패할 수 있으며, 이로 인해 서비스 또는 데이터에 액세스할 수 없게 됩니다.

-   그룹 기반 라이선스를 사용하여 사용자에게 *소스 라이선스*가 할당되어 있습니다. 기존 제품의 라이선스가 단일 소스 그룹에서 상속되며 직접 할당된 것이 아닙니다. 참고: 라이선스가 직접 할당된 경우 *대상 라이선스*의 적용을 차단할 수 있습니다. [여기](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-advanced#direct-licenses-coexist-with-group-licenses)에서 직접 및 그룹 라이선스 할당에 대해 자세히 알아보세요. [이 스크립트](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-ps-examples#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)와 같은 PowerShell 스크립트를 사용하여 사용자에게 직접 라이선스가 있는지 확인하는 것이 좋습니다.

-   대상 제품에 사용 가능한 라이선스가 충분합니다. 충분하지 않으면 일부 사용자가 *대상 라이선스*를 얻지 못할 수 있습니다. [여기](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products)에서 사용 가능한 라이선스 수를 확인할 수 있습니다.

-   사용자에게 *대상 라이선스*와 충돌하거나 *소스 라이선스*가 제거되지 않도록 할 수 있는 다른 제품 라이선스(예: 다른 제품에 대한 종속성이 있는 Workplace Analytics 또는 Project Online과 같은 추가 기능 제품)가 할당되어 있지 않습니다.

-   사용자 환경에서 그룹이 관리되는 방식을 이해합니다. 예를 들어 온-프레미스로 그룹을 관리하고 AAD Connect를 사용하여 Azure AD에 동기화하는 경우 온-프레미스 시스템을 사용하여 사용자를 추가/제거해야 하며, 변경 내용이 AAD에 동기화되고 그룹 기반 라이선스에 수집되는 데 시간이 걸립니다. Azure AD 동적 그룹 멤버 자격을 사용하는 경우 대신 해당 특성을 수정하여 사용자를 추가/제거합니다. 그러나 사용자를 그룹에 추가/제거하는 방법만 다르고 전반적인 마이그레이션 프로세스는 동일합니다.

## <a name="migrating-users-between-products-without-conflicting-service-plans"></a>충돌하는 서비스 계획이 없는 제품 간 사용자 마이그레이션
그룹 기반 라이선스를 사용하여 사용자 라이선스를 *소스 라이선스*(이 예제에서는 *Office 365 Enterprise E3*)에서 *대상 라이선스*(이 예제에서는 *Office 365 Enterprise E5*)로 변경하는 것을 목적으로 합니다. 두 제품에 충돌하는 서비스 계획이 포함되어 있지 않으므로 충돌 없이 동시에 완전히 할당할 수 있습니다. 마이그레이션 중에 사용자가 서비스 또는 데이터에 대한 액세스 권한을 잃지 않습니다. 또한 마이그레이션이 작은 “배치”로 수행되므로 각 배치의 결과를 검증하여 프로세스 중에 발생할 수 있는 문제의 범위를 최소화할 수 있습니다. 전반적인 프로세스는 다음과 같습니다.
1.  사용자가 소스 그룹의 멤버이며 해당 그룹에서 *소스 라이선스*를 상속합니다.
2.  멤버 없이 *대상 라이선스*로 대상 그룹을 만듭니다.
3.  대상 그룹에 사용자 배치를 추가합니다. 이렇게 하면 GBL(그룹 기반 라이선스)에서 변경 내용을 수집하고 *대상 라이선스*를 할당합니다. 이 작업은 배치 크기 및 테넌트의 기타 활동에 따라 약간의 시간이 소요될 수 있습니다.
4.  사용자 배치가 GBL에서 완전히 처리되었으며 각 사용자에게 실제로 *대상 라이선스*가 할당되었는지 확인합니다. 사용자가 다른 제품과 충돌 또는 라이선스 부족과 같은 오류 상태로 종료되지 않았는지 확인합니다. 오류에 대한 자세한 정보는 [여기](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal)에서 확인할 수 있습니다.
5.  이제 사용자에게 소스 및 *대상 라이선스*가 모두 할당되어 있습니다.
6.  소스 그룹에서 동일한 사용자 배치를 제거합니다. GBL이 변경 내용에 응답하고 *소스 라이선스*가 사용자로부터 제거됩니다.
7.  후속 사용자 배치에 대해 이 프로세스를 반복합니다.

### <a name="migrating-a-single-user-using-azure-portal"></a>Azure Portal을 사용하여 단일 사용자 마이그레이션
단일 사용자가 마이그레이션되는 간단한 연습입니다.

- **1단계**: 사용자에게 그룹에서 상속된 *소스 라이선스*가 있으며 라이선스에 대한 직접 할당이 없습니다.
![그룹에서 상속된 소스 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithSourceLicenseInherited.png)

- **2단계**: 사용자가 대상 그룹에 추가되었으며 GBL에서 변경 내용을 처리했습니다. 이제 사용자에게 두 그룹에서 상속된 *소스* 및 *대상* 라이선스가 모두 있습니다.
![그룹에서 상속된 소스 및 대상 라이선스가 모두 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithBothSourceAndTargetLicense.png)

- **3단계**: 사용자가 소스 그룹에서 제거되었으며 GBL에서 변경 내용을 처리했습니다. 이제 사용자에게 *대상 라이선스*만 있습니다.
![그룹에서 상속된 대상 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithTargetLicenseAssigned.png)

### <a name="automating-migration-using-powershell"></a>PowerShell을 사용하여 마이그레이션 자동화
> [!NOTE]
> 이 샘플 코드는 이 문서의 [마지막 섹션](#powershell-automation-of-migration-and-verification-steps)에 포함된 PowerShell 함수를 사용합니다.

이 코드 조각은 마이그레이션 프로세스를 더 큰 규모로 자동화할 수 있는 방법을 보여 줍니다.
```
#A batch of users that we want to migrate in this iteration. This can be an array of User Principal Names (string) or ObjectIds (Guid)
#Note: this could be loaded from a text file that represents a larger batch of users we want to migrate
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com','MigrationUser2@tailspinonline.com'

###############NON-CONFLICTING LICENSES SCENARIO################
#The group and license that we are moving from
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:ENTERPRISEPACK'      #<- this is the O365 E3 product
#The group and license that we are moving to
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPREMIUM'   #<- this is the O365 E5 product

if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will assign the target license $targetSkuId to all users"
AddUsersToGroup $usersToMigrate $targetGroupId

#Verify that the target license shows up in conflict state for each user on the list. This step will run in a loop, forever, until all users are in the expected state.
#Since GBL may take some time to reflect the changes on users, this loop should terminate after some time dependent on the size of the user collection.
#Note: If the loop has not terminated for a long time, stop the script and inspect the users reported as not yet in the expected state and verify that they are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceandTargetLicensePresent} 'STEP 2: Checking if all users still have the source license and now also have the target license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

#Now it is safe to remove the users from the source group
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

#Verify that target license is now active on each user and the source license has been removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

샘플 출력(사용자 2명 마이그레이션):
```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPREMIUM licenses available (13) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.

STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will assign the target license TailspinOnline:ENTERPRISEPREMIUM to all users
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:ENTERPRISEPACK.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

## <a name="migrating-users-between-products-with-conflicting-service-plans"></a>충돌하는 서비스 계획이 있는 제품 간 사용자 마이그레이션
그룹 기반 라이선스를 사용하여 사용자 라이선스를 *소스 라이선스*(이 예제에서는 *Office 365 Enterprise E1*)에서 *대상 라이선스*(이 예제에서는 *Office 365 Enterprise E3*)로 변경하는 것을 목적으로 합니다. 두 제품에 충돌하는 서비스 계획([여기](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans)서 충돌에 대한 자세한 정보 확인)이 포함되어 있으므로 사용자를 원활하게 마이그레이션하기 위해 충돌을 해결해야 합니다. 마이그레이션 중에 사용자가 서비스 또는 데이터에 대한 액세스 권한을 잃지 않습니다. 또한 마이그레이션이 작은 “배치”로 수행되므로 각 배치의 결과를 검증하여 프로세스 중에 발생할 수 있는 문제의 범위를 최소화할 수 있습니다. 전반적인 프로세스는 다음과 같습니다.
1.  사용자가 소스 그룹의 멤버이며 해당 그룹에서 *소스 라이선스*를 상속합니다.
2.  멤버 없이 *대상 라이선스*로 대상 그룹을 만듭니다.
3.  대상 그룹에 사용자 배치를 추가합니다. 이렇게 하면 GBL(그룹 기반 라이선스)에서 변경 내용을 수집하고 *대상 라이선스*를 할당하려고 합니다. 두 제품의 서비스 간 충돌로 인해 할당이 실패하고, 대신 GBL에서 각 사용자에 대한 오류를 기록합니다.
이 작업은 배치 크기 및 테넌트의 기타 활동에 따라 약간의 시간이 소요될 수 있습니다.
4.  사용자 배치가 GBL에서 완전히 처리되었으며 각 사용자에 대해 충돌 오류가 기록되었는지 확인합니다. 일부 사용자가 예상치 않은 오류 상태로 종료되지 않았는지 확인합니다. 오류에 대한 자세한 정보는 [여기](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal)에서 확인할 수 있습니다.
5.  여전히 사용자에게 *소스 라이선스* 및 *대상 라이선스*에 대한 충돌 오류가 있습니다. *대상 라이선스*는 아직 실제로 할당되지 않았습니다.
6.  소스 그룹에서 동일한 사용자 배치를 제거합니다. GBL에서 변경 내용에 응답하고 *소스 라이선스*가 각 사용자로부터 제거됩니다. 이와 동시에 충돌 오류도 제거되며(다른 제품 라이선스가 오류에 영향을 주지 않는다고 가정) *대상 라이선스*가 할당됩니다. 이렇게 하면 전환 중에 서비스 또는 데이터가 손실되지 않습니다.
7.  후속 사용자 배치에 대해 이 프로세스를 반복합니다.

### <a name="migrating-a-single-user-using-azure-portal"></a>Azure Portal을 사용하여 단일 사용자 마이그레이션
단일 사용자가 마이그레이션되는 간단한 연습입니다.

- **1단계**: 사용자에게 그룹에서 상속된 *소스 라이선스*가 있으며 라이선스에 대한 직접 할당이 없습니다.
![그룹에서 상속된 소스 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithSourceLicenseInheritedConflictScenario.png)

- **2단계**: 사용자가 대상 그룹에 추가되었으며 GBL에서 변경 내용을 처리했습니다. 여전히 사용자에게 *소스 라이선스*가 있으며, 충돌로 인해 오류 상태인 *대상 라이선스*가 있습니다.
![그룹에서 상속된 소스 라이선스 및 오류 상태인 대상 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithSourceLicenseAndTargetLicenseInConflict.png)

- **3단계**: 사용자가 소스 그룹에서 제거되었으며 GBL에서 변경 내용을 처리했습니다. 이제 사용자에게 *대상 라이선스*가 적용되었습니다. ![그룹에서 상속된 대상 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithTargetLicenseAssignedConflictScenario.png)

### <a name="automating-migration-using-powershell"></a>PowerShell을 사용하여 마이그레이션 자동화
> [!NOTE]
> 이 샘플 코드는 이 문서의 [마지막 섹션](#powershell-automation-of-migration-and-verification-steps)에 포함된 PowerShell 함수를 사용합니다.

이 코드 조각은 마이그레이션 프로세스를 더 큰 규모로 자동화할 수 있는 방법을 보여 줍니다.
```
#A batch of users that we want to migrate in this iteration. This can be an array of User Principal Names (string) or ObjectIds (Guid)
#Note: this could be loaded from a text file that represents a larger batch of users we want to migrate
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

###############CONFLICTING LICENSES SCENARIO################
#The group and license that we are moving from
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             #<- this is the O365 E1 product
#The group and license that we are moving to
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           #<- this is the O365 E3 product

#Assumptions before migration:
#1. Users are already in the source group and they have the source license assigned from that group
#2. Users do not have the same source license assigned from another group at the same time and they do not have the source license assigned directly
#This is important - if not true, removing users from the source group in Step 3 is not going to result in the target license getting applied correctly
#3. There are enough available licenses for the target license to assign to the users we are migrating.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

#Verify that the target license shows up in conflict state for each user on the list. This step will run in a loop, forever, until all users are in the expected state.
#Since GBL may take some time to reflect the changes on users, this loop should terminate after some time dependent on the size of the user collection.
#Note: If the loop has not terminated for a long time, stop the script and inspect the users reported as not yet in the expected state and verify that they are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

#Now it is safe to remove the users from the source group
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

#Verify that target license is now active on each user and the source license has been removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

샘플 출력(사용자 2명 마이그레이션):
```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPACK licenses available (61) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.
STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will put target license TailspinOnline:ENTERPRISEPACK in conflict state with the source license TailspinOnline:STANDARDPACK
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:STANDARDPACK and remove the conflict on target license TailspinOnline:ENTERPRISEPACK which will become assigned.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

## <a name="powershell-automation-of-migration-and-verification-steps"></a>마이그레이션 및 확인 단계의 PowerShell 자동화
이 섹션에는 문서의 앞부분에서 사용한 스크립트를 실행하는 데 필요한 PowerShell 코드가 포함되어 있습니다.

>[!WARNING]
>이 코드는 데모 용도의 예로 제공됩니다. 사용자 환경에서 사용하려는 경우, 먼저 작은 규모로 테스트하거나 별도의 테스트 테넌트에서 테스트하는 것이 좋습니다. 환경의 특정 요구에 맞게 코드를 조정해야 할 수도 있습니다.

코드를 실행하려면 [Azure AD PowerShell v1.0 라이브러리](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0] for instructions)를 사용해야 합니다. 스크립트를 실행하기 전에 먼저 *connect-msolservice* cmdlet을 실행하여 테넌트에 로그인합니다.
```
#BEGIN: Helper functions used in the script

#Retrieves user object based on ObjectId or UserPrincipalName
function GetUserObject
{
    [OutputType([Microsoft.Online.Administration.User])]
    Param([object]$userId)

    $userIdType = $userId.GetType()

    if($userIdType -eq [Guid])
    {
        return Get-MsolUser -ObjectId $userId
    }
    elseif($userIdType -eq [string])
    {
        return Get-MsolUser -UserPrincipalName $userId
    }
    else
    {
        throw "User Id type must be a Guid or a string (UserPrincipalName). The user id type was: $($userIdType.Name)"
    }
}

#Gets a Guid objectId for a user, even if a UserPrincipal string was passed in. Guid ids are needed for group membership manipulation, where UPNs cannot be used
function GetGuidUserId
{
    [OutputType([Guid])]
    Param([object]$userId)

    [Guid]$guidId = [Guid]::Empty
    if($userId.GetType() -eq [String])
    {
        $user = GetUserObject $userId
        return $user.ObjectId
    }
    elseif($userId.GetType() -eq [Guid])
    {
        return $userId
    }
    else
    {
        throw "UserId type must be a Guid or a string (UserPrincipalName). The user id type was: $($userId.GetType().Name)"
    }
}

#Adds a collection of users to a group. Note: this fails if a user is already a member of the group
function AddUsersToGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Adding user $userId to group $groupId"
        Add-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

#Removes a collection of users from a group. Note: this fails if a user is not a member of the group
function RemoveUsersFromGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Removing user $userId from group $groupId"
        Remove-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

#Returns the license object corresponding to the skuId. Returns NULL if not found
function GetUserLicense
{
    [OutputType([Microsoft.Online.Administration.UserLicense])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    #we look for the specific license SKU in all licenses assigned to the user
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

#Checks if the specific SKU license is assigned to the user, regardless of how it may be assigned (directly or via GBL)
function IsLicenseAssignedToUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [string]$skuId)

    $license = GetUserLicense $user $skuId

    return ($license -ne $null)
}

function GetObjectIdsAssigningLicense
{
    [OutputType([Guid[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        return [Guid[]]$license.GroupsAssigningLicense
    }
    return [Array]::CreateInstance([Guid],0)
}

#Returns TRUE if the user is inheriting the license from the specific group.
#Note: this returns true only if the license is successfully assigned from the group. If the license is in error state, this return false
function UserHasLicenseAssignedFromThisGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    [Guid[]]$objectsAssigningLicense = GetObjectIdsAssigningLicense $user $skuId

    #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
    #This could be a group object or a user object (contrary to what the name suggests)
    foreach ($assignmentSource in $objectsAssigningLicense)
    {
        #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
        #Note: the license may also be assigned directly in addition to being inherited
        if ($assignmentSource -ieq $groupId)
        {
            return $true
        }
    }
    return $false
}

#Returns error objects for a specific license
function GetErrorsForLicense
{
    [OutputType([Microsoft.Online.Administration.IndirectLicenseError[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    [Microsoft.Online.Administration.IndirectLicenseError[]] $errorObjects = $user.IndirectLicenseErrors | Where {"$($_.AccountSku.AccountName):$($_.AccountSku.SkuPartNumber)" -ieq $skuId}

    if($errorObjects -eq $null)
    {
        #there are no errors at all
        return [Array]::CreateInstance([Microsoft.Online.Administration.IndirectLicenseError],0)
    }

    return $errorObjects
}
#Returns an error label associated with a specific license inherited from a specific group. return $null if there is no error
function GetErrorForLicenseFromGroup
{
    [OutputType([string])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId)


    #There are some errors. Check if any of them is associated with the group
    foreach($licenseError in GetErrorsForLicense $user $skuId)
    {
        if($licenseError.ReferencedObjectId -eq $groupId)
        {
            return $licenseError.Error
        }
    }
    return $null
}

#Checks if the license is in an expected state for a given group.
#If expectedError is set to a value, this looks if the license is in that specific error state from the group
#If expectedError is NULL, this checks if the license is successfully assigned from the group
function IsExpectedLicenseStateForGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId, [string]$expectedError)

    #we expect the license to be fully assigned from the group and not in error state
    if([string]::IsNullOrEmpty($expectedError))
    {
        #check if the assigned license is inherted from the expected group, without an error on it
        return (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
    }
    #we expect the license to be in the specific error state on the specific group
    else
    {
        $error = GetErrorForLicenseFromGroup $user $groupId $skuId
        return ($error -ieq $expectedError)
    }
}

#Detects if the licenses are in the specific state where the source license is still assigned, but the target license is in conflict state
#Note: if the source license is not present, this throws an exception to abort the script, because that state may signify something went wrong with the migration steps and user lost access to services
function VerifySourceLicensePresentAndTargetLicenseInConflictState
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check user still has source license - if not, abort because something is seriously wrong
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is in conflict, as expected
    $conflictError = 'MutuallyExclusiveViolation'
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $conflictError)
}

#Detects if the licenses are in the specific state where the source license is no longer present, but the target license is correctly assigned
#Note: if the source license is gone, but target license is not present, this throws an exception to abort the script, because something went wrong and the user may have lost access to services
function VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check if user has the source license completely removed, which is a prerequisite to the target license eventually kicking in
    if(IsLicenseAssignedToUser $user $sourceSkuId)
    {
        return $false
    }

    #check user has the target license at all - if not, abort because something is seriously wrong
    if(-Not (IsLicenseAssignedToUser $user $targetSkuId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $targetSkuId assigned, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is assigned from the expected target group, and not in error state anymore
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $null)
}

#Detects if the licenses are in the specific state where the source license is still assigned and the target license is assigned as well
#Note: if the source license is not present, this throws an exception to abort the script, because that state may signify something went wrong with the migration steps and user lost access to services
function VerifySourceandTargetLicensePresent
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check user still has source license - if not, abort because something is seriously wrong
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is also assigned from the target group
    return (UserHasLicenseAssignedFromThisGroup $user $targetSkuId $targetGroupId)
}


#Verifies basic assumptions that should be true for a user before we execute the migration process.
#Returns TRUE if all assumptions are true. Prints details
function VerifyAssumptionsForUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    $userName = $user.UserPrincipalName
    #1. User has the source license assigned from the source group.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        Write-Host "$userName does not have source license $sourceSkuId assigned from source group $sourceGroupId."
        return $false
    }

    #2. User does not have the same source license assigned from another group at the same time and they do not have the source license assigned directly
    [Guid[]]$otherObjectsAssigningLicense = GetObjectIdsAssigningLicense $user $sourceSkuId | Where {$_ -ne $sourceGroupId}
    foreach($otherObject in $otherObjectsAssigningLicense)
    {
        if($otherObject -eq $user.ObjectId)
        {
            Write-Host "$userName has source license assigned directly to the user."
        }
        else
        {
            Write-Host "$username has source license assigned from an additional unexpected group $otherObject."
        }
    }
    if($otherObjectsAssigningLicense -and $otherObjectsAssigningLicense.Count -gt 0)
    {
        return $false
    }

    #3. User does not have the target license assigned
    if(IsLicenseAssignedToUser $user $targetSkuId)
    {
        Write-Host "$userName already has target license assigned."
        return $false
    }

    #4. User does not have the target license in error state from some groups
    [Microsoft.Online.Administration.IndirectLicenseError[]]$licenseErrors = GetErrorsForLicense $user $targetSkuId
    foreach($licenseError in $licenseErrors)
    {
        Write-Host "$userName has target license in error state $($licenseError.Error) from unexpected group $($licenseError.ReferencedObjectId)."
    }
    if($licenseErrors -and $licenseErrors.Count -gt 0)
    {
        return $false
    }

    Write-Host "$userName`t`tOK"
    return $true
}

#Checks if all users to be migrated are in correct state
function VerifyAssumptions
{
    [OutputType([bool])]
    Param([object[]]$userIds, [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    Write-Host "Verifying initial assumptions:"

    #Check if there are enough target licenses for all users
    $skuState = Get-MsolAccountSku | Where {$_.AccountSkuId -ieq $targetSkuId}

    if($skuState -eq $null)
    {
        Write-Host "Target license SKU $targetSkuId does not exist in the tenant at all."
        return $false
    }

    $availableLicenses = $skuState.ActiveUnits - $skuState.ConsumedUnits

    if($userIds.Count -gt $availableLicenses)
    {
        Write-Host "Not enough licenses for all users. User count: $($userIds.Count), licenses available: $availableLicenses"
        return $false
    }
    else
    {
        Write-Host "Enough $targetSkuId licenses available ($availableLicenses) for users: $($userIds.Count)."
    }

    #Check if each user to be migrated is in expected state
    $usersOK = 0
    $usersNotOK = 0
    foreach($userId in $userIds)
    {
        $user = GetUserObject $userId
        if(VerifyAssumptionsForUser $user $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId)
        {
            ++$usersOK
        }
        else
        {
            ++$usersNotOK
        }
    }
    if($usersNotOK -gt 0)
    {
        Write-Host "Checks passed for $usersOK users, but failed for $usersNotOK users."
        return $false
    }
    Write-Host "Checks passed for all $usersOK users."
    return $true
}

#Helper function: it executes one of the verification functions (passed in as a delegate using $checkFunction) for each user, keeps track of how many users passed/failed verification
#and repeats the loop until all users have passed the check. The loop may never terminate if some users never reach the expected state, which should be investigated.
#Note: if the verification function fails with an exception (e.g. because it detected unexpected user state) this loop will terminate and investigation into user state is needed
function ExecuteVerificationLoop
{
    Param([System.Management.Automation.ScriptBlock]$checkFunction, [string]$consoleMessage, [object[]]$userIds,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #how long to wait until the loop is retried
    $sleepIntervalSeconds = 60
    $retryIteration = 1

    While($true)
    {
        Write-Host "`n$consoleMessage. Check iteration: $retryIteration`n"

        $usersInExpectedState = 0
        $usersNotYet = 0

        foreach ($userId in $userIds)
        {
            $user = GetUserObject $userId
            if($checkFunction.InvokeReturnAsIs($user, $sourceGroupId, $sourceSkuId, $targetGroupId, $targetSkuId))
            {
                Write-Host "$userId`t`tExpected state: YES"
                ++$usersInExpectedState
            }
            else
            {
                Write-Host "$userId`t`tExpected state: NO"
                ++$usersNotYet
            }
        }

        Write-Host "`nTotal users checked: $($userIds.Count). In expected state: $usersInExpectedState. Not yet: $usersNotYet"

        if($usersNotYet -eq 0)
        {
            Write-Host "Check passed for all users. Exiting check loop."
            return
        }

        ++$retryIteration
        Write-Host "Not all users are in expected state. Waiting $sleepIntervalSeconds seconds to try again."
        Start-Sleep -Seconds $sleepIntervalSeconds
    }
}
#END: Helper functions used in the script

#BEGIN: Execute script

#enable strict mode
Set-StrictMode -Version latest
#stop on first exception thrown
$ErrorActionPreference = "Stop"

#A batch of users that we want to migrate in this iteration. This can be an array of User Principal Names (string) or ObjectIds (Guid)
#Note: this could be loaded from a text file that represents a larger batch of users we want to migrate
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

###############CONFLICTING LICENSES SCENARIO################
#The group and license that we are moving from
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             #<- this is the O365 E1 product
#The group and license that we are moving to
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           #<- this is the O365 E3 product

#Assumptions before migration:
#1. Users are already in the source group and they have the source license assigned from that group
#2. Users do not have the same source license assigned from another group at the same time and they do not have the source license assigned directly
#This is important - if not true, removing users from the source group in Step 3 is not going to result in the target license getting applied correctly
#3. There are enough available licenses for the target license to assign to the users we are migrating.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

#Verify that the target license shows up in conflict state for each user on the list. This step will run in a loop, forever, until all users are in the expected state.
#Since GBL may take some time to reflect the changes on users, this loop should terminate after some time dependent on the size of the user collection.
#Note: If the loop has not terminated for a long time, stop the script and inspect the users reported as not yet in the expected state and verify that they are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

#Now it is safe to remove the users from the source group
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

#Verify that target license is now active on each user and the source license has been removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

#END: Execute script
```

## <a name="next-steps"></a>다음 단계

그룹을 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 이 문서를 읽어 보세요.

* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure AD의 그룹 기반 라이선싱에 대한 PowerShell 예제](active-directory-licensing-ps-examples.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)
