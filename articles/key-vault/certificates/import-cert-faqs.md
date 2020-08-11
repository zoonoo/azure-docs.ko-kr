---
title: 질문과 대답-인증서 가져오기 Azure Key Vault
description: 질문과 대답-인증서 가져오기 Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 402672d8eeaae8a5097e2ab2905997eb1f646ad6
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056349"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>질문과 대답-인증서 가져오기 Azure Key Vault

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Azure Key Vault에서 인증서를 가져오려면 어떻게 해야 하나요?

인증서 가져오기 – 가져오기 작업의 경우 Azure key vault는 두 가지 인증서 형식인 PEM 및 PFX를 수락 합니다. 공용 부분만 포함 된 PEM 파일이 있지만 Azure key vault에는 파일 폴더와 개인 키를 사용 하는 PEM 또는 PFX만 필요 합니다. [자습서에 따라 인증서 가져오기](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>암호로 보호 된 인증서를 주요 자격 증명 모음으로 가져온 다음 다운로드 하면 인증서와 연결 된 암호를 볼 수 없습니다.
    
키 자격 증명 모음에 저장 한 후 보호 된 인증서를 업로드 하면 해당 인증서와 연결 된 암호가 저장 되지 않습니다. 가져오기 작업 중에 한 번만 필요합니다. 이것은 디자인 개념인 경우에도 항상 비밀으로 인증서를 가져오고, Base64에서 PFX로 변환 하 [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)를 통해 이전 암호를 추가할 수 있습니다.

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>' 잘못 된 매개 변수 오류 '는 어떻게 해결할 수 있나요? Key Vault에서 가져오기에 대해 지원되는 인증서 형식은 무엇인가요?

인증서를 가져올 때 키가 파일 자체에 포함 되어 있는지 확인 해야 합니다. 개인 키가 다른 형식에 별도로 있는 경우 해당 키를 인증서와 결합 해야 합니다. 일부 인증 기관은 인증서를 다양 한 형식으로 제공 하므로 인증서를 가져오기 전에 해당 인증서가 pem 또는 .pfx 형식 이어야 하며 사용 된 키가 RSA 또는 ECC 인지 확인 합니다. [인증서 요구 사항](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) 및 [인증서 키 요구 사항](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)에 대 한 검토를 참조 하세요.

###  <a name="can-i-import-certificate-using-arm-template"></a>ARM 템플릿을 사용 하 여 인증서를 가져올 수 있나요?

아니요, ARM 템플릿을 사용 하 여 인증서 작업을 수행할 수 없습니다. 권장 해결 방법은 API 또는 CLI 또는 PowerShell에서 인증서 가져오기 방법을 사용 하는 것입니다. 기존의 인증서가 있는 경우 암호로 가져올 수 있습니다.

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>포털을 통해 인증서를 가져오는 동안 "오류가 발생했습니다"라는 메시지가 표시됩니다. 자세히 조사 하려면 어떻게 해야 하나요?
    
자세한 오류를 보려면 [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) 또는 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)을 통해 인증서 파일을 가져옵니다.

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>' 오류 유형: 액세스 거부 또는 사용자가 인증서를 가져올 수 없습니다. '를 어떻게 해결할 수 있나요?
    
이 작업을 수행하려면 인증서/가져오기 권한이 필요합니다. Key Vault가 있는 위치로 이동하여 액세스 정책에서 사용자에게 적절한 권한을 부여해야 합니다. 액세스 정책 추가 > Key Vault> 액세스 > 정책 추가로 이동 하 여 인증서 사용 권한 (또는 사용 권한 필요 >)을 선택 하 고 사용자의 전자 메일을 검색 한 다음 사용자의 전자 메일을 추가 합니다. [인증서 관련 액세스 정책에 대 한 자세한 정보 읽기](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>' 오류 유형: 인증서를 만들 때 충돌 '을 해결 하려면 어떻게 해야 하나요?
    
인증서 이름은 고유해야 합니다. Azure key vault에서 [인증서](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) 를 구성 하는 경우에도 동일한 이름의 인증서가 일시 삭제 된 상태에 있을 수 있습니다. 인증서에 지정 하려는 것과 동일한 이름으로 Key Vault에 다른 키 또는 암호가 있으면 실패 하 고 해당 키 또는 비밀을 제거 하거나 인증서에 다른 이름을 사용 해야 합니다. [삭제 된 인증서 보기](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>' 오류 유형: 문자 길이가 너무 깁니다. '가 표시 되는 이유는 무엇 인가요?
    
* 인증서 주체 이름의 길이는 문자 제한이 200 char입니다.
* 인증서 암호 길이는 문자 제한이 200 char입니다.

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Azure Key vault에서 만료 된 인증서를 가져올 수 있나요?
    
아니요, 만료 된 PFX 인증서를 Azure Key Vault으로 가져오지 않습니다.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>인증서를 적절 한 형식으로 변환 하려면 어떻게 해야 하나요?

인증 기관에 요청 하 여 필요한 형식으로 인증서를 제공할 수 있습니다. 또한 적절 한 형식으로 변환 하는 데 도움이 되는 타사 도구도 있습니다. 그러나 Microsoft는 원하는 형식으로 인증서를 가져오는 방법에 대 한 추가 정보를 제공할 수 없습니다.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>파트너가 아닌 Ca에서 인증서를 가져올 수 있나요?
예, 모든 CA에서 인증서를 가져올 수 있지만 key vault는 이러한 인증서를 자동으로 갱신할 수 없습니다. 인증서 만료에 대 한 알림을 받도록 전자 메일 알림을 설정할 수 있습니다.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>파트너 CA에서 인증서를 가져오는 경우 자동 갱신 기능이 계속 작동 하나요?
예, 업로드 한 후에는 인증서의 발급 정책에서 autorotation를 지정 해야 합니다. 또한 다음 주기 또는 인증서 버전까지 변경 내용이 반영 됩니다.

### <a name="unable-to-see-the-app-service-certificate-imported-to-key-vault"></a>Key Vault으로 가져온 App Service Certificate를 볼 수 없나요? 
인증서를 성공적으로 가져온 경우 비밀 블레이드에서을 검토 하세요.


## <a name="next-steps"></a>다음 단계

- [인증서 Azure Key Vault](/azure/key-vault/certificates/about-certificates)
