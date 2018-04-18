---
title: Azure Active Directory용 MSI(관리 서비스 ID)
description: Azure 리소스용 관리 서비스 ID에 대해 대략적으로 설명합니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: e4f9d9e4e0f84610ad072d889abf68b62c0dd41f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Azure 리소스용 MSI(관리 서비스 ID)

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

클라우드 응용 프로그램을 빌드할 때 일반적으로 발생하는 문제 중 하나는 클라우드 서비스에 인증하기 위해 코드에 포함해야 하는 자격 증명을 관리하는 방법입니다. 즉, 이러한 자격 증명의 보안을 유지하는 것이 중요합니다. 이러한 자격 증명은 개발자 워크스테이션에 표시되거나 소스 컨트롤에 체크 인되지 않는 것이 가장 좋습니다. Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. MSI(관리 서비스 ID)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

## <a name="how-does-it-work"></a>작동 원리

Azure 서비스에 대해 관리 서비스 ID를 사용하도록 설정하면 Azure는 구독에서 사용하는 Azure AD 테넌트의 서비스 인스턴스용 ID를 자동으로 만듭니다.  내부적으로 Azure는 서비스 인스턴스에 ID의 자격 증명을 프로비전합니다.  그러면 코드가 로컬 요청을 수행하여 Azure AD 인증을 지원하는 서비스용 액세스 토큰을 가져올 수 있습니다.  서비스 인스턴스가 사용하는 자격 증명 롤링은 Azure에서 처리합니다.  서비스 인스턴스가 삭제되면 Azure는 Azure AD에서 ID와 자격 증명을 자동으로 정리합니다.

Azure Virtual Machines에서 관리 서비스 ID가 작동하는 방식의 예제는 다음과 같습니다.

![Virtual Machine MSI 예제](../media/msi-vm-example.png)

1. Azure Resource Manager가 VM에서 MSI를 사용하도록 설정하라는 메시지를 받습니다.
2. Azure Resource Manager가 Azure AD에서 VM의 ID를 나타내는 서비스 주체를 만듭니다. 서비스 주체는 이 구독이 신뢰하는 Azure AD 테넌트에서 작성됩니다.
3. Azure Resource Manager가 VM의 MSI VM 확장에서 서비스 주체 세부 정보를 구성합니다.  이 단계에서는 확장에서 사용하는 클라이언트 ID와 인증서가 Azure AD에서 액세스 토큰을 가져오도록 구성합니다.
4. 이제 VM의 서비스 주체 ID가 확인되었으므로 Azure 리소스 액세스 권한을 해당 ID에 부여할 수 있습니다.  예를 들어 코드가 Azure Resource Manager를 호출해야 하는 경우 Azure AD에서 RBAC(역할 기반 액세스 제어)를 사용하여 VM 서비스 주체에 적절한 역할을 할당합니다.  코드가 Key Vault를 호출해야 하는 경우에는 Key Vault의 특정 비밀이나 키 액세스 권한을 코드에 부여합니다.
5. VM에서 실행되는 코드가 MSI VM 확장이 호스팅하는 로컬 엔드포인트의 토큰을 요청합니다. http://localhost:50342/oauth2/token  리소스 매개 변수가 토큰을 보낼 대상 서비스를 지정합니다. 예를 들어 코드가 Azure Resource Manager에 인증하도록 하려면 resource=https://management.azure.com/을 사용합니다.
6. MSI VM 확장은 구성된 클라이언트 ID 및 인증서를 사용하여 Azure AD에서 액세스 토큰을 요청합니다.  Azure AD가 JWT(JSON Web Token) 액세스 토큰을 반환합니다.
7. 코드가 Azure AD 인증을 지원하는 서비스에 대한 호출에서 액세스 토큰을 전송합니다.

관리 서비스 ID를 지원하는 각 Azure 서비스에는 코드가 액세스 토큰을 가져오는 데 사용할 수 있는 고유한 방법이 있습니다. 토큰을 가져오는 구체적인 방법을 확인하려면 각 서비스의 자습서를 참조하세요.

## <a name="try-managed-service-identity"></a>관리 서비스 ID 사용해 보기

다른 Azure 리소스에 액세스하기 위한 종단 간 시나리오에 대해 알아보려면 관리되는 서비스 ID 자습서를 시도해 보세요.
<br><br>
| MSI 사용 리소스에서 | 학습할 방법 |
| ------- | -------- |
| Azure VM(Windows) | [Windows VM 관리 서비스 ID를 사용하여 Azure Data Lake Store에 액세스](tutorial-windows-vm-access-datalake.md) |
|                    | [Windows VM 관리 서비스 ID를 사용하여 Azure Resource Manager 액세스](tutorial-windows-vm-access-arm.md) |
|                    | [Windows VM 관리 서비스 ID를 사용하여 Azure SQL 액세스](tutorial-windows-vm-access-sql.md) |
|                    | [Windows VM 관리 서비스 ID를 사용하여 액세스 키를 통해 Azure Storage 액세스](tutorial-windows-vm-access-storage.md) |
|                    | [Windows VM 관리 서비스 ID를 사용하여 SAS를 통해 Azure Storage 액세스](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Windows VM 관리 서비스 ID 및 Azure Key Vault를 사용한 비 Azure AD 리소스 액세스](tutorial-windows-vm-access-nonaad.md) |
| Azure VM(Linux)   | [Linux VM 관리 서비스 ID를 사용하여 Azure Data Lake Store에 액세스](tutorial-linux-vm-access-datalake.md) |
|                    | [Linux VM 관리 서비스 ID를 사용하여 Azure Resource Manager 액세스](tutorial-linux-vm-access-arm.md) |
|                    | [Linux VM 관리 서비스 ID를 사용하여 액세스 키를 통해 Azure Storage 액세스](tutorial-linux-vm-access-storage.md) |
|                    | [ VM 관리 서비스 ID를 사용하여 SAS를 통해 Azure Storage 액세스](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Linux VM 관리 서비스 ID 및 Azure Key Vault를 사용한 비 Azure AD 리소스 액세스](tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Azure App Service 또는 Azure Functions를 통한 관리 서비스 ID 사용](/azure/app-service/app-service-managed-service-identity) |
| Azure 기능    | [Azure App Service 또는 Azure Functions를 통한 관리 서비스 ID 사용](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Azure Service Bus를 통한 관리 서비스 ID 사용](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Azure Event Hubs를 통한 관리 서비스 ID 사용](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>관리 서비스 ID를 지원하는 Azure 서비스

관리 서비스 ID를 지원하는 Azure 서비스는 MSI를 사용하여 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다.  현재 Azure 전반에 걸쳐 MSI와 Azure AD 인증을 통합하는 과정이 진행되고 있습니다.  업데이트를 자주 확인해 주세요.

### <a name="azure-services-that-support-managed-service-identity"></a>관리 서비스 ID를 지원하는 Azure 서비스

관리 서비스 ID를 지원하는 Azure 서비스는 다음과 같습니다.

| 서비스 | 상태 | Date | 구성 | 토큰 가져오기 |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | 미리 보기 | 2017년 9월 | [Azure Portal](qs-configure-portal-windows-vm.md)<br>[PowerShell](qs-configure-powershell-windows-vm.md)<br>[Azure CLI](qs-configure-cli-windows-vm.md)<br>[Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md) | [REST](how-to-use-vm-token.md#get-a-token-using-http)<br>[.NET](how-to-use-vm-token.md#get-a-token-using-c)<br>[Bash/Curl](how-to-use-vm-token.md#get-a-token-using-curl)<br>[Go](how-to-use-vm-token.md#get-a-token-using-go)<br>[PowerShell](how-to-use-vm-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | 미리 보기 | 2017년 9월 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager 템플릿](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions<sup>1</sup> | 미리 보기 | 2017년 9월 | [Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Azure Resource Manager 템플릿](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | 미리 보기 | 2017년 11월 | [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

<sup>1</sup> Azure Functions 지원을 통해 사용자 코드에서 ID를 사용할 수 있지만 트리거와 바인딩에는 연결 문자열이 여전히 필요할 수 있습니다.

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, 관리 서비스 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

| 서비스 | 리소스 ID | 상태 | Date | 액세스 권한 할당 |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure 리소스 관리자 | https://management.azure.com | 사용 가능 | 2017년 9월 | [Azure Portal](howto-assign-access-portal.md) <br>[PowerShell](howto-assign-access-powershell.md) <br>[Azure CLI](howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | 사용 가능 | 2017년 9월 | |
| Azure Data Lake | https://datalake.azure.net | 사용 가능 | 2017년 9월 | |
| Azure SQL | https://database.windows.net | 사용 가능 | 2017년 10월 | |
| Azure Event Hubs | https://eventhubs.azure.net | 사용 가능 | 2017년 12월 | |
| Azure Service Bus | https://servicebus.azure.net | 사용 가능 | 2017년 12월 | |

## <a name="how-much-does-managed-service-identity-cost"></a>관리 서비스 ID의 비용은 어느 정도인가요?

관리 서비스 ID는 Azure 구독에 기본적으로 포함된 Azure Active Directory Free와 함께 제공됩니다.  즉, 관리 서비스 ID에 대한 추가 비용은 없습니다.

## <a name="support-and-feedback"></a>지원 및 피드백

많은 의견 부탁드립니다!

* Stack Overflow 관련 작업 방법 질문은 [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi) 태그를 포함하여 작성해 주세요.
* [개발자용 Azure AD 피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)에서 기능을 요청하거나 피드백을 보내 주세요.






