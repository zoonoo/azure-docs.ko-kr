---
title: Azure 키 볼트 인증서를 가진 TLS 종료
description: HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Azure 응용 프로그램 게이트웨이를 키 볼트와 통합하는 방법에 대해 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 26093d051da8f2182a40f80837acbd9ef7dd008f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312081"
---
# <a name="tls-termination-with-key-vault-certificates"></a>키 볼트 인증서를 가진 TLS 종료

[Azure Key Vault는](../key-vault/key-vault-overview.md) 보안 암호, 키 및 TLS/SSL 인증서를 보호하는 데 사용할 수 있는 플랫폼 관리 비밀 저장소입니다. Azure 응용 프로그램 게이트웨이는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 이 지원은 응용 프로그램 게이트웨이의 v2 SKU로 제한됩니다.

키 볼트 통합은 TLS 종단을 위한 두 가지 모델을 제공합니다.

- 수신기에 연결된 TLS/SSL 인증서를 명시적으로 제공할 수 있습니다. 이 모델은 TLS/SSL 인증서를 TLS 종료를 위한 응용 프로그램 게이트웨이에 전달하는 전통적인 방법입니다.
- HTTPS 지원 수신기를 만들 때 선택적으로 기존 Key Vault 인증서 또는 비밀에 대한 참조를 제공할 수 있습니다.

Key Vault와의 애플리케이션 게이트웨이 통합은 다음과 같은 많은 이점을 제공합니다.

- TLS/SSL 인증서는 응용 프로그램 개발 팀에서 직접 처리하지 않으므로 보안이 강화됩니다. 통합을 통해 별도의 보안 팀은 다음을 수행할 수 있습니다.
  * 응용 프로그램 게이트웨이를 설정합니다.
  * 애플리케이션 게이트웨이 수명 주기를 제어합니다.
  * 선택한 응용 프로그램 게이트웨이에 권한을 부여하여 키 자격 증명 모음에 저장된 인증서에 액세스합니다.
- 키 자격 증명 모음으로 기존 인증서가져오기 지원 또는 Key Vault API를 사용하여 신뢰할 수 있는 Key Vault 파트너와 함께 새 인증서를 만들고 관리할 수 있습니다.
- 키 자격 증명 모음에 저장된 인증서의 자동 갱신 지원

응용 프로그램 게이트웨이는 현재 소프트웨어 유효성이 검사된 인증서만 지원합니다. HSM(하드웨어 보안 모듈)의 유효성을 검사한 인증서는 지원되지 않습니다. 응용 프로그램 게이트웨이키 볼트 인증서를 사용하도록 구성되면 해당 인스턴스는 Key Vault에서 인증서를 검색하고 TLS 종료를 위해 로컬로 설치합니다. 또한 인스턴스는 Key Vault를 24시간 간격으로 폴링하여 인증서의 갱신된 버전을 검색합니다(있는 경우). 업데이트된 인증서가 발견되면 현재 HTTPS 수신기와 연결된 TLS/SSL 인증서가 자동으로 회전됩니다.

> [!NOTE]
> Azure 포털은 비밀이 아닌 KeyVault 인증서만 지원합니다. 응용 프로그램 게이트웨이는 여전히 KeyVault의 참조 비밀을 지원하지만 PowerShell, CLI, API, ARM 템플릿 등과 같은 포털이 아닌 리소스를 통해서만 지원됩니다. 

## <a name="how-integration-works"></a>통합의 작동 방식

키 볼트와 애플리케이션 게이트웨이 통합에는 3단계 구성 프로세스가 필요합니다.

1. **사용자 할당된 관리되는 ID 만들기**

   응용 프로그램 게이트웨이가 사용자를 대신하여 Key Vault에서 인증서를 검색하는 데 사용하는 기존 사용자 할당관리 ID를 만들거나 다시 사용합니다. 자세한 내용은 [Azure 리소스에 대한 관리ID란 무엇입니까?](../active-directory/managed-identities-azure-resources/overview.md) 이 단계는 Azure Active Directory 테넌트에서 새 ID를 만듭니다. ID를 만드는 데 사용되는 구독에서 ID를 신뢰합니다.

1. **키 자격 증명 모음 구성**

   그런 다음 기존 인증서를 가져오거나 키 자격 증명 모음에 새 인증서를 만듭니다. 인증서는 응용 프로그램 게이트웨이를 통해 실행되는 응용 프로그램에서 사용됩니다. 이 단계에서는 암호가 없는 기본 64인코딩 된 PFX 파일로 저장된 키 자격 증명 모음 암호를 사용할 수도 있습니다. 키 자격 증명 모음의 인증서 유형 개체에서 사용할 수 있는 자동 갱신 기능 때문에 인증서 형식을 사용하는 것이 좋습니다. 인증서 또는 암호를 만든 후 키 자격 증명 모음에서 액세스 정책을 정의하여 ID에 보안 정보에 *대한* 액세스 권한을 부여할 수 있도록 합니다.

1. **애플리케이션 게이트웨이 구성**

   앞의 두 단계를 완료한 후 기존 응용 프로그램 게이트웨이를 설정하거나 수정하여 사용자가 할당한 관리 ID를 사용할 수 있습니다. 또한 HTTP 수신기의 TLS/SSL 인증서를 구성하여 키 볼트 인증서 또는 비밀 ID의 전체 URI를 가리킬 수도 있습니다.

   ![키 볼트 인증서](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>다음 단계

[Azure PowerShell을 사용하여 키 볼트 인증서로 TLS 종료 구성](configure-keyvault-ps.md)
