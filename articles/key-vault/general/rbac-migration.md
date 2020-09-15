---
title: Azure 역할 기반 access control로 마이그레이션 | Microsoft Docs
description: 자격 증명 모음 액세스 정책에서 Azure 역할로 마이그레이션하는 방법에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e06a7a759c712b47f3a725a3c49a660226da6a09
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064200"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-preview-permission-model"></a>자격 증명 모음 액세스 정책에서 Azure 역할 기반 액세스 제어 (미리 보기) 권한 모델로 마이그레이션

자격 증명 모음 액세스 정책 모델은 키, 암호 및 인증서에 대 한 액세스를 제공 하기 위해 Key Vault 기본 제공 되는 기존 권한 부여 시스템입니다. Key Vault 범위에서 보안 주체 (사용자, 그룹, 서비스 주체, 관리 id)에 대 한 개별 사용 권한을 할당 하 여 액세스를 제어할 수 있습니다. 

Azure RBAC (역할 기반 access control)는 Azure 리소스에 대 한 세분화 된 액세스 관리를 제공 하는 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 을 기반으로 하는 권한 부여 시스템입니다. Key Vault 키, 암호 및 인증서 액세스 관리에 대 한 Azure RBAC는 현재 공개 미리 보기로 제공 됩니다. Azure RBAC를 사용 하 여 리소스에 대 한 액세스를 제어 합니다. 역할 할당은 보안 주체, 역할 정의 (미리 정의 된 사용 권한 집합) 및 범위 (리소스 그룹 또는 개별 리소스 그룹)의 세 요소로 구성 됩니다. 자세한 내용은 azure [역할 기반 액세스 제어 (AZURE RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)를 참조 하세요.

Azure RBAC로 마이그레이션하기 전에 이점 및 제한 사항을 이해 하는 것이 중요 합니다.

자격 증명 모음 액세스 정책에 대 한 Azure RBAC의 주요 혜택:
- Azure 리소스에 대해 통합 된 액세스 제어 모델을 제공 합니다. Azure 서비스에서 동일한 API를 제공 합니다.
- 관리자 용 중앙 액세스 관리-한 뷰에서 모든 Azure 리소스를 관리 합니다.
- 시간 기반 access control에 대 한 [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) 와 통합
- 할당 거부-특정 범위에서 보안 주체를 제외 하는 기능입니다. 자세한 내용은 [Azure 거부 할당 이해](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) 를 참조 하세요.

Azure RBAC 단점:
- 역할 할당에 대 한 대기 시간-역할 할당을 적용 하는 데 몇 분 정도 걸릴 수 있습니다. 자격 증명 모음 액세스 정책은 즉시 할당 됩니다.
- 제한 된 수의 역할 할당-2000 구독 당 역할 할당 및 1024 액세스 정책 Key Vault

## <a name="access-policies-to-azure-roles-mapping"></a>Azure 역할 매핑에 대 한 액세스 정책

Azure RBAC에는 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 할당할 수 있는 여러 Azure 기본 제공 역할이 있습니다. 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [Azure 사용자 지정 역할](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)을 만들면 됩니다.

키, 인증서 및 비밀 액세스 관리에 대 한 기본 제공 역할을 Key Vault 합니다.
- Key Vault 관리자 (미리 보기)
- Key Vault 판독기 (미리 보기)
- Key Vault 인증서 담당자 (미리 보기)
- Key Vault Crypto 담당자 (미리 보기)
- Key Vault Crypto 사용자 (미리 보기)
- Key Vault 비밀 책임자 (미리 보기)
- Key Vault 비밀 사용자 (미리 보기)

기존 기본 제공 역할에 대 한 자세한 내용은 [Azure 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 을 참조 하세요.

자격 증명 모음 액세스 정책은 개별적으로 선택 된 사용 권한 또는 미리 정의 된 권한 템플릿과 함께 할당할 수 있습니다.

액세스 정책 미리 정의 된 권한 템플릿:
- 키, 암호, 인증서 관리
- 키 & 비밀 관리
- 암호 & 인증서 관리
- 키 관리
- 비밀 관리
- 인증서 관리
- SQL Server 커넥터
- Azure Data Lake Storage 또는 Azure Storage
- Azure Backup
- Exchange Online 고객 키
- SharePoint Online 고객 키
- Azure 정보 BYOK

### <a name="access-policies-templates-to-azure-roles-mapping"></a>Azure 역할 매핑에 액세스 정책 템플릿
| 액세스 정책 템플릿 | 작업 | Azure 역할 |
| --- | --- | --- |
| 키, 암호, 인증서 관리 | 키: 모든 작업 <br>인증서: 모든 작업<br>비밀: 모든 작업 | Key Vault 관리자 (미리 보기) |
| 키 & 비밀 관리 | 키: 모든 작업 <br>비밀: 모든 작업| Key Vault Crypto 담당자 (미리 보기)<br> Key Vault 비밀 책임자 (미리 보기)|
| 암호 & 인증서 관리 | 인증서: 모든 작업 <br>비밀: 모든 작업| Key Vault 인증서 담당자 (미리 보기)<br> Key Vault 비밀 책임자 (미리 보기)|
| 키 관리 | 키: 모든 작업| Key Vault Crypto 담당자 (미리 보기)|
| 비밀 관리 | 비밀: 모든 작업| Key Vault 비밀 책임자 (미리 보기)|
| 인증서 관리 | 인증서: 모든 작업 | Key Vault 인증서 담당자 (미리 보기)|
| SQL Server 커넥터 | 키: 가져오기, 나열, 키 래핑, 키 래핑 해제 | Key Vault Crypto 서비스 암호화 (미리 보기)|
| Azure Data Lake Storage 또는 Azure Storage | 키: get, list, 래핑 해제 키 | N/A<br> 사용자 지정 역할 필요|
| Azure Backup | 키: get, list, backup<br> 인증서: get, list, backup | N/A<br> 사용자 지정 역할 필요|
| Exchange Online 고객 키 | 키: 가져오기, 나열, 키 래핑, 키 래핑 해제 | Key Vault Crypto 서비스 암호화 (미리 보기)|
| Exchange Online 고객 키 | 키: 가져오기, 나열, 키 래핑, 키 래핑 해제 | Key Vault Crypto 서비스 암호화 (미리 보기)|
| Azure 정보 BYOK | 키: get, 암호 해독, 서명 | N/A<br>사용자 지정 역할 필요|


## <a name="assignment-scopes-mapping"></a>할당 범위 매핑  

Key Vault에 대 한 Azure RBAC를 사용 하면 다음 범위에서 역할을 할당할 수 있습니다.
- 관리 그룹
- 구독
- 리소스 그룹
- Key Vault 리소스
- 개별 키, 암호 및 인증서

자격 증명 모음 액세스 정책 권한 모델은 Key Vault 리소스 수준 에서만 정책 할당으로 제한 됩니다. 

일반적으로 응용 프로그램당 키 자격 증명 모음 하나를 사용 하 고 key vault 수준에서 액세스를 관리 하는 것이 좋습니다. 다른 범위에서 액세스를 관리 하면 액세스 관리를 간소화할 수 있는 시나리오가 있습니다.

- * * 인프라, 보안 관리자 및 운영자: 자격 증명 모음 액세스 정책을 사용 하 여 관리 그룹, 구독 또는 리소스 그룹 수준에서 키 자격 증명 모음 그룹을 관리 하려면 각 주요 자격 증명 모음에 대 한 정책을 유지 관리 해야 합니다. Azure RBAC를 통해 관리 그룹, 구독 또는 리소스 그룹에서 하나의 역할 할당을 만들 수 있습니다. 이 할당은 동일한 범위에서 만든 모든 새 key vault에 적용 됩니다. 이 시나리오에서는 영구 액세스를 제공 하 여 just-in-time 액세스에 Privileged Identity Management를 사용 하는 것이 좋습니다.
 
- * * 응용 프로그램: 응용 프로그램에서 다른 응용 프로그램과 암호를 공유 해야 하는 시나리오가 있습니다. 자격 증명 모음 액세스 정책을 사용 하 여 모든 암호에 대 한 액세스 권한을 부여 하지 않도록 별도의 key vault를 만들어야 했습니다. Azure RBAC는 단일 키 자격 증명 모음을 사용 하는 대신 개별 암호의 범위에 할당 역할을 허용 합니다.

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>Azure RBAC 마이그레이션 단계에 대 한 자격 증명 모음 액세스 정책
Azure RBAC와 자격 증명 모음 액세스 정책 권한 모델 간에는 많은 차이점이 있습니다. 마이그레이션 중 중단이 발생 하지 않도록 하려면 아래 단계를 수행 하는 것이 좋습니다.
 
1. **역할 식별 및 할당**: 위의 매핑 테이블을 기반으로 기본 제공 역할을 식별 하 고 필요한 경우 사용자 지정 역할을 만듭니다. 범위 매핑 지침에 따라 범위에서 역할을 할당 합니다. 키 자격 증명 모음에 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure 역할 기반 액세스 제어를 사용 하 여 Key Vault에 대 한 액세스 제공 (미리 보기)](rbac-guide.md) 을 참조 하세요.
1. **역할 할당 유효성 검사**: Azure RBAC의 역할 할당을 전파 하는 데 몇 분 정도 걸릴 수 있습니다. 역할 할당을 확인 하는 방법에 대 한 지침은 [범위에서 역할 할당 나열](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) 을 참조 하세요.
1. **주요 자격 증명 모음에 대 한 모니터링 및 경고 구성**: 액세스 거부 예외에 대해 로깅을 설정 하 고 경고를 설정 하는 것이 중요 합니다. 자세한 내용은 [Azure Key Vault 모니터링 및 경고](https://docs.microsoft.com/azure/key-vault/general/alert) 를 참조 하세요.
1. **Key Vault에서 azure 역할 기반 액세스 제어 권한 모델 설정**: azure RBAC 권한 모델을 사용 하도록 설정 하면 기존 액세스 정책이 모두 무효화 됩니다. 오류가 발생 하는 경우에는 그대로 남아 있는 모든 기존 액세스 정책을 사용 하 여 권한 모델을 다시 전환할 수 있습니다.

> [!NOTE]
> Azure RBAC 권한 모델을 사용 하도록 설정 하면 액세스 정책 업데이트를 시도 하는 모든 스크립트가 실패 합니다. Azure RBAC를 사용 하도록 해당 스크립트를 업데이트 하는 것이 중요 합니다.

## <a name="troubleshooting"></a>문제 해결
-  몇 분 후 역할 할당이 작동 하지 않습니다. 역할 할당에 시간이 더 오래 걸릴 수 있습니다. 이러한 경우를 처리 하기 위해 코드에서 재시도 논리를 작성 하는 것이 중요 합니다.
- Key Vault 삭제 (일시 삭제) 하 고 복구할 때 역할 할당은 사라졌습니다. 현재 모든 Azure 서비스에서 일시 삭제 기능에 대 한 제한입니다. 복구 후 모든 역할 할당을 다시 만들어야 합니다.    

## <a name="learn-more"></a>자세한 정보

- [Azure RBAC 개요](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [사용자 지정 역할 자습서](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-cli)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
