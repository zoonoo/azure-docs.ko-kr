---
title: 파이프라인 작업에서 Azure Key Vault 비밀 사용
description: Azure 키 자격 증명 모음에서 저장된 자격 증명을 가져오고 데이터 팩터리 파이프라인 실행 중에 사용하는 방법을 알아봅니다.
services: data-factory
author: ChrisLound
manager: anandsub
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: chlound
ms.openlocfilehash: f2531ebfd8b1eafc04fa6eda660b0eec3d1147f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417086"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>파이프라인 작업에서 Azure Key Vault 비밀 사용

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Key Vault에 자격 증명 또는 비밀 값을 저장하고 파이프라인 실행 중에 사용하여 활동에 전달할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 기능은 데이터 팩터리 관리 ID를 기반으로 합니다.  [데이터 팩터리에 대한 관리 ID에서](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity) 작동하는 방법을 알아보고 데이터 팩터리에 연결된 ID가 있는지 확인합니다.

## <a name="steps"></a>단계

1. 데이터 팩터리의 속성을 열고 관리되는 ID 응용 프로그램 ID 값을 복사합니다.

    ![관리되는 ID 값](media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png)

2. 키 자격 증명 모음 액세스 정책을 열고 관리되는 ID 권한을 비밀 얻기 및 나열에 추가합니다.

    ![Key Vault 액세스 정책](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png)

    ![Key Vault 액세스 정책](media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png)

    **추가를**클릭한 다음 **에 추가를 클릭합니다.**

3. 키 볼트 비밀로 이동하여 비밀 식별자를 복사합니다.

    ![비밀 식별자](media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png)

    데이터 팩터리 파이프라인 실행 중에 얻을 수 있는 비밀 URI를 기록합니다.

4. 데이터 팩터리 파이프라인에서 새 웹 활동을 추가하고 다음과 같이 구성합니다.  

    |속성  |값  |
    |---------|---------|
    |안전한 출력     |True         |
    |URL     |[비밀 URI 값]?api 버전=7.0         |
    |메서드     |GET         |
    |인증     |MSI         |
    |리소스        |https://vault.azure.net       |

    ![웹 활동](media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png)

    > [!IMPORTANT]
    > 비밀 URI 끝에 **?api 버전=7.0을** 추가해야 합니다.  

    > [!CAUTION]
    > 보안 출력 옵션을 true로 설정하여 비밀 값이 일반 텍스트로 기록되지 않도록 합니다.  이 값을 사용하는 추가 활동에는 보안 입력 옵션이 true로 설정되어 있어야 합니다.

5. 다른 활동에서 값을 사용하려면 다음 코드 ** @activity식('Web1')을 사용합니다.output.value.**

    ![코드 표현식](media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png)

## <a name="next-steps"></a>다음 단계

Azure Key Vault를 사용하여 데이터 저장소 및 계산에 대한 자격 증명을 저장하는 방법을 알아보려면 [Azure Key Vault의 저장소 자격 증명을](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) 참조하십시오.
