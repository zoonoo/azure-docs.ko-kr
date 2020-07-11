---
title: Azure Key Vault 버전
description: 다양한 버전의 Azure Key Vault
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: mbaldwin
ms.openlocfilehash: 58c5fdcfef5e866d0c69d65412582c0ed649f7c6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86230828"
---
# <a name="key-vault-versions"></a>키 자격 증명 모음 버전

Azure Key Vault를 사용 하는 같습니다 새로운 기능입니다. 새 기능 및 향상 된 기능에 대해서는 [Azure 업데이트 Key Vault 채널](https://azure.microsoft.com/updates/?category=security&query=Key%20vault)에서도 발표 됩니다.

## <a name="june-2020"></a>2020년 6월

Key Vault에 대 한 Azure Monitor는 현재 미리 보기로 제공 됩니다.  Azure Monitor는 Key Vault 요청, 성능, 실패 및 대기 시간에 대 한 통합 보기를 제공 하 여 주요 자격 증명 모음에 대 한 종합적인 모니터링을 제공 합니다. 자세한 내용은 [Azure Monitor for Key Vault (미리 보기)](../../azure-monitor/insights/key-vault-insights-overview.md)를 참조 하세요.

## <a name="may-2020"></a>2020년 5월

이제 BYOK (사용자의 키로 가져오기)를 사용 하는 것이 일반적으로 사용 됩니다. Key Vault [AZURE KEY VAULT byok 사양을](../keys/byok-specification.md)참조 하 고 [HSM 보호 된 키를 Key Vault (Byok)로 가져오는](../keys/hsm-protected-keys-byok.md)방법에 대해 알아봅니다.

## <a name="march-2020"></a>2020년 3월

이제 미리 보기에서 개인 끝점을 사용할 수 있습니다. Azure 개인 링크 서비스를 사용 하면 가상 네트워크의 개인 끝점을 통해 Azure Key Vault 및 Azure에서 호스트 되는 고객/파트너 서비스에 액세스할 수 있습니다.  [Azure 개인 링크를 사용 하 여 Key Vault를 통합](private-link-service.md)하는 방법에 대해 알아봅니다.

## <a name="2019"></a>2019

- 차세대 Azure Key Vault Sdk 릴리스. 사용 예제는 [Python](../secrets/quick-create-python.md), [.net](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md)및 [Node.js](../secrets/quick-create-node.md) 에 대 한 Azure Key Vault 비밀 퀵 스타트를 참조 하세요.
- 주요 자격 증명 모음 인증서를 관리 하는 새로운 Azure 정책. [Key Vault에 대 한 Azure Policy 기본 제공 정의](../policy-samples.md)를 참조 하세요.
- Azure Key Vault 가상 머신 확장은 이제 일반 공급 됩니다.  [Linux 용 가상 머신 확장 Key Vault](../../virtual-machines/extensions/key-vault-linux.md) 및 [Windows 용 가상 머신 확장 Key Vault](../../virtual-machines/extensions/key-vault-windows.md)을 참조 하세요.
- 현재 Azure Event Grid에서 사용할 수 있는 Azure Key Vault에 대 한 이벤트 기반 비밀 관리. 자세한 내용은 [Azure Key Vault 이벤트의 Event Grid 스키마] (.를 참조 하십시오. /.. /event-grid/event-schema-key-vault.md]를 입력 하 고 [Azure Event Grid를 사용 하 여 주요 자격 증명 모음 알림을 수신 하 고 응답](event-grid-tutorial.md)하는 방법을 알아봅니다.

## <a name="2018"></a>2018

올해 출시 된 새로운 기능 및 통합:

- Azure Functions와 통합 됩니다. 주요 자격 증명 모음 작업에 [Azure Functions](../../azure-functions/index.yml) 를 활용 하는 예제 시나리오는 [비밀의 회전 자동화](../secrets/tutorial-rotation.md)를 참조 하세요. 
- [Azure Databricks와 통합](/azure/databricks/scenarios/store-secrets-azure-key-vault)됩니다. 이제 Azure Databricks에서는 Azure Key Vault 지원 및 Databricks 지원 이라는 두 가지 유형의 비밀 범위를 지원 합니다. 자세한 내용은 [Azure Key Vault 지원 비밀 범위 만들기](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) 를 참조 하세요.
- [Azure Key Vault에 대 한 가상 네트워크 서비스 끝점](overview-vnet-service-endpoints.md)입니다.

## <a name="2016"></a>2016

올해 출시 된 새로운 기능:

- 관리 되는 저장소 계정 키입니다. 저장소 계정 키 기능을 사용 하면 Azure Storage와 쉽게 통합할 수 있습니다. 자세한 내용은 개요 토픽 [관리 스토리지 계정 키 개요](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys)를 참조하세요.
- 일시 삭제. 일시 삭제 기능은 주요 자격 증명 모음 및 주요 자격 증명 모음 개체의 데이터 보호를 향상 시킵니다. 자세한 내용은 [일시 삭제 개요](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 토픽을 참조하세요.

## <a name="2015"></a>2015

올해 출시 된 새로운 기능:
- 인증서 관리. 2016 26 월 26 일에 GA 버전 2015-06-01에 기능으로 추가 되었습니다.

일반 공급 (버전 2015-06-01)은 6 월 24 2015 일에 발표 되었습니다. 이 릴리스에서 다음과 같은 사항이 변경되었습니다. 
- 키 삭제-"사용" 필드가 제거 되었습니다.
- 키-"사용" 필드에 대 한 정보를 가져옵니다.
- 자격 증명 모음으로 키를 가져옵니다. "사용" 필드가 제거 되었습니다.
- 키 복원-"사용" 필드가 제거 되었습니다.     
- RSA 알고리즘에 대해 "RSA_OAEP"을 "OAEP"로 변경 했습니다. [키, 암호 및 인증서 정보](about-keys-secrets-certificates.md)를 참조 하세요.    
 
두 번째 미리 보기 버전 (버전 2015-02-01-미리 보기)은 2015 년 4 월 20 일에 발표 되었습니다. 자세한 내용은 [REST API 업데이트](https://docs.microsoft.com/archive/blogs/kv/rest-api-update)를 참조하세요. 다음 작업을 업데이트했습니다.
 
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
 
첫 번째 미리 보기 버전 (버전 2014-12-08-미리 보기)은 2015 년 1 월 8 일에 발표 되었습니다.  
 
## <a name="next-steps"></a>다음 단계

- [키, 비밀 및 인증서에 대한 정보](about-keys-secrets-certificates.md)
- [키](../keys/index.yml)
- [비밀](../secrets/index.yml)
- [인증서](../certificates/index.yml)
