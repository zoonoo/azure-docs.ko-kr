---
title: Azure API for FHIR용 고객 관리형 키 구성
description: Cosmos DB를 통해 Azure API for FHIR에서 지원되는 사용자 고유의 키 기능을 가져옵니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: matjazl
ms.openlocfilehash: 05c208ba3c9005d38b8924037748764f8d112e3a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398184"
---
# <a name="configure-customer-managed-keys"></a>고객 관리형 키 구성

새 Azure API for FHIR 계정을 만들면 기본적으로 Microsoft 관리형 키를 사용하여 데이터가 암호화됩니다. 이제는 사용자가 직접 선택하고 관리하는 사용자 고유의 키를 사용하여 데이터에 대한 두 번째 암호화 계층을 추가할 수 있습니다.

Azure에서 이는 일반적으로 고객의 AKV(Azure Key Vault)에 있는 암호화 키를 사용하여 수행됩니다. Azure SQL, Azure Storage 및 Cosmos DB는 현재 이 기능을 제공하는 몇 가지 예입니다. Azure API for FHIR은 Cosmos DB에서 제공하는 이 지원을 활용합니다. 계정을 만드는 경우 AKV 키 URI를 지정하는 옵션이 제공됩니다. DB 계정이 프로비저닝될 때 이 키를 Cosmos DB에 전달합니다. FHIR 요청이 수행되면 Cosmos DB에서 키를 가져오고 이를 사용하여 데이터를 암호화/암호 해독합니다. 시작하기 위해 다음 링크를 참조할 수 있습니다.

- [Azure 구독에 대한 Azure Cosmos DB 리소스 공급자 등록](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [AKV 인스턴스 구성](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
-  [AKV 인스턴스에 액세스 정책 추가](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [AKV에서 키 생성](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

Azure Portal에서 Azure API for FHIR을 만들면 "추가 설정" 탭의 "데이터베이스 설정" 아래에 "데이터 암호화" 구성 옵션이 표시됩니다. 기본적으로 서비스 관리형 키 옵션이 선택되어 있습니다. AKV 키는 여기서 "고객 관리형 키" 옵션을 선택하여 지정할 수 있습니다. 복사된 키 URI를 여기에 입력할 수 있습니다.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API for FHIR 만들기":::

또는 키 선택기에서 키를 선택할 수 있습니다.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="키 선택기":::

기존 FHIR 계정의 경우 "데이터베이스" 블레이드에서 아래와 같은 키 암호화 선택 항목(서비스 또는 고객 관리형 키)이 표시됩니다. 구성 옵션을 선택한 후에는 수정할 수 없습니다. 그러나 키는 수정하고 업데이트할 수 있습니다.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

또한 지정된 키의 새 버전을 만들 수 있습니다. 그러면 서비스 중단 없이 데이터가 새 버전을 통해 암호화됩니다. 키에 대한 액세스를 제거하여 데이터에 대한 액세스를 제거할 수도 있습니다.