---
title: "Azure Active Directory용 MSI(관리 서비스 ID)"
description: "Azure 리소스용 관리 서비스 ID에 대해 대략적으로 설명합니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 95980c082b09ad959ab8bbaae0250b40ac08d2c8
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Azure 리소스용 MSI(관리 서비스 ID)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

클라우드 응용 프로그램을 빌드할 때 일반적으로 발생하는 문제 중 하나는 클라우드 서비스에 인증하기 위해 코드에 포함해야 하는 자격 증명을 관리하는 방법입니다. 즉, 이러한 자격 증명의 보안을 유지하는 것이 중요합니다. 이러한 자격 증명은 개발자 워크스테이션에 표시되거나 소스 컨트롤에 체크 인되지 않는 것이 가장 좋습니다. Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. MSI(관리 서비스 ID)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

## <a name="how-does-it-work"></a>작동 원리

Azure 서비스 인스턴스에서 관리 서비스 ID를 사용할 때는 Azure 구독에 사용되는 Azure AD 테넌트에 ID가 생성됩니다. 또한 Azure는 해당 ID용 자격 증명을 서비스 인스턴스에 프로비전합니다. 그러면 코드가 Azure AD 인증을 지원하는 서비스용 액세스 토큰을 가져오기 위한 로컬 요청을 할 수 있습니다. 이러한 모든 작업이 수행되는 동안 Azure는 서비스 인스턴스에 사용되는 자격 증명 롤링을 처리합니다.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>리소스가 관리 서비스 ID를 사용하도록 설정하려면 어떻게 해야 하나요?

사용 가능한 관리 서비스 ID의 유형은 *시스템 할당* ID와 *사용자 할당* ID의 두 가지입니다.

- **시스템 할당** MSI는 Azure 서비스 인스턴스에서 직접 사용하도록 설정됩니다. 이처럼 Azure는 MSI를 사용하도록 설정하는 프로세스를 통해 Azure AD 테넌트에 서비스 인스턴스용 ID를 만들고 해당 ID용 자격 증명을 서비스 인스턴스에 프로비전합니다. 시스템 할당 MSI의 수명 주기는 MSI가 사용하도록 설정된 Azure 서비스 인스턴스와 직접적으로 연관됩니다. 서비스 인스턴스가 삭제되면 Azure는 Azure AD에서 ID와 자격 증명을 자동으로 정리합니다.

- **사용자 할당** MSI*(비공개 미리 보기)*는 독립 실행형 Azure 리소스로 생성됩니다. Azure는 생성 프로세스를 통해 Azure AD 테넌트에 ID를 만듭니다. 생성된 ID는 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자 할당 MSI는 여러 Azure 서비스 인스턴스에서 사용될 수 있으므로 해당 수명 주기는 개별적으로 관리됩니다.

아래에는 시스템 할당 MSI가 Azure Virtual Machines에서 작동하는 방식의 예가 나와 있습니다.

![Virtual Machine MSI 예제](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager가 VM에서 시스템 할당 MSI를 사용하도록 설정하라는 메시지를 받습니다.
2. Azure Resource Manager가 Azure AD에서 VM의 ID를 나타내는 서비스 주체를 만듭니다. 서비스 주체는 이 구독이 신뢰하는 Azure AD 테넌트에서 작성됩니다.
3. Azure Resource Manager가 VM의 MSI VM 확장에서 서비스 주체 세부 정보를 구성합니다. 이 단계에서는 확장에서 사용하는 클라이언트 ID와 인증서가 Azure AD에서 액세스 토큰을 가져오도록 구성합니다.
4. 이제 VM의 서비스 주체 ID가 확인되었으므로 Azure 리소스 액세스 권한을 해당 ID에 부여할 수 있습니다. 예를 들어 코드가 Azure Resource Manager를 호출해야 하는 경우 Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 VM 서비스 주체에 적절한 역할을 할당합니다.  코드가 Key Vault를 호출해야 하는 경우에는 Key Vault의 특정 비밀이나 키 액세스 권한을 코드에 부여합니다.
5. VM에서 실행되는 코드가 MSI VM 확장이 호스팅하는 로컬 끝점(http://localhost:50342/oauth2/token)의 토큰을 요청합니다. 리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. 예를 들어 코드가 Azure Resource Manager에 인증하도록 하려면 resource=https://management.azure.com/을 사용합니다.
6. MSI VM 확장은 구성된 클라이언트 ID 및 인증서를 사용하여 Azure AD에서 액세스 토큰을 요청합니다.  Azure AD가 JWT(JSON Web Token) 액세스 토큰을 반환합니다.
7. 코드가 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다.

아래에는 동일한 다이어그램을 통해 사용자 할당 MSI가 Azure Virtual Machines에서 작동하는 방식을 보여 주는 예가 나와 있습니다.

![Virtual Machine MSI 예제](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager가 사용자 할당 MSI를 만들라는 메시지를 받습니다.
2. Azure Resource Manager가 Azure AD에서 MSI의 ID를 나타내는 서비스 주체를 만듭니다. 서비스 주체는 이 구독이 신뢰하는 Azure AD 테넌트에서 작성됩니다.
3. Azure Resource Manager가 VM의 MSI VM 확장에서 서비스 주체 세부 정보를 구성하라는 메시지를 받습니다. 이 단계에서는 확장에서 사용하는 클라이언트 ID와 인증서가 Azure AD에서 액세스 토큰을 가져오도록 구성합니다.
4. 이제 MSI의 서비스 주체 ID가 확인되었으므로 Azure 리소스 액세스 권한을 해당 ID에 부여할 수 있습니다. 예를 들어 코드가 Azure Resource Manager를 호출해야 하는 경우 Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 MSI 서비스 주체에 적절한 역할을 할당합니다. 코드가 Key Vault를 호출해야 하는 경우에는 Key Vault의 특정 비밀이나 키 액세스 권한을 코드에 부여합니다. 참고: 4단계를 완료하기 위해 3단계를 반드시 완료해야 하는 것은 아닙니다. MSI가 있으면 VM에서 구성되어 있는지 여부에 관계없이 리소스 액세스 권한을 부여할 수 있습니다.
5. VM에서 실행되는 코드가 MSI VM 확장에서 호스트되는 로컬 끝점(http://localhost:50342/oauth2/token)에서 토큰을 요청합니다. 클라이언트 ID 매개 변수는 사용할 MSI ID의 이름을 지정합니다. 또한 리소스 매개 변수는 토큰을 전송할 서비스를 지정합니다. 예를 들어 코드가 Azure Resource Manager에 인증하도록 하려면 resource=https://management.azure.com/을 사용합니다.
6. MSI VM 확장은 요청한 클라이언트용 인증서가 구성되어 있는지 확인하고 Azure AD의 액세스 토큰을 요청합니다. Azure AD가 JWT(JSON Web Token) 액세스 토큰을 반환합니다.
7. 코드가 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다.

관리 서비스 ID를 지원하는 각 Azure 서비스에는 코드가 액세스 토큰을 가져오는 데 사용할 수 있는 고유한 방법이 있습니다. 토큰을 가져오는 구체적인 방법을 확인하려면 각 서비스의 자습서를 참조하세요.

## <a name="try-managed-service-identity"></a>관리 서비스 ID 사용해 보기

다른 Azure 리소스에 액세스하기 위한 종단 간 시나리오에 대해 알아보려면 관리되는 서비스 ID 자습서를 시도해 보세요.
<br><br>
| MSI 사용 리소스에서 | 학습할 방법 |
| ------- | -------- |
| Azure VM(Linux)   | [Linux VM 관리 서비스 ID를 사용하여 Azure Resource Manager 액세스](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Linux VM 관리 서비스 ID를 사용하여 액세스 키를 통해 Azure Storage 액세스](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>관리 서비스 ID를 지원하는 Azure 서비스

관리 서비스 ID를 지원하는 Azure 서비스는 MSI를 사용하여 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다.  현재 Azure 전반에 걸쳐 MSI와 Azure AD 인증을 통합하는 과정이 진행되고 있습니다.  업데이트를 자주 확인해 주세요.

### <a name="azure-services-that-support-managed-service-identity"></a>관리 서비스 ID를 지원하는 Azure 서비스

관리 서비스 ID를 지원하는 Azure 서비스는 다음과 같습니다.

| 서비스 | 상태 | Date | 구성 | 토큰 가져오기 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | 미리 보기 | 2017년 9월 | [Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Azure 리소스 관리자 템플릿](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, 관리 서비스 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

| 서비스 | 리소스 ID | 상태 | Date | 액세스 권한 할당 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure 리소스 관리자 | https://management.azure.com/ | 사용 가능 | 2017년 9월 | [Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | 사용 가능 | 2017년 9월 | |
| Azure Data Lake | https://datalake.azure.net/ | 사용 가능 | 2017년 9월 | |
| Azure SQL | https://database.windows.net/ | 사용 가능 | 2017년 10월 | |

## <a name="how-much-does-managed-service-identity-cost"></a>관리 서비스 ID의 비용은 어느 정도인가요?

관리 서비스 ID는 Azure 구독에 기본적으로 포함된 Azure Active Directory Free와 함께 제공됩니다.  즉, 관리 서비스 ID에 대한 추가 비용은 없습니다.

## <a name="support-and-feedback"></a>지원 및 피드백

많은 의견 부탁드립니다!

* Stack Overflow 관련 작업 방법 질문은 [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi) 태그를 포함하여 작성해 주세요.
* [개발자용 Azure AD 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)에서 기능을 요청하거나 피드백을 보내 주세요.






