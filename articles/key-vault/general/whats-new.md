---
title: Azure Key Vault의 새 기능
description: Azure Key Vault 최신 업데이트
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: dbbde397ab235068ea90280da721e3e3dc38866a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792364"
---
# <a name="whats-new-for-azure-key-vault"></a>Azure Key Vault의 새 기능

Azure Key Vault의 새 기능은 다음과 같습니다. [Azure 업데이트 Key Vault 채널](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)에도 새 기능과 개선된 기능이 공지되어 있습니다.

## <a name="october-2020"></a>2020년 10월

> [!WARNING]
> 이러한 업데이트는 Azure Key Vault 구현에 영향을 미칠 수 있습니다.

[기본적으로 지금 일시 삭제](#soft-delete-on-by-default)를 지원하기 위해 Azure Key Vault PowerShell cmdlet에 다음과 같은 두 가지 변경 사항이 적용되었습니다.

- [Update-AzKeyVault](/powershell/module/az.keyvault/update-azkeyvault)의 DisableSoftDelete 및 EnableSoftDelete 매개 변수는 더 이상 사용되지 않습니다.
- [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) cmdlet의 출력에는 더 이상 `SecretValueText` 특성이 없습니다.

## <a name="july-2020"></a>2020년 7월

> [!WARNING]
> 이 두 가지 업데이트는 Azure Key Vault 구현에 영향을 미칠 수 있습니다.

### <a name="soft-delete-on-by-default"></a>기본적으로 일시 삭제 사용

2020년이 종료되면 새 키 자격 증명 모음과 기존 키 자격 증명 모음을 포함한 **모든 키 자격 증명 모음에서 기본적으로 소프트 삭제가 사용** 됩니다. 호환성이 손상될 수 있는 이 변경 내용에 대한 자세한 내용과 영향을 받는 키 자격 증명 모음을 찾아서 미리 업데이트하는 단계에 대한 자세한 내용은 [모든 키 자격 증명 모음에 대해 사용하도록 설정되는 일시 삭제](soft-delete-change.md) 문서를 참조하세요.

### <a name="azure-tls-certificate-changes"></a>Azure TLS 인증서 변경

Microsoft는 다른 루트 CA(인증 기관)의 TLS 인증서를 사용하도록 Azure 서비스를 업데이트하 고 있습니다. 이렇게 변경하는 이유는 현재 CA 인증서가 CA/브라우저 포럼 기준 요구 사항 중 하나를 준수하지 않기 때문입니다.  자세한 내용은 [Azure TLS 인증서 변경](../../security/fundamentals/tls-certificate-changes.md)을 참조하세요.

## <a name="june-2020"></a>2020년 6월

Key Vault용 Azure Monitor는 현재 미리 보기로 제공됩니다.  Azure Monitor는 Key Vault 요청, 성능, 실패 및 대기 시간에 대한 통합 보기를 제공하여 키 자격 증명 모음의 종합적인 모니터링을 제공합니다. 자세한 내용은 [Key Vault용 Azure Monitor(미리 보기)](../../azure-monitor/insights/key-vault-insights-overview.md)를 참조하세요.

## <a name="may-2020"></a>2020년 5월

이제 Key Vault "BYOK(Bring Your Own Key)"를 일반적으로 사용할 수 있습니다. [Azure Key Vault BYOK 사양](../keys/byok-specification.md)을 살펴보고, [Key Vault로 HSM 보호 키 가져오기(BYOK)](../keys/hsm-protected-keys-byok.md) 방법에 대해 알아보세요.

## <a name="march-2020"></a>2020년 3월

이제 미리 보기에서 프라이빗 엔드포인트를 사용할 수 있습니다. Azure Private Link Service를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure Key Vault 및 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.  [Azure Private Link와 Key Vault를 통합하는 방법](private-link-service.md)을 알아보세요.

## <a name="2019"></a>2019

- 차세대 Azure Key Vault SDK가 출시되었습니다. 사용 방법은 [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) 및 [Node.js](../secrets/quick-create-node.md)에 대한 Azure Key Vault 비밀 빠른 시작을 참조하세요.
- 키 자격 증명 모음 인증서를 관리하는 새로운 Azure 정책. [Key Vault에 대한 Azure Policy 기본 제공 정의](../policy-reference.md)를 참조하세요.
- Azure Key Vault 가상 머신 확장은 이제 일반 공급됩니다.  [Linux용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-linux.md) 및 [Windows용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-windows.md)을 참조하세요.
- Azure Key Vault에 대한 이벤트 기반 비밀 관리는 현재 Azure Event Grid에서 사용할 수 있습니다. [Azure Key Vault의 이벤트에 대한 Event Grid 스키마](../../event-grid/event-schema-key-vault.md]에서 자세한 내용을 확인하고, [Azure Event Grid를 사용하여 키 자격 증명 모음 알림 수신 및 응답](event-grid-tutorial.md) 방법을 알아보세요.

## <a name="2018"></a>2018

올해 출시된 새로운 기능 및 통합:

- Azure Functions와 통합됩니다. 키 자격 증명 모음 작업에 [Azure Functions](../../azure-functions/index.yml)를 활용하는 예제 시나리오는 [비밀 순환 자동화](../secrets/tutorial-rotation.md)를 참조하세요.
- [Azure Databricks와 통합](/azure/databricks/scenarios/store-secrets-azure-key-vault). 이 통합을 통해 이제 Azure Databricks는 두 가지 유형의 비밀 범위 Azure Key Vault 지원 및 Databricks 지원을 제공합니다. 자세한 내용은 [Azure Key Vault에서 지원하는 비밀 범위 만들기](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)를 참조하세요.
- [Azure Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)

## <a name="2016"></a>2016

올해 출시된 새로운 기능:

- 관리형 스토리지 계정 키. Azure Storage와 더 쉽게 통합되는 스토리지 계정 키 기능을 추가했습니다. 자세한 내용은 개요 토픽 [관리 스토리지 계정 키 개요](../secrets/overview-storage-keys.md)를 참조하세요.
- 일시 삭제. 일시 삭제 기능은 키 자격 증명 모음 및 키 자격 증명 모음 개체의 데이터 보호를 개선합니다. 자세한 내용은 [일시 삭제 개요](./soft-delete-overview.md) 토픽을 참조하세요.

## <a name="2015"></a>2015

올해 출시된 새로운 기능:
- 인증서 관리. 2016년 9월 26일에 GA 버전 2015-06-01에 추가된 기능입니다.

일반 공급(버전 2015-06-01)은 2015년 6월 24일에 발표되었습니다. 이 릴리스에서 다음과 같은 사항이 변경되었습니다.
- 키 삭제 - "사용" 필드를 제거했습니다.
- 키에 대한 정보 가져오기 - "사용" 필드를 제거했습니다.
- 키 자격 증명 모음에 키 가져오기 - "사용" 필드를 제거했습니다.
- 키 복원 - "사용" 필드를 제거했습니다.
- RSA 알고리즘에 대한 "RSA_OAEP"를 "RSA-OAEP"로 변경했습니다. [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-certificates.md)를 참조하세요.

두 번째 미리보기 버전(2015-02-01-preview)은 2015년 4월 20일에 발표되었습니다. 자세한 내용은 [REST API 업데이트](/archive/blogs/kv/rest-api-update)를 참조하세요. 다음 작업을 업데이트했습니다.

- 자격 증명 모음에 키 나열 - 작업에 페이지 매김 지원을 추가했습니다.
- 키 버전 나열 - 키 버전을 나열하는 작업을 추가했습니다.
- 자격 증명 모음에 비밀 나열 - 페이지 매김 지원을 추가했습니다.
- 비밀 버전 나열 - 비밀 버전을 나열하는 작업을 추가합니다.
- 모든 작업 - 특성에 만든/업데이트한 타임스탬프를 추가했습니다.
- 비밀 만들기 - 비밀에 콘텐츠 형식을 추가했습니다.
- 키 만들기 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀 만들기 - 선택 사항 정보로 태그를 추가했습니다.
- 키 업데이트 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀 업데이트 - 선택 사항 정보로 태그를 추가했습니다.
- 비밀의 최대 크기를 10K에서 25K 바이트로 변경했습니다. [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-certificates.md)를 참조하세요.

## <a name="2014"></a>2014

첫 번째 미리보기 버전(2014-12-08-preview)은 2015년 1월 8일에 발표되었습니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있는 경우 [지원](https://azure.microsoft.com/support/options/)을 통해 문의하시기 바랍니다.