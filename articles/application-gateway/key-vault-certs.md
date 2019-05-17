---
title: Azure Key Vault 인증서를 사용 하 여 SSL 종료
description: HTTPS 사용 수신기에 연결 된 서버 인증서에 대 한 Key Vault를 사용 하 여 Azure Application Gateway를 통합 하는 방법도 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 18af315c58c838a7237acfbcc32f622a0edbd3b3
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827636"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Key Vault 인증서를 사용 하 여 SSL 종료

[Azure Key Vault](../key-vault/key-vault-whatis.md) 는 비밀, 키 및 SSL 인증서를 보호 하는 데 사용할 수 있는 플랫폼에서 관리 되는 암호를 저장 합니다. Azure Application Gateway는 HTTPS 사용 수신기에 연결 된 서버 인증서 (공개 미리 보기)에서 Key Vault를 사용 하 여 통합을 지원 합니다. 이 지원은 Application Gateway의 v2 SKU로 제한 됩니다.

> [!IMPORTANT]
> Key Vault를 사용 하 여 Application Gateway 통합 현재 공개 미리 보기 중입니다. 이 미리 보기 서비스 수준 계약 (SLA) 없이 제공 되 고 프로덕션 워크 로드에 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 공개 미리 보기는 SSL 종료를 위한 두 가지 모델을 제공합니다.

- 수신기에 연결 된 SSL 인증서를 명시적으로 제공할 수 있습니다. 이 모델은 SSL 종료에 대 한 Application Gateway에 SSL 인증서를 전달 하는 일반적인 방법입니다.
- 필요에 따라 HTTPS 기반 수신기를 만들 때 기존 주요 자격 증명 모음 인증서 또는 암호에 대 한 참조를 제공할 수 있습니다.

Key Vault를 사용 하 여 application Gateway 통합 등 많은 이점을 제공 합니다.

- 더욱 강력한 보안을 SSL 인증서 응용 프로그램 개발 팀에서 직접 처리 되지 않습니다. 통합 하는 별도 보안 팀을 허용합니다.
  * 응용 프로그램 게이트웨이 설정 합니다.
  * 응용 프로그램 게이트웨이 수명 주기를 제어 합니다.
  * 선택한 응용 프로그램 게이트웨이에 인증서 키 자격 증명 모음에 저장 된 액세스 권한을 부여 합니다.
- Key vault에 기존 인증서 가져오기에 대 한 지원. 또는 키 자격 증명 모음 Api를 사용 하 여 만들고 신뢰할 수 있는 Key Vault 파트너 중 하나를 사용 하 여 새 인증서를 관리 합니다.
- Key vault에 저장 되는 인증서의 자동 갱신을 지원 합니다.

Application Gateway는 현재 소프트웨어 유효성 검사 된 인증서만을 지원합니다. 하드웨어 보안 모듈 (HSM)-유효성이 검사 된 인증서는 지원 되지 않습니다. Application Gateway는 Key Vault 인증서를 사용 하도록 구성 되 면 해당 인스턴스 Key Vault에서 인증서를 검색 및 SSL 종료에 대해 로컬로 설치 합니다. 인스턴스 또한 풀링 Key Vault 인증서의 갱신 된 버전을 검색 하려면 24 시간 간격 존재 하는 경우. 업데이트 된 인증서가 없으면 현재 HTTPS 수신기와 연결 된 SSL 인증서를 자동으로 회전 합니다.

## <a name="how-integration-works"></a>통합의 작동 방식

Key Vault를 사용 하 여 application Gateway 통합에는 세 단계로 구성 프로세스를 필요합니다.

1. **사용자 할당 관리 되는 id 만들기**

   만들거나는 기존 사용자 할당 관리 서비스 id 사용자를 대신해 Key Vault에서 인증서를 검색 하는 응용 프로그램 게이트웨이 다시 사용 합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요. 이 단계에서는 Azure Active Directory 테 넌 트에서 새 id를 만듭니다. Id는 id를 만드는 데 사용 되는 구독에서 신뢰할 수 있습니다.

1. **키 자격 증명 모음 구성**

   그런 다음 기존 인증서를 가져오세요 하거나 키 자격 증명 모음에서 새로 만듭니다. 인증서는 응용 프로그램 게이트웨이 통해 실행 되는 응용 프로그램에서 사용 됩니다. 이 단계에서는 암호를 사용 하지 않는, 기본 64로 인코딩된 PFX 파일로 저장 되는 key vault 비밀을 사용할 수도 있습니다. Key vault에 인증서 형식 개체를 사용 하 여 사용할 수 있는 자동 갱신 기능으로 인해 인증서 형식을 사용 하는 것이 좋습니다. Id를 부여할 수 있도록 키 자격 증명 모음의 액세스 정책을 정의 하는 인증서 또는 암호를 만든 후 *가져올* 비밀에 대 한 액세스.

1. **Application gateway 구성**

   앞의 두 단계를 완료 하면 설정 하거나 사용자 할당 관리 되는 id를 사용 하도록 기존 application gateway를 수정할 수 있습니다. Key Vault 인증서 또는 암호 ID의 전체 URI를 가리키도록 HTTP 수신기의 SSL 인증서를 구성할 수도 있습니다.

   ![키 자격 증명 모음 인증서](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>다음 단계

[Azure PowerShell을 사용 하 여 Key Vault 인증서를 사용 하 여 SSL 종료를 구성](configure-keyvault-ps.md)
