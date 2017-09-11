---
title: "Azure Key Vault의 고객 관리 키를 사용하는 Azure Storage 서비스 암호화 | Microsoft Docs"
description: "Azure Storage 서비스 암호화 기능을 사용하여 데이터를 저장할 때 서비스 쪽에서 Azure Blob Storage를 암호화하고 고객 관리 키를 사용하여 데이터를 검색할 때 암호 해독합니다."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.contentlocale: ko-kr
ms.lasthandoff: 08/23/2017

---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화

Microsoft Azure는 고객 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호하도록 지원하는 데 최선을 다하고 있습니다.  미사용 데이터를 보호할 수 있는 한 가지 방법은 자동으로 데이터를 저장소에 쓸 때 암호화하고 데이터를 검색할 때 암호 해독하는 SSE(저장소 서비스 암호화)를 사용하는 것입니다. 암호화 및 암호 해독은 자동으로 적용되며 완전히 투명하고, 사용 가능한 가장 강력한 블록 암호 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용합니다.

SSE를 사용하는 Microsoft 관리 암호화 키를 사용하거나 사용자 고유의 암호화 키를 사용할 수 있습니다. 이 문서는 후자에 대해 설명합니다. Microsoft 관리 키 사용 또는 일반적인 SSE에 대한 자세한 내용은 [미사용 데이터에 대한 저장소 서비스 암호화](../storage-service-encryption.md)를 참조하세요.

사용자 고유의 암호화 키 사용 기능을 제공하기 위해 Blob 저장소에 대한 SSE가 AKV(Azure Key Vault)와 통합됩니다. 사용자 고유의 암호화 키를 만들고 AKV에 저장할 수도 있고 AKV의 API를 사용하여 암호화 키를 생성할 수도 있습니다. AKV를 사용하면 키를 관리하고 제어할 수 있을 뿐만 아니라 키 사용을 감사할 수도 있습니다. 

사용자 고유의 키를 만드는 이유 좀 더 유연하게 작업할 수 있으며 액세스 제어를 만들고, 순환하고, 사용할 수 없게 설정하고, 정의할 수 있습니다. 또한 데이터를 보호하는 데 사용된 암호화 키를 감사할 수 있습니다.

## <a name="sse-with-customer-managed-keys-preview"></a>고객 관리 키를 사용하는 SSE 미리 보기

이 기능은 현재 미리 보기로 제공됩니다. 이 기능을 사용하려면 새 저장소 계정을 만들어야 합니다. 새 키 자격 증명 모음 및 키를 만들 수도 있고 기존 키 자격 증명 모음 및 키를 사용할 수도 있습니다. 저장소 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다.

미리 보기에 참여하려면 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)에 문의하세요. 미리 보기에 참여할 특별 링크를 제공해 드립니다.

자세히 알아보려면 [FAQ](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest)를 참조하세요.

> [!IMPORTANT]
> 이 문서의 단계를 수행하기 전에 미리 보기에 등록해야 합니다. 미리 보기 액세스 권한이 없는 경우 포털에서 이 기능을 사용하도록 설정할 수 없습니다.

## <a name="getting-started"></a>시작하기
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>1단계: [새 저장소 계정 만들기](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>2단계: 암호화 사용
[Azure Portal](https://portal.azure.com)을 사용하여 저장소 계정에 대해 SSE를 사용하도록 설정할 수 있습니다. 저장소 계정의 설정 블레이드에서 다음 그림에 표시된 것처럼 Blob 서비스 섹션을 찾은 후 암호화를 클릭합니다.

![암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Blob 서비스에 대해 SSE 사용*

저장소 계정에 대해 저장소 서비스 암호화를 프로그래밍 방식으로 사용 또는 사용하지 않으려면 [Azure Storage Resource Provider REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN), [.NET용 Storage Resource Provider 클라이언트 라이브러리](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN), [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0) 또는 [Azure CLI](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli)를 사용하면 됩니다.

이 화면에서 "use your own key"(사용자 고유 키 사용) 확인란이 표시되지 않는 경우 미리 보기가 승인되지 않은 것입니다. [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)으로 메일을 보내 승인을 요청합니다.

![암호화 미리 보기를 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

기본적으로 SSE는 Microsoft 관리 키를 사용합니다. 사용자 고유의 키를 사용하려면 해당 확인란을 선택합니다. 그런 다음 키 URI를 지정하거나 선택기에서 키 및 Key Vault를 선택할 수 있습니다.

## <a name="step-3-select-your-key"></a>3단계: 키 선택

![암호화 사용자 고유의 키 사용 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![키 URI 입력을 사용하는 암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

저장소 계정에 Key Vault에 대한 액세스 권한이 없는 경우 Azure Powershell을 통해 다음 명령을 실행하여 필요한 Key Vault에 대한 액세스 권한을 저장소 계정에 부여할 수 있습니다.

![Key Vault에 대해 거부된 액세스를 보여 주는 포털 스크린샷](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

또한 Azure Portal에서 Azure Key Vault로 이동하여 저장소 계정에 액세스 권한을 부여하는 방법으로 Azure Portal을 통해 액세스 권한을 부여할 수 있습니다.

## <a name="step-4-copy-data-to-storage-account"></a>4단계: 저장소 계정에 데이터 복사
새 저장소 계정으로 데이터를 전송하여 암호화되도록 하려면 [3단계의 미사용 데이터에 대한 저장소 서비스 암호화에서 시작](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account)을 참조하세요.

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>5단계: 암호화된 데이터 상태 쿼리
암호화된 데이터 상태를 쿼리하려면 [4단계의 미사용 데이터에 대한 저장소 서비스 암호화에서 시작](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data)을 참조하세요.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>미사용 데이터에 대한 저장소 서비스 암호화에 대한 질문과 대답
**Q: Premium Storage를 사용 중입니다. 고객 관리 키를 사용하는 SSE를 사용할 수 있나요?**

A: 예, Standard storage 및 Premium Storage 모두에서 Microsoft 관리 키 및 고객 관리 키를 사용하는 SSE가 지원됩니다. 

**Q: Azure PowerShell 및 Azure CLI를 사용하여 고객 관리 키를 사용하는 SSE로 새 저장소 계정을 만들 수 있나요?**

A: 예.

**Q: 고객 관리 키를 사용하는 SSE를 사용하는 경우 Azure Storage 비용은 얼마나 늘어나나요?**

A: Azure Key Vault 사용과 연관된 비용이 있습니다. 자세한 내용은 [Key Vault 가격](https://azure.microsoft.com/en-us/pricing/details/key-vault/)을 참조하세요. SSE 사용에 따른 추가 비용은 없습니다.

**Q: 암호화 키에 대한 액세스를 해지할 수 있나요?**

A: 예, 언제든 액세스를 취소할 수 있습니다. 키에 대한 액세스를 취소하는 방법은 여러 가지가 있습니다. 자세한 내용은 [Azure Key Vault PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) 및 [Azure Key Vault CLI](https://docs.microsoft.com/en-us/cli/azure/keyvault)를 참조하세요. 액세스를 취소하면 Azure Storage에서 계정 암호화 키에 액세스할 수 없으므로 저장소 계정의 모든 Blob에 대한 액세스가 효과적으로 차단됩니다.

**Q: 저장소 계정 및 키를 다른 지역에 만들 수 있나요?**

A: 아니요, 저장소 계정 및 Key Vault/키는 동일한 지역에 있어야 합니다. 

**Q: 저장소 계정을 만들면서 고객 관리 키를 사용하는 SSE를 사용할 수 있나요?**

A: 아니요. 저장소 계정을 만들면서 SSE를 사용하는 경우 Microsoft 관리 키만 사용할 수 있습니다. 고객 관리 키를 사용하려면 저장소 계정 속성을 업데이트해야 합니다. REST 또는 저장소 클라이언트 라이브러리 중 하나를 사용하여 프로그래밍 방식으로 저장소 계정을 업데이트하거나 계정을 만든 후 Azure Portal을 사용하여 저장소 계정 속성을 업데이트할 수 있습니다.

**Q: 고객 관리 키를 사용하는 SSE를 사용하면서 암호화를 사용하지 않을 수 있나요?**

A: 아니요, 고객 관리 키를 사용하는 SSE를 사용하면서 암호화를 사용하지 않을 수 없습니다. 암호화를 사용하지 않으려면 Microsoft 관리 키를 사용하도록 전환해야 합니다. 이 작업은 Azure Portal 또는 PowerShell을 사용하여 수행할 수 있습니다.

**Q: 새 저장소 계정을 만들 때 기본적으로 SSE가 사용되도록 설정되어 있나요?**

A: SSE는 기본적으로 사용되지 않습니다. Azure 포털로 사용하도록 설정할 수 있습니다. 또한 프로그래밍 방식으로 저장소 리소스 공급자 REST API를 사용하여 이 기능을 사용하도록 설정할 수 있습니다. 

**Q: 내 저장소 계정에서 암호화를 사용할 수 없습니다.**

A: Resource Manager 저장소 계정인가요? 클래식 저장소 계정은 지원되지 않습니다. 고객 관리 키를 사용하는 SSE는 새로 만든 리소스 관리자 저장소 계정에서만 사용할 수 있습니다.

**Q: 고객 관리 키를 사용하는 SSE는 특정 지역에서만 허용되나요?**

A: 이 미리 보기의 경우 Blob storage에 대한 SSE는 특정 지역에서만 사용할 수 있습니다. 미리 보기에 대한 세부 정보와 가용성을 확인하려면 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)으로 메일을 보내주세요. 

**Q: 문제가 있거나 피드백을 제공하고 싶은 경우 어떻게 연락하나요?**

A: 저장소 서비스 암호화에 대한 문제는 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)으로 문의하세요. 

## <a name="next-steps"></a>다음 단계

*   개발자가 보안 응용 프로그램을 빌드하는 데 도움이 되는 포괄적인 보안 기능 집합에 대한 자세한 내용은 [Storage 보안 가이드](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide)를 참조하세요.
*   Azure Key Vault에 대한 개요는 [Azure Key Vault란](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)?을 참조하세요.
*   Azure Key Vault 시작 방법은 [Azure Key Vault 시작](../../key-vault/key-vault-get-started.md)을 참조하세요.

