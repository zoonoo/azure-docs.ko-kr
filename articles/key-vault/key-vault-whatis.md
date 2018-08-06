---
title: Azure Key Vault란? | Microsoft Docs
description: 'Azure Key Vault는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Azure Key Vault를 사용하여, 고객은 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키, PFX 파일 및 암호)을 암호화하여 하드웨어 보안 모듈(HSM)로 보호된 키를 사용합니다.'
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/26/2018
ms.author: barclayn
ms.openlocfilehash: cd9dd4d400177e511f23c2677b77198a50420a35
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282999"
---
# <a name="what-is-azure-key-vault"></a>Azure Key Vault란?

Azure Key Vault는 클라우드 응용 프로그램 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Key Vault를 사용하여, 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키, PFX 파일 및 암호)을 암호화에 하드웨어 보안 모듈(HSM)로 보호된 키를 사용합니다. 추가된 보증을 위해, HSM에서 키를 생성하거나 가져올 수 있습니다. 이 작업을 수행하려면 Microsoft는 FIPS 140-2 Level 2 유효성 검사가 적용된 HSM(하드웨어 및 펌웨어)에서 키를 처리합니다.  

키 자격 증명 모음은 키 관리 프로세스를 간소화하고 데이터를 액세스하고 암호화 하는 키의 제어를 유지할 수 있습니다. 개발자는 개발 및 테스트(분)을 위한 키를 만든 다음, 프로덕션 키로 원활하게 마이그레이션할 수 있습니다. 보안 관리자는 필요한 경우 권한을 키로 부여(및 해지)할 수 있습니다.

## <a name="basic-concepts"></a>기본 개념

Azure Key Vault는 비밀을 안전하게 저장하고 액세스하기 위한 도구입니다. 비밀은 API 키, 암호 또는 인증서 등에 대한 액세스를 엄격하게 제어하려는 항목입니다.
일부 키 용어는 다음과 같습니다.
- **테넌트** - 테넌트는 Microsoft 클라우드 서비스의 특정 인스턴스를 소유 및 관리하는 조직입니다. 조직에 대한 Azure 및 Office 365 서비스의 집합을 참조하는 정확한 방식으로 가장 자주 사용됩니다.
- **자격 증명 모음 소유자** - 전체 액세스를 가져오는 Key Vault를 만들고 제어할 수 있습니다. 자격 증명 모음 소유자는 비밀 및 키에 액세스하는 사용자를 기록하도록 감사를 설정할 수도 있습니다. 관리자는 키 수명 주기를 제어할 수 있습니다. 키의 새 버전으로 롤, 백업 등을 수행할 수 있습니다.
- **자격 증명 모음 소비자** - 자격 증명 모음 소유자가 부여되는 권한에 따른 액세스를 부여할 때 Key Vault 내의 자산에서 작업을 수행할 수 있습니다.
- **[Azure Active Directory](../active-directory/active-directory-whatis.md)** 는 지정된 테넌트에 대한 Azure AD 서비스입니다. 각 디렉터리에는 하나 이상의 도메인이 있습니다. 디렉터리는 연결된 여러 구독을 가질 수 있지만 오직 하나의 테넌트만을 가질 수 있습니다. 
- **Azure 테넌트 ID** - Azure 구독 내에서 Azure Active Directory를 식별하는 고유한 방법입니다. 
- **관리 서비스 ID** - Azure Key Vault는 자격 증명과 기타 키 및 비밀을 안전하게 저장하는 방법을 제공하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. MSI(관리 서비스 ID)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 Key Vault 또는 모든 서비스에 인증할 수 있으므로 코드에 어떤 자격 증명도 포함할 필요가 없습니다. MSI에 대해 자세히 알아보려면 [여기](../active-directory/managed-service-identity/overview.md)를 참조하세요.

    ![MSI 그래픽](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>주요 자격 증명 모음 역할

다음 표에서 키 자격 증명 모음이 개발자와 보안 관리자의 요구를 충족하도록 도와주는 방법을 더 잘 이해할 수 있습니다.

| 역할 | 문제 설명 | Azure Key Vault로 해결됨 |
| --- | --- | --- |
| Azure 응용 프로그램용 개발자 |"서명 및 암호화를 위한 키를 사용하는 Azure용 응용 프로그램을 작성하려는 경우 솔루션이 지리적으로 분산되는 응용 프로그램에 적합하도록 이러한 키를 내 응용 프로그램의 외부에 두려고 합니다. <br/><br/>또한 직접 코드를 작성하지 않고도 이러한 키와 암호를 보호하려고 합니다. 또한 이러한 키와 암호를 내 응용 프로그램에서 최적의 성능으로 사용할 수 있게 하려고 합니다." |√ 키를 자격 증명 모음에 저장하고 필요할 때 URI로 호출합니다.<br/><br/> √ Azure에서는 업계 표준 알고리즘, 키 길이 및 HSM(하드웨어 보안 모듈)을 사용하여 키를 보호합니다.<br/><br/> √ 응용 프로그램과 동일한 Azure 데이터 센터에 상주하는 HSM에서 키를 처리합니다. 이를 통해 키가 온-프레미스와 같은 별도 위치에 있는 경우 안정성을 향상시키고 대기 시간을 단축합니다. |
| SaaS(Software as a Service)에 대한 개발자  |"내 고객의 테넌트 키와 암호에 대한 책임 또는 잠재적인 법적 책임을 원하지 않습니다. <br/><br/>핵심 소프트웨어 기능을 제공하는 일에 최선을 다해 집중할 수 있도록 고객이 키를 소유하고 관리하기를 원합니다." |√ 고객은 Azure에서 고유한 키를 가져오고 관리할 수 있습니다. SaaS 응용 프로그램이 해당 고객의 키를 사용하여 암호화 작업을 수행해야 하는 경우, Key Vault는 응용 프로그램을 대신하여 이러한 작업을 수행합니다. 응용 프로그램에서 고객의 키를 표시하지 않습니다. |
| 수석 보안 책임자(CSO) |"보안 키 관리를 위해 응용 프로그램이 FIPS 140-2 Level 2 HSM을 준수하는지 알고 싶습니다. <br/><br/>내 조직이 키 수명 주기를 제어하고 키 사용을 모니터링할 수 있는지 확인하고 싶습니다. <br/><br/>여러 Azure 서비스 및 리소스를 사용하더라도, Azure의 단일 위치에서 키를 관리 하고 싶습니다." |√ HSM은 FIPS 140-2 Level 2 검증되었습니다.<br/><br/>√ Key Vault는 Microsoft에서 키를 보거나 추출할 수 없게 설계되어 있습니다.<br/><br/>√ 키 사용을 거의 실시간으로 로깅합니다.<br/><br/>√ Azure에 얼마나 많은 자격 증명 모음이 있는지, 지원되는 영역 및 사용하는 응용 프로그램과 관계 없이 자격 증명 모음은 단일 인터페이스를 제공합니다. |

Azure를 구독하는 사용자는 주요 자격 증명 모음을 만들고 사용할 수 있습니다. 키 자격 증명 모음에는 개발자와 보안 관리자의 혜택이 있지만, 조직에 대한 다른 Azure 서비스를 관리하는 조직의 관리자가 구현하고 관리할 수 있습니다. 예를 들어, 관리자는 Azure 구독을 사용하여 로그인하고, 키를 저장할 조직에 대한 자격 증명 모음을 만든 후 다음과 같은 운영 태스크를 담당할 수 있습니다.

* 키 또는 비밀 만들기 또는 가져오기
* 키 또는 비밀 취소 또는 삭제
* 사용자 또는 응용 프로그램이 키 자격 증명 모음에 액세스하도록 권한을 부여하므로 해당 키 및 암호를 관리하거나 사용할 수 있습니다.
* 구성 키 용도(예: 서명 또는 암호화)
* 키 사용량 모니터링

그런 다음 이 관리자는 개발자에게 URI를 제공하여 자신의 응용 프로그램에서 호출 하고 보안 관리자에게 해당 키 사용 현황 로깅 정보를 제공합니다. 

   ![Azure Key Vault 개요][1]

개발자는 API를 사용하여 직접 키를 관리할 수도 있습니다. 자세한 내용은 [키 자격 증명 모음 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

관리자에 대한 시작 자습서의 경우 [Azure Key Vault 시작](key-vault-get-started.md)을 참조하세요.

키 자격 증명 모음에 사용 현황 로깅에 대한 자세한 내용은 [Azure Key Vault 로깅](key-vault-logging.md)을 참조하세요.

Azure Key Vault로 키 및 암호를 사용하는 방법에 대한 자세한 내용은 [키, 암호 및 인증서 정보](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault는 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.
