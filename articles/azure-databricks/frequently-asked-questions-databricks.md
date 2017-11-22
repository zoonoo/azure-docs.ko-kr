---
title: "Azure Databricks: 일반적인 질문 및 도움말 | Microsoft Docs"
description: "Azure Databricks와 관련된 일반적인 질문에 대한 답변과 문제 해결 정보를 살펴봅니다."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: b6b001087cba5f8550d4fea3e4a2f7c1c865beae
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Azure Databricks 미리 보기: 일반적인 질문 및 도움말

이 문서에는 Azure Databricks와 관련될 수 있는 상위 쿼리가 나열되어 있습니다. 또한 Azure Databricks를 사용하는 동안 발생할 수 있는 몇 가지 일반적인 문제도 나열되어 있습니다. Azure Databricks에 대한 자세한 내용은 [Azure Databricks란?](what-is-azure-databricks.md)을 참조하세요. 

## <a name="common-questions"></a>일반적인 질문

이 섹션에는 Azure Databricks와 관련된 일반적인 질문이 나열되어 있습니다.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>로컬 암호화에 사용자 고유 키를 사용할 수 있나요? 
현재 릴리스에서는 Azure Key Vault에서 고유 키를 사용하는 것이 지원되지 않습니다. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Azure Databricks과 함께 Azure VNET을 사용할 수 있나요?
새 VNET은 Azure Databricks 프로비전의 일부로 만들어집니다. 이 릴리스의 일부로 사용자 고유의 Azure VNET을 사용할 수 없습니다.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>노트북에서 Azure Data Lake Store에 어떻게 액세스할까요? 

1. Azure Active Directory에서 서비스 주체를 프로비전하고 해당 키를 기록합니다.
2. Azure Data Lake Store의 서비스 주체에게 필요한 권한을 할당합니다.
3. Azure Data Lake Store의 파일에 액세스하려면 노트북에서 서비스 주체 자격 증명을 사용합니다.

자세한 내용은 [Azure Databricks에서 Data Lake Store 사용](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 Azure Databricks의 일반적인 문제를 해결하는 방법에 대해 설명합니다.

### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>문제: {메일} 계정에는 Azure Portal의 Databricks 작업 영역 리소스에 소유자 또는 참가자 역할이 없습니다.

**오류 메시지**

{메일} 계정에는 Azure Portal의 Databricks 작업 영역 리소스에 소유자 또는 참가자 역할이 없습니다. 테넌트에서 게스트 사용자인 경우에도 이 오류가 발생할 수 있습니다. 관리자에게 문의하여 액세스 권한을 부여받거나 Databricks 작업 영역에서 직접 사용자로 추가합니다. 

**솔루션**

테넌트를 초기화하려면 게스트 사용자가 아닌, 테넌트의 일반 사용자로 로그인해야 합니다. 또한 Databricks 작업 영역 리소스에 참가자 역할이 있어야 합니다. Azure Portal에 있는 Azure Databricks 작업 영역 내의 **액세스 제어(IAM)** 탭에서 사용자 액세스 권한을 부여할 수 있습니다.

### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>문제: {메일} 계정이 Databricks에 등록되지 않았습니다. 

**솔루션**

작업 영역을 만들지 않고 작업 영역의 사용자로 추가된 경우 작업 영역을 생성한 사람에게 문의하여 Azure Databricks 관리 콘솔을 사용하여 추가합니다. 자세한 내용은 [사용자 추가 및 관리](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html)를 참조하세요. 작업 영역을 만든 후 계속해서 이 오류가 발생되면 Azure Portal에서 "작업 영역 초기화"를 다시 클릭합니다.

### <a name="issue-cloud-provider-launch-failure-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>문제: 클라우드 공급자 시작 실패: 클러스터를 설정하는 동안 클라우드 공급자 오류가 발생했습니다.

**오류 메시지**

클라우드 공급자 시작 실패: 클러스터를 설정하는 동안 클라우드 공급자 오류가 발생했습니다. 자세한 내용은 Databricks 가이드를 참조하세요. Azure 오류 코드: PublicIPCountLimitReached. Azure 오류 메시지: 이 지역에서 이 구독에 대해 60개를 초과하는 공용 IP 주소를 만들 수 없습니다.

**솔루션**

Azure Databricks 클러스터는 노드당 하나의 공용 IP 주소를 사용합니다. 구독이 이미 해당 공용 IP를 모두 사용한 경우 [할당량 증가를 요청](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request)해야 합니다. **문제 유형**으로 **할당량**, **할당량 유형**으로 **네트워킹 ARM**을 선택하고 **세부 정보**에서 공용 IP 주소 할당량 증가를 요청합니다(예를 들어 제한이 현재 60이고 100개의 노드 클러스터를 만들려는 경우 160으로 제한 증가를 요청합니다).

## <a name="next-steps"></a>다음 단계
버전 2의 데이터 팩터리를 만드는 단계별 지침은 다음 자습서를 참조하십시오.

- [빠른 시작: Azure Databricks 시작](quickstart-create-databricks-workspace-portal.md)
- [Azure Databricks란?](what-is-azure-databricks.md)

