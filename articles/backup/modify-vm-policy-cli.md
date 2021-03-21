---
title: CLI를 사용 하 여 기존 VM 백업 정책 업데이트
description: Azure CLI를 사용 하 여 기존 VM 백업 정책을 업데이트 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: 33083d6585d2b9296cd184ba258b8d2143d685b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98728581"
---
# <a name="update-the-existing-vm-backup-policy-using-cli"></a>CLI를 사용 하 여 기존 VM 백업 정책 업데이트

Azure CLI를 사용 하 여 기존 VM 백업 정책을 업데이트할 수 있습니다. 이 문서에서는 기존 정책을 JSON 파일로 내보내고, 파일을 수정한 후 Azure CLI를 사용 하 여 수정 된 정책으로 정책을 업데이트 하는 방법을 설명 합니다.

## <a name="modify-an-existing-policy"></a>기존 정책 수정

기존 VM 백업 정책을 수정 하려면 다음 단계를 수행 합니다.

1. [Az backup policy show](/cli/azure/backup/policy#az_backup_policy_show) 명령을 실행 하 여 업데이트 하려는 정책의 세부 정보를 검색 합니다.

    예제:

    ```azurecli
    az backup policy show --name testing123 --resource-group rg1234 --vault-name testvault
    ```

    위의 예제에서는 이름이 *testing123* 인 VM 정책에 대 한 세부 정보를 보여 줍니다.

    출력:

    ```json
    {
    "eTag": null,
    "id": "/Subscriptions/efgsf-123-test-subscription/resourceGroups/rg1234/providers/Microsoft.RecoveryServices/vaults/testvault/backupPolicies/testing123",
    "location": null,
    "name": "testing123",
    "properties": {
        "backupManagementType": "AzureIaasVM",
        "instantRpDetails": {
        "azureBackupRgNamePrefix": null,
        "azureBackupRgNameSuffix": null
        },
        "instantRpRetentionRangeInDays": 2,
        "protectedItemsCount": 0,
        "retentionPolicy": {
        "dailySchedule": {
            "retentionDuration": {
            "count": 180,
            "durationType": "Days"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "monthlySchedule": null,
        "retentionPolicyType": "LongTermRetentionPolicy",
        "weeklySchedule": {
            "daysOfTheWeek": [
            "Sunday"
            ],
            "retentionDuration": {
            "count": 30,
            "durationType": "Weeks"
            },
            "retentionTimes": [
            "2020-08-03T04:30:00+00:00"
            ]
        },
        "yearlySchedule": null
        },
        "schedulePolicy": {
        "schedulePolicyType": "SimpleSchedulePolicy",
        "scheduleRunDays": null,
        "scheduleRunFrequency": "Daily",
        "scheduleRunTimes": [
            "2020-08-03T04:30:00+00:00"
        ],
        "scheduleWeeklyFrequency": 0
        },
        "timeZone": "UTC"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupPolicies"
    }
    ```

1. 위의 출력을 json 파일에 저장 합니다. 예를 들어 *Policy.js* 로 저장해 보겠습니다.
1. 요구 사항에 따라 JSON 파일을 업데이트 하 고 변경 내용을 저장 합니다.

    예: 매주 보존을 60 일로 업데이트 하려면 개수를 60로 변경 하 여 JSON 파일의 다음 섹션을 업데이트 합니다.

    ```json
            "retentionDuration": {
          "count": 60,
          "durationType": "Weeks"
        }

    ```

1. 변경 내용을 저장합니다.
1. [Az backup policy set](/cli/azure/backup/policy#az_backup_policy_set) 명령을 실행 하 고 업데이트 된 JSON 파일의 전체 경로를 **--policy** 매개 변수의 값으로 전달 합니다.

    ```azurecli
    az backup policy set --resource-group rg1234 --vault-name testvault --policy C:\temp2\Policy.json --name testing123
    ```

>[!NOTE]
>[Az backup policy get-default-vm](/cli/azure/backup/policy#az_backup_policy_get_default_for_vm) 명령을 실행 하 여 샘플 JSON 정책을 검색할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup 서비스를 사용 하 여 Azure VM 백업 관리](backup-azure-manage-vms.md)