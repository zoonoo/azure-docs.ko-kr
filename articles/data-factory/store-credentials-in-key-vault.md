---
title: "Azure Key Vault에 자격 증명 저장 | Microsoft Docs"
description: "Azure Data Factory에서 런타임 시 자동으로 검색할 수 있는 Azure Key Vault에 사용되는 데이터 저장소의 자격 증명을 저장하는 방법을 알아봅니다."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Azure Key Vault에 자격 증명 저장

[Azure Key Vault](../key-vault/key-vault-whatis.md)에서 데이터 저장소의 자격 증명을 저장할 수 있습니다. Azure Data Factory에서는 데이터 저장소를 사용하는 작업을 실행할 때 자격 증명을 검색합니다.

> [!NOTE]
> 현재 [Dynamics 커넥터](connector-dynamics-crm-office-365.md)만이 이 기능을 지원합니다. 

## <a name="steps"></a>단계

데이터 팩터리를 만들 때 팩터리가 만들어지면서 서비스 ID도 생성됩니다. 서비스 ID는 Azure Activity Directory에 등록된 관리되는 응용 프로그램이고 이 특정 데이터 팩터리를 나타냅니다. Azure Portal -> 데이터 팩터리 -> 속성에서 서비스 ID 정보를 찾을 수 있습니다. 

- 서비스 ID ID
- 서비스 ID 테넌트
- 서비스 ID 응용 프로그램 ID

Azure Key Vault에 저장된 자격 증명을 참조하려면 다음을 수행해야 합니다.

1. 데이터 팩터리와 함께 생성된 "서비스 ID 응용 프로그램 ID"를 복사합니다.
2. Azure Key Vault에 대한 서비스 ID 액세스 권한을 부여합니다. 키 자격 증명 모음 -> 액세스 제어 -> 추가 ->에서 이 서비스 ID 응용 프로그램 ID를 검색하여 읽기 권한자 권한을 추가합니다. 그러면 이 지정된 팩터리가 키 자격 증명 모음에 있는 비밀에 액세스할 수 있습니다.
3. Azure Key Vault를 가리키는 연결된 서비스를 만듭니다. [Azure Key Vault 연결된 서비스](#azure-key-vault-linked-service)를 참조합니다.
4. 해당 비밀을 키 자격 증명 모음에 저장한 참조 내에서 데이터 저장소 연결된 서비스를 만듭니다. [키 자격 증명 모음에 저장된 참조 자격 증명](#reference-credential-stored-in-key-vault)을 참조합니다.

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 연결된 서비스

Azure Key Vault 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureKeyVault**로 설정되어야 합니다. | 예 |
| baseUrl | Azure Key Vault URL(DNS 이름)을 지정합니다. | 예 |

**예제:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>키 자격 증명 모음에 저장된 참조 자격 증명

키 자격 증명 모음 암호를 참조하는 연결된 서비스에서 필드를 구성할 때 다음 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 필드의 형식 속성을 **AzureKeyVaultSecret**으로 설정해야 합니다. | 예 |
| secretName | Azure Key Vault의 비밀 이름입니다. | 예 |
| secretVersion | Azure Key Vault의 비밀 버전입니다.<br/>지정하지 않으면 항상 최신 버전의 비밀을 사용합니다.<br/>지정하는 경우 지정된 버전을 사용합니다.| 아니요 |
| store | 자격 증명을 저장하는 데 사용하는 Azure Key Vault 연결된 서비스를 나타냅니다. | 예 |

**예: ("암호" 섹션 참조)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
