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
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: barclayn
ms.openlocfilehash: 56a1ebcfbb6dda9bc96aa241bd2b8d753022181a
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385861"
---
# <a name="what-is-azure-key-vault"></a>Azure Key Vault란?

Azure Key Vault는 다음 문제를 해결하는 데 도움이 됩니다.
- **비밀 관리** - Azure Key Vault를 사용하여 토큰, 암호, 인증서, API 키 및 기타 비밀에 대한 액세스를 안전하게 저장하고 엄격히 제어할 수 있습니다.
- **키 관리** - Azure Key Vault를 키 관리 솔루션으로 사용할 수도 있습니다. Azure Key Vault를 사용하면 데이터를 암호화하는 데 사용되는 암호화 키를 쉽게 만들고 제어할 수 있습니다. 
- **인증서 관리** - Azure Key Vault는 Azure 및 내부 연결 리소스와 함께 사용할 공용 및 개인 SSL/TLS(Secure Sockets Layer/Transport Layer Security) 인증서를 쉽게 프로비전, 관리 및 배포할 수 있는 서비스이기도 합니다. 
- **하드웨어 보안 모듈로 지원되는 비밀 저장** - 비밀과 키는 HSM의 유효성을 검사하는 소프트웨어 또는 FIPS 140-2 수준 2를 통해 보호할 수 있습니다

## <a name="basic-concepts"></a>기본 개념

Azure Key Vault는 비밀을 안전하게 저장하고 액세스하기 위한 도구입니다. 비밀은 API 키, 암호 또는 인증서 등에 대한 액세스를 엄격하게 제어하려는 항목입니다. **Vault**는 비밀의 논리적 그룹입니다. Key Vault로 작업을 수행하려면 우선 인증을 받아야 합니다. 

근본적으로 Key Vault에 인증할 수 있는 방법을 3가지입니다.

1. **[Azure 리소스의 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)** 사용(**권장 및 모범 사례**): Azure에서 Virtual Machine에 앱을 배포하는 경우, Key Vault에 액세스할 수 있는 Virtual Machine에 ID를 할당할 수 있습니다. [여기](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에 나열된 다른 Azure 리소스에 ID를 할당할 수도 있습니다. 이런 방식의 이점은 앱/서비스에서 첫 번째 비밀의 회전을 관리하지 않는다는 점입니다. ID가 Azure에서 자동으로 회전됩니다. 
2. **서비스 사용자 및 인증서 사용:** 2번째 옵션은 Key Vault에 액세스할 수 있는 서비스 사용자 및 연결된 인증서를 사용하는 것입니다. 인증서를 회전하는 부담이 응용 프로그램 소유자나 개발자에게 가기 때문에 권장되지 않습니다.
3. **서비스 사용자 및 비밀 사용:** 3번째 옵션(비선호 옵션)은 서비스 사용자와 비밀을 사용하여 Key Vault에 인증하는 것입니다.

일부 주요 용어는 다음과 같습니다.
- **테넌트** - Microsoft 클라우드 서비스의 특정 인스턴스를 소유하고 관리하는 조직입니다. 조직에 대한 Azure 및 Office 365 서비스의 집합을 참조하는 정확한 방식으로 가장 자주 사용됩니다.
- **자격 증명 모음 소유자** - 키 자격 증명 모음을 만들고 전체 자격 증명 모음을 액세스하고 제어할 수 있습니다. 자격 증명 모음 소유자는 비밀과 키에 액세스하는 사용자를 기록하도록 감사를 설정할 수도 있습니다. 관리자는 키 수명 주기를 제어할 수 있습니다. 새 버전의 키로 롤링하고, 백업하고, 관련 작업을 수행할 수 있습니다.
- **자격 증명 모음 소비자**: 자격 증명 모음 소유자가 소비자 액세스 권한을 부여하면 이 소비자가 키 자격 증명 모음 내의 자산에 대한 작업을 수행할 수 있습니다. 사용 가능한 작업은 부여된 사용 권한에 따라 달라집니다.
- **리소스** - Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 몇 가지 일반적인 리소스는 가상 머신, 저장소 계정, 웹앱, 데이터베이스 및 가상 네트워크이지만 더 많은 종류가 있습니다.
- **리소스 그룹**: Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다.
- **서비스 사용자** - Azure AD 테넌트에 의해 보안이 유지되는 리소스에 액세스하려면 액세스가 필요한 엔터티를 보안 주체를 통해 나타내야 합니다. 사용자(사용자 주체) 및 응용 프로그램(서비스 주체) 둘 다 마찬가지입니다. 보안 주체는 해당 테넌트의 사용자/응용 프로그램에 대한 액세스 정책 및 권한을 정의합니다. 이를 통해 로그인 동안의 사용자/응용 프로그램의 인증 및 리소스 액세스 동안의 권한 부여 같은 핵심 기능이 허용됩니다.
- **[Azure AD(Azure Active Directory)](../active-directory/active-directory-whatis.md)**: 테넌트용 Active Directory 서비스입니다. 각 디렉터리에는 하나 이상의 도메인이 있습니다. 디렉터리는 연결된 여러 구독을 가질 수 있지만 오직 하나의 테넌트만을 가질 수 있습니다. 
- **Azure 테넌트 ID**: Azure 구독 내에서 Azure AD 인스턴스를 식별하는 고유한 방법입니다.
- **Azure 리소스에 대한 관리 ID**: Azure Key Vault는 자격 증명과 기타 키 및 비밀을 안전하게 저장하는 방법을 제공하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증되어야 합니다. 관리 ID를 사용하면 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 Key Vault 또는 모든 서비스에 인증할 수 있으므로 코드에 어떤 자격 증명도 포함할 필요가 없습니다. 자세한 내용은 아래 이미지와 [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

    ![Azure 리소스에 대한 관리 ID가 작동하는 방식에 대한 다이어그램](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Key Vault 역할

다음 표에서 키 자격 증명 모음이 개발자와 보안 관리자의 요구를 충족하도록 도와주는 방법을 더 잘 이해할 수 있습니다.

| 역할 | 문제 설명 | Azure Key Vault로 해결됨 |
| --- | --- | --- |
| Azure 응용 프로그램용 개발자 |"서명 및 암호화를 위한 키를 사용하는 Azure용 응용 프로그램을 작성하려는 경우 솔루션이 지리적으로 분산되는 응용 프로그램에 적합하도록 이러한 키를 내 응용 프로그램의 외부에 두려고 합니다. <br/><br/>직접 코드를 작성하지 않고도 이러한 키와 비밀을 보호하려고 합니다. 또한 이러한 키와 암호를 내 응용 프로그램에서 최적의 성능으로 사용할 수 있게 하려고 합니다." |√ 키를 자격 증명 모음에 저장하고 필요할 때 URI로 호출합니다.<br/><br/> √ Azure에서는 업계 표준 알고리즘, 키 길이 및 하드웨어 보안 모듈을 사용하여 키를 보호합니다.<br/><br/> √ 응용 프로그램과 동일한 Azure 데이터 센터에 상주하는 HSM에서 키를 처리합니다. 이 방법은 온-프레미스와 같이 별도의 위치에 있는 키보다 안정성이 뛰어나 대기 시간이 줄어 듭니다. |
| SaaS(Software as a Service) 개발자 |"내 고객의 테넌트 키와 암호에 대한 책임 또는 잠재적인 법적 책임을 원하지 않습니다. <br/><br/>핵심 소프트웨어 기능을 제공하는 일에 최선을 다해 집중할 수 있도록 고객이 키를 소유하고 관리하기를 원합니다." |√ 고객은 Azure에서 고유한 키를 가져오고 관리할 수 있습니다. SaaS 응용 프로그램이 해당 고객의 키를 사용하여 암호화 작업을 수행해야 하는 경우, Key Vault는 응용 프로그램을 대신하여 이러한 작업을 수행합니다. 응용 프로그램에서 고객의 키를 표시하지 않습니다. |
| 수석 보안 책임자(CSO) |"보안 키 관리를 위해 응용 프로그램이 FIPS 140-2 Level 2 HSM을 준수하는지 알고 싶습니다. <br/><br/>내 조직이 키 수명 주기를 제어하고 키 사용을 모니터링할 수 있는지 확인하고 싶습니다. <br/><br/>여러 Azure 서비스 및 리소스를 사용하더라도, Azure의 단일 위치에서 키를 관리 하고 싶습니다." |√ HSM은 FIPS 140-2 Level 2 검증되었습니다.<br/><br/>√ Key Vault는 Microsoft에서 키를 보거나 추출할 수 없게 설계되어 있습니다.<br/><br/>√ 키 사용은 거의 실시간으로 기록됩니다.<br/><br/>√ Azure에 얼마나 많은 자격 증명 모음이 있는지, 지원되는 영역 및 사용하는 응용 프로그램과 관계 없이 자격 증명 모음은 단일 인터페이스를 제공합니다. |

Azure를 구독하는 사용자는 주요 자격 증명 모음을 만들고 사용할 수 있습니다. 키 자격 증명 모음에는 개발자와 보안 관리자의 혜택이 있지만, 조직에 대한 다른 Azure 서비스를 관리하는 조직의 관리자가 구현하고 관리할 수 있습니다. 예를 들어 관리자는 Azure 구독을 사용하여 로그인하고, 키를 저장할 조직에 대한 자격 증명 모음을 만든 후, 다음과 같은 운영 태스크를 담당할 수 있습니다.

* 키 또는 비밀 만들기 또는 가져오기
* 키 또는 비밀 취소 또는 삭제
* 사용자 또는 응용 프로그램이 키 자격 증명 모음에 액세스하도록 권한을 부여하므로 해당 키 및 암호를 관리하거나 사용할 수 있습니다.
* 구성 키 용도(예: 서명 또는 암호화)
* 키 사용량 모니터링

그런 다음 이 관리자는 개발자에게 URI를 제공하여 자신의 응용 프로그램에서 호출 하고 보안 관리자에게 해당 키 사용 현황 로깅 정보를 제공합니다. 

![Azure Key Vault 개요][1]

개발자는 API를 사용하여 직접 키를 관리할 수도 있습니다. 자세한 내용은 [키 자격 증명 모음 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

관리자를 위한 시작 자습서는 [Azure Key Vault 시작](key-vault-get-started.md)을 참조하세요.

키 자격 증명 모음에 사용 현황 로깅에 대한 자세한 내용은 [Azure Key Vault 로깅](key-vault-logging.md)을 참조하세요.

Azure Key Vault에서 키와 비밀을 사용하는 방법에 대한 자세한 내용은 [키, 비밀 및 인증서 정보](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx)를 참조하세요.

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault는 대부분 지역에서 사용할 수 있습니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.
