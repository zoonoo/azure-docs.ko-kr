---
title: 인증서 갱신 Azure Key Vault 정보
description: 인증서 갱신 Azure Key Vault 정보
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: beea6f7472d6bc871b4a2df323445b05671a621f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224563"
---
# <a name="about-azure-key-vault-certificate-renewal"></a>인증서 갱신 Azure Key Vault 정보

Azure Key Vault를 사용하여 네트워크에 대한 디지털 인증서를 쉽게 프로비저닝, 관리 및 배포하고 애플리케이션에 대한 보안 통신을 사용하도록 설정할 수 있습니다. 인증서에 대 한 일반적인 정보는 [Azure Key Vault 인증서](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates) 를 참조 하세요.

수명이 짧은 인증서를 보유 하거나 인증서 회전 빈도를 높이면 악의적 사용자의 손상 범위를 제한 합니다.

## <a name="certificate-expiration-notifications"></a>인증서 만료 알림
먼저 인증서가 만료 될 때 알리도록 인증서 연락처를 Key Vault에 추가 해야 합니다 (예: [AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)) Second를 사용 하 여 인증서 만료에 대 한 통지를 받을 시기를 구성 합니다. 수명 시간 작업을 구성 하려면 [여기](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate) 를 따르세요.

키 자격 증명 모음에는 세 가지 범주의 인증서를 만듭니다. 이 가이드는 인증서 갱신을 달성할 수 있는 방법을 이해 하는 데 도움이 됩니다.
-   통합 CA (DigiCert 또는 GlobalSign)를 사용 하 여 만든 인증서
-   통합 되지 않은 CA를 사용 하 여 만든 인증서
-   자체 서명된 인증서

## <a name="renewal-of-integrated-ca-certificate"></a>통합 CA 인증서 갱신 
기쁘게도 Azure 키 자격 증명 모음은 Microsoft 신뢰할 수 있는 Ca (DigiCert 및 GlobalSign)에서 발급 한 인증서의 종단 간 유지 관리를 담당 합니다. [키 자격 증명 모음을 사용 하 여 신뢰할 수 있는 CA를 통합](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)하는 방법을 알아봅니다.

## <a name="renewal-of-non-integrated-ca-certificate"></a>통합 되지 않은 CA 인증서 갱신 
Azure 키 자격 증명 모음을 통해 사용자는 모든 CA에서 인증서를 가져와서 사용자가 여러 Azure 리소스와 통합 하 고 배포를 쉽게 만들 수 있습니다. 인증서 추적이 만료 되었거나 인증서가 이미 만료 된 것을 발견 한 경우 그런 다음 Key Vault 최신 상태를 유지 하는 데 도움이 될 수 있습니다. 통합 되지 않은 CA 인증서의 경우 key vault를 통해 사용자는 거의 만료 된 전자 메일 알림을 설정할 수 있습니다. 이러한 알림은 여러 사용자에 대해서도 설정할 수 있습니다.

이제 인증서를 갱신 하려면 Azure Key Vault 인증서가 버전이 지정 된 개체 인지 이해 하는 것이 중요 합니다. 현재 버전이 만료 되는 경우 새 버전을 만들어야 합니다. 개념적으로, 각각의 새 버전에는 키로 구성 된 새 인증서와 해당 키를 id에 연결 하는 blob이 모두 있습니다. 파트너 관계에 있지 않은 CA를 사용 하는 경우 key vault는 키 값 쌍을 생성 하 고 CSR을 반환 합니다.

**Azure Portal에서 수행 하는 단계는 다음과 같습니다.**
1.  갱신 하려는 인증서를 엽니다.
2.  인증서 화면에서 **+ 새 버전** 단추를 선택 합니다.
3.  **인증서 작업** 선택
4.  **CSR 다운로드**를 선택 합니다. 그러면 로컬 드라이브에 csr 파일이 다운로드 됩니다.
5.  CSR을 선택 하 여 요청에 서명 합니다.
6.  서명 된 요청을 다시 가져오고 동일한 인증서 작업 화면에서 **CSR 병합** 을 선택 합니다.

> [!NOTE]
> 서명 된 CSR을 만든 것과 동일한 CSR 요청을 병합 하는 것이 중요 합니다. 그렇지 않으면 키가 일치 하지 않습니다.

단계는 새 인증서를 만드는 방법과 비슷하며 [여기]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)에 자세히 설명 되어 있습니다.

## <a name="renewal-of-self-signed-certificate"></a>자체 서명 된 인증서 갱신

좋은 소식입니다. Azure 키 자격 증명 모음은 사용자에 대 한 자체 서명 된 인증서의 자동 갱신도 처리 합니다. 발급 정책 변경 및 인증서의 수명 동작 특성 업데이트에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)를 참조 하세요.

### <a name="troubleshoot"></a>문제 해결
발급 된 인증서가 Azure Portal의 ' 사용 안 함 ' 상태 이면 인증서 작업 보기로 이동 하 여 해당 인증서에 대 한 오류 메시지를 확인 합니다.

### <a name="frequently-asked-questions"></a>질문과 대답
* 인증서의 autorotation 기능을 테스트 하려면 어떻게 해야 하나요?
  유효 기간이 1 월 인 인증서를 만든 다음 1%에 대 한 회전에 대 한 수명 시간 작업을 설정 합니다. 이 설정은 인증서를 7.2 시간 안에 회전 합니다.
  
* 인증서 자동 갱신 후 태그가 복제 되나요?
  아니요. 사용자가 수동으로 태그를 복사 하지 않는 한 태그는 복제 되지 않습니다.

### <a name="see-also"></a>참고 항목
*   [DigiCert 인증 기관과 Key Vault 통합](how-to-integrate-certificate-authority.md)
*   [자습서: Key Vault에서 인증서 자동 회전 구성](tutorial-rotate-certificates.md)
