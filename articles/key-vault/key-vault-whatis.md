---
title: Azure Key Vault란? - Azure Key Vault | Microsoft Docs
description: Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호합니다. 고객은 HSM(하드웨어 보안 모듈)을 통해 보호되는 키를 사용하여 인증 키, 저장소 계정 키, 데이터 암호화 키, .PFX 파일 및 암호를 암호화할 수 있습니다.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: f8826f0c0cb63068313a570b050531511126dbc9
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54422926"
---
# <a name="what-is-azure-key-vault"></a>Azure Key Vault란?

Azure Key Vault는 다음 문제를 해결하는 데 도움이 됩니다.

- **비밀 관리** - Azure Key Vault를 사용하여 토큰, 암호, 인증서, API 키 및 기타 비밀에 대한 액세스를 안전하게 저장하고 엄격히 제어할 수 있습니다.
- **키 관리** - Azure Key Vault를 키 관리 솔루션으로 사용할 수도 있습니다. Azure Key Vault를 사용하면 데이터를 암호화하는 데 사용되는 암호화 키를 쉽게 만들고 제어할 수 있습니다. 
- **인증서 관리** - Azure Key Vault는 Azure 및 내부 연결 리소스와 함께 사용할 공용 및 개인 SSL/TLS(Secure Sockets Layer/Transport Layer Security) 인증서를 쉽게 프로비전, 관리 및 배포할 수 있는 서비스이기도 합니다. 
- **하드웨어 보안 모듈로 지원되는 비밀 저장** - 비밀과 키는 HSM의 유효성을 검사하는 소프트웨어 또는 FIPS 140-2 수준 2를 통해 보호할 수 있습니다.

## <a name="basic-concepts"></a>기본 개념

Azure Key Vault는 비밀을 안전하게 저장하고 액세스하기 위한 도구입니다. 비밀은 API 키, 암호 또는 인증서 등에 대한 액세스를 엄격하게 제어하려는 항목입니다. **Vault**는 비밀의 논리적 그룹입니다. Key Vault로 작업을 수행하려면 우선 인증을 받아야 합니다. 

기본적으로 Key Vault에 인증하는 방법에는 다음 세 가지가 있습니다.

1. **[Azure 리소스에 대한 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)** 사용(**권장 및 모범 사례**): Azure에서 Virtual Machine에 앱을 배포하는 경우 Key Vault에 액세스할 수 있는 Virtual Machine에 ID를 할당할 수 있습니다. [여기](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에 나열된 다른 Azure 리소스에 ID를 할당할 수도 있습니다. 이런 방식의 이점은 앱/서비스에서 첫 번째 비밀의 회전을 관리하지 않는다는 점입니다. ID가 Azure에서 자동으로 회전됩니다. 
2. **서비스 사용자 및 인증서 사용:** 두 번째 옵션은 Key Vault에 액세스할 수 있는 서비스 사용자 및 연결된 인증서를 사용하는 것입니다. 인증서를 회전하는 부담이 애플리케이션 소유자나 개발자에게 가기 때문에 권장되지 않습니다.
3. **서비스 사용자 및 비밀 사용:** 세 번째 옵션(비선호 옵션)은 서비스 주체와 비밀을 사용하여 Key Vault에 인증하는 것입니다.

> [!NOTE]
> 위의 세 번째 옵션은 Key Vault에 인증하는 데 사용되는 부트스트랩 비밀을 자동으로 회전하기 어렵기 때문에 사용하면 안 됩니다.

일부 주요 용어는 다음과 같습니다.

- **테넌트**: 테넌트는 Microsoft 클라우드 서비스의 특정 인스턴스를 소유하고 관리하는 조직입니다. 조직에 대한 Azure 및 Office 365 서비스의 집합을 참조하는 정확한 방식으로 가장 자주 사용됩니다.
- **자격 증명 모음 소유자**: 자격 증명 모음 소유자는 키 자격 증명 모음을 만들고 전체 자격 증명 모음을 액세스하고 제어할 수 있습니다. 자격 증명 모음 소유자는 비밀과 키에 액세스하는 사용자를 기록하도록 감사를 설정할 수도 있습니다. 관리자는 키 수명 주기를 제어할 수 있습니다. 새 버전의 키로 롤링하고, 백업하고, 관련 작업을 수행할 수 있습니다.
- **자격 증명 모음 소비자**: 자격 증명 모음 소유자가 소비자 액세스 권한을 부여하면 자격 증명 모음 소비자가 키 자격 증명 모음 내의 자산에 대한 작업을 수행할 수 있습니다. 사용 가능한 작업은 부여된 사용 권한에 따라 달라집니다.
- **리소스**: 리소스는 Azure를 통해 사용할 수 있는 관리 가능한 항목입니다. 몇 가지 일반적인 리소스는 가상 머신, 저장소 계정, 웹앱, 데이터베이스 및 가상 네트워크이지만 더 많은 종류가 있습니다.
- **리소스 그룹**: 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다.
- **서비스 주체** - Azure 서비스 주체는 특정 Azure 리소스에 액세스하기 위해 사용자가 만든 앱, 서비스 및 자동화 도구에서 사용하는 보안 ID입니다. 특정한 역할이 있는 '사용자 ID'(사용자 이름과 암호 또는 인증서)이며 엄격하게 통제된 권한을 갖습니다. 일반 사용자 ID와 달리 서비스 주체는 특정 작업만 수행하면 됩니다. 해당 관리 작업을 수행하는 데 필요한 최소 사용 권한 수준을 부여하면 보안이 향상됩니다.
- **[Azure AD(Azure Active Directory)](../active-directory/active-directory-whatis.md)**: Azure AD는 테넌트에 대한 Active Directory 서비스입니다. 각 디렉터리에는 하나 이상의 도메인이 있습니다. 디렉터리는 연결된 여러 구독을 가질 수 있지만 오직 하나의 테넌트만을 가질 수 있습니다. 
- **Azure 테넌트 ID**: 테넌트 ID는 Azure 구독 내에서 Azure AD 인스턴스를 식별하는 고유한 방법입니다.
- **Azure 리소스에 대한 관리 ID**: Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. 관리 ID를 사용하면 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 Key Vault 또는 모든 서비스에 인증할 수 있으므로 코드에 어떤 자격 증명도 포함할 필요가 없습니다. 자세한 내용은 아래 이미지와 [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

    ![Azure 리소스에 대한 관리 ID가 작동하는 방식에 대한 다이어그램](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Key Vault 역할

다음 표에서 키 자격 증명 모음이 개발자와 보안 관리자의 요구를 충족하도록 도와주는 방법을 더 잘 이해할 수 있습니다.

| 역할 | 문제 설명 | Azure Key Vault로 해결됨 |
| --- | --- | --- |
| Azure 애플리케이션용 개발자 |"서명 및 암호화를 위한 키를 사용하는 Azure용 애플리케이션을 작성하려는 경우 솔루션이 지리적으로 분산되는 애플리케이션에 적합하도록 이러한 키를 내 애플리케이션의 외부에 두려고 합니다. <br/><br/>직접 코드를 작성하지 않고도 이러한 키와 비밀을 보호하려고 합니다. 또한 이러한 키와 비밀을 내 애플리케이션에서 최적의 성능으로 사용할 수 있게 하려고 합니다." |√ 키를 자격 증명 모음에 저장하고 필요할 때 URI로 호출합니다.<br/><br/> √ Azure에서는 업계 표준 알고리즘, 키 길이 및 하드웨어 보안 모듈을 사용하여 키를 보호합니다.<br/><br/> √ 애플리케이션과 동일한 Azure 데이터 센터에 상주하는 HSM에서 키를 처리합니다. 이 방법은 온-프레미스와 같이 별도의 위치에 있는 키보다 안정성이 뛰어나 대기 시간이 줄어 듭니다. |
| SaaS(Software as a Service) 개발자 |"내 고객의 테넌트 키와 암호에 대한 책임 또는 잠재적인 법적 책임을 원하지 않습니다. <br/><br/>핵심 소프트웨어 기능을 제공하는 일에 최선을 다해 집중할 수 있도록 고객이 키를 소유하고 관리하기를 원합니다." |√ 고객은 Azure에서 고유한 키를 가져오고 관리할 수 있습니다. SaaS 애플리케이션이 해당 고객의 키를 사용하여 암호화 작업을 수행해야 하는 경우, Key Vault는 애플리케이션을 대신하여 이러한 작업을 수행합니다. 애플리케이션에서 고객의 키를 표시하지 않습니다. |
| 수석 보안 책임자(CSO) |"보안 키 관리를 위해 애플리케이션이 FIPS 140-2 Level 2 HSM을 준수하는지 알고 싶습니다. <br/><br/>내 조직이 키 수명 주기를 제어하고 키 사용을 모니터링할 수 있는지 확인하고 싶습니다. <br/><br/>여러 Azure 서비스 및 리소스를 사용하더라도, Azure의 단일 위치에서 키를 관리 하고 싶습니다." |√ HSM은 FIPS 140-2 Level 2 검증되었습니다.<br/><br/>√ Key Vault는 Microsoft에서 키를 보거나 추출할 수 없게 설계되어 있습니다.<br/><br/>√ 키 사용은 거의 실시간으로 기록됩니다.<br/><br/>√ Azure에 얼마나 많은 자격 증명 모음이 있는지, 지원되는 영역 및 사용하는 애플리케이션과 관계 없이 자격 증명 모음은 단일 인터페이스를 제공합니다. |

Azure를 구독하는 사용자는 주요 자격 증명 모음을 만들고 사용할 수 있습니다. 키 자격 증명 모음에는 개발자와 보안 관리자의 혜택이 있지만, 조직에 대한 다른 Azure 서비스를 관리하는 조직의 관리자가 구현하고 관리할 수 있습니다. 예를 들어 관리자는 Azure 구독을 사용하여 로그인하고, 키를 저장할 조직에 대한 자격 증명 모음을 만든 후, 다음과 같은 운영 태스크를 담당할 수 있습니다.

- 키 또는 비밀 만들기 또는 가져오기
- 키 또는 비밀 취소 또는 삭제
- 사용자 또는 애플리케이션이 키 자격 증명 모음에 액세스하도록 권한을 부여하므로 해당 키 및 비밀을 관리하거나 사용할 수 있습니다.
- 구성 키 용도(예: 서명 또는 암호화)
- 키 사용량 모니터링

그런 다음 이 관리자는 개발자에게 URI를 제공하여 자신의 애플리케이션에서 호출 하고 보안 관리자에게 해당 키 사용 현황 로깅 정보를 제공합니다. 

![Azure Key Vault의 작동 방식 개요][1]

개발자는 API를 사용하여 직접 키를 관리할 수도 있습니다. 자세한 내용은 [키 자격 증명 모음 개발자 가이드](key-vault-developers-guide.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[자격 증명 모음을 보호하는 방법](key-vault-secure-your-key-vault.md)
<!--Image references--> 알아보기 [1]: ./media/key-vault-whatis/AzureKeyVault_overview.png Azure Key Vault는 대부분의 Azure 지역에 제공됩니다. 자세한 내용은 [키 자격 증명 모음 가격 책정 페이지](https://azure.microsoft.com/pricing/details/key-vault/)를 참조하세요.
