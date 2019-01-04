---
title: Azure Stack Key Vault 소개 | Microsoft Docs
description: Azure Stack Key Vault 키 및 비밀을 관리 하는 방법에 대해 알아봅니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: a6b4e8c3543d4681c92fbbde30eec0a543fcb0fd
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139287"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Azure Stack의 주요 자격 증명 모음 소개

## <a name="prerequisites"></a>필수 조건 

* Azure Key Vault 서비스를 포함 하는 제품을 구독 해야 합니다.  
* [PowerShell Azure Stack과 함께 사용 하도록 구성 되어](azure-stack-powershell-configure-user.md)입니다.
 
## <a name="key-vault-basics"></a>키 자격 증명 모음 기본 사항
Azure Stack에서 Key Vault는 암호화 키를 보호 하 고 암호는 클라우드 응용 프로그램 및 서비스 사용 합니다. Key Vault를 사용 하 여 암호화할 수 있습니다 키 및 암호와 같은:
   * 인증 키 
   * Storage 계정 키
   * 데이터 암호화 키
   * .pfx 파일
   * 암호

키 자격 증명 모음은 키 관리 프로세스를 간소화하고 데이터를 액세스하고 암호화 하는 키의 제어를 유지할 수 있습니다. 개발자는 개발 및 테스트(분)을 위한 키를 만든 다음, 프로덕션 키로 원활하게 마이그레이션할 수 있습니다. 보안 관리자는 필요한 경우 권한을 키로 부여(및 해지)할 수 있습니다.

Azure Stack 구독을 사용 하 여 모든 사용자가 만들고 사용할 수 있는 키 자격 증명 모음입니다. Key Vault 혜택이 있지만 개발자와 보안 관리자, 조직에 대 한 다른 Azure Stack 서비스를 관리 하는 연산자를 구현 하 고 관리할 수 있습니다. 예를 들어, Azure Stack 연산자는 Azure Stack 구독에 로그인 할 수는 키를 저장 한 다음 이러한 운영 작업을 담당 하는 조직에 대 한 자격 증명 모음을 만듭니다.

* 만들거나 키나 비밀을 가져옵니다.
* 취소 하거나 키나 비밀을 삭제 합니다.
* 사용자 또는 응용 프로그램 다음 관리 하거나 해당 키 및 암호를 사용할 수 있도록 key vault 액세스 권한을 부여 합니다.
* 구성 키 용도 (예: 서명 또는 암호화) 합니다.

연산자는 Uniform Resource Identifier (Uri)가 응용 프로그램에서 호출을 사용 하 여 개발자가 제공할 수 있습니다. 연산자는 키 사용 현황 로깅 정보를 사용 하 여 보안 관리자를 제공할 수도 있습니다.

개발자는 API를 사용하여 직접 키를 관리할 수도 있습니다. 자세한 내용은 Key Vault 개발자 가이드를 참조 하세요.

## <a name="scenarios"></a>시나리오
다음 시나리오는 Key Vault 어떻게 도움이 되는지 개발자와 보안 관리자의 요구를 충족을 설명 합니다.

### <a name="developer-for-an-azure-stack-application"></a>Azure Stack 응용 프로그램 개발자
**문제:** 서명 및 암호화 키를 사용 하는 Azure Stack에 대 한 응용 프로그램을 작성 합니다. 이러한 키를 되도록 응용 프로그램에서 외부 솔루션은 지리적으로 분산 되는 응용 프로그램에 적합 합니다.

**문:** 키 자격 증명 모음에 저장 되 고 필요한 경우를 URI로 호출 합니다.

### <a name="developer-for-software-as-a-service-saas"></a>소프트웨어에 대 한 개발자 서비스 (SaaS)
**문제:** 내 고객의 키와 비밀에 대 한 책임 또는 잠재적인 책임을 원하지 않는 합니다. 수행할를 제공 하는 핵심 소프트웨어 기능을 수행 하 집중할 수 있도록 고객 소유 하 고 해당 키를 관리 합니다.

**문:** 고객을 Azure Stack에 고유한 키를 가져오고이 관리 하도록 합니다. 

### <a name="chief-security-officer-cso"></a>수석 보안 책임자 (CSO)
**문제:** 내 조직이 키 수명 주기를 제어 이며 키 사용을 모니터링할 수 있는지 확인 하려고 합니다.

**문:** Key Vault는 Microsoft을 참조 하거나 키를 추출 하지 않습니다 있도록 설계 되었습니다. 응용 프로그램을 고객의 키를 사용 하 여 암호화 작업을 수행 해야 경우 Key Vault는 응용 프로그램 대신 키를 사용 합니다. 애플리케이션에서 고객의 키를 표시하지 않습니다. 여러 Azure Stack 서비스 및 리소스를 사용 하지만 Azure Stack의 단일 위치에서 키를 관리할 수 있습니다. 자격 증명 모음 지원 및 응용 프로그램을 사용 하 여 해당 얼마나 많은 자격 증명 모음에에서 있는 Azure Stack 지역 것에 관계 없이 단일 인터페이스를 제공 합니다.

## <a name="next-steps"></a>다음 단계

* [포털을 사용 하 여 Azure Stack에서 Key Vault를 관리 합니다.](azure-stack-kv-manage-portal.md)  
* [PowerShell을 사용 하 여 Azure Stack에서 Key Vault를 관리 합니다.](azure-stack-kv-manage-powershell.md)

