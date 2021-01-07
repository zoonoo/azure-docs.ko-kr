---
title: Azure 관리형 HSM 개요 - Azure 관리형 HSM | Microsoft Docs
description: Azure 관리형 HSM은 클라우드 애플리케이션에 대한 암호화 키를 보호하는 클라우드 서비스입니다.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 95feeebf009cae22cf3952df80596c12e75cbe4b
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357575"
---
# <a name="what-is-azure-key-vault-managed-hsm-preview"></a>Azure Key Vault 관리형 HSM(미리 보기)이란?

Azure Key Vault 관리형 HSM은 **FIPS 140-2 수준 3** 유효성이 검사된 HSM을 사용하여 클라우드 애플리케이션용 암호화 키를 보호할 수 있는 완전 관리형 고가용 단일 테넌트 표준 규격 클라우드 서비스입니다.  

## <a name="why-use-managed-hsm"></a>관리형 HSM을 사용하는 이유

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>완전 관리형, 고가용성, 서비스로 제공되는 단일 테넌트 HSM

- **완전 관리형** : HSM 프로비저닝, 구성, 패치 및 유지 관리는 서비스에 의해 처리됩니다. 
- **고가용성 및 영역 복원력** (가용성 영역이 지원되는 경우): 각 HSM 클러스터는 두 개 이상의 가용성 영역에 걸쳐 있는 여러 HSM 파티션으로 구성됩니다. 하드웨어에 오류가 발생하면 HSM 클러스터에 대한 멤버 파티션이 정상 노드로 자동 마이그레이션됩니다.
- **단일 테넌트** : 각 관리형 HSM 인스턴스는 단일 고객 전용이며 여러 HSM 파티션의 클러스터로 구성됩니다. 각 HSM 클러스터는 각 고객의 HSM 클러스터를 암호화로 격리하는 별도의 고객 관련 보안 도메인을 사용합니다.


### <a name="access-control-enhanced-data-protection--compliance"></a>액세스 제어, 향상된 데이터 보호 & 준수

- **중앙 집중식 키 관리** : 조직 전체에서 중요하고 가치가 높은 키를 한 곳에서 관리합니다. 키당 세분화된 권한으로 '최소 권한 있는 액세스' 원칙에 따라 각 키에 대한 액세스를 제어합니다.
- **격리된 액세스 제어** : 관리형 HSM "로컬 RBAC" 액세스 제어 모델을 통해 지정된 HSM 클러스터 관리자는 관리 그룹, 구독 또는 리소스 그룹 관리자도 재정의할 수 없는 HSM을 완벽하게 제어할 수 있습니다.
- **FIPS 140-2 수준 3 유효성이 검사된 HSM** : FIPS((Federal Information Protection Standard)) 140-2 수준 3 유효성이 검사된 HSM을 사용하여 데이터를 보호하고 규정 준수 요구 사항을 충족합니다. 관리형 HSM은 Marvell LiquidSecurity HSM 어댑터를 사용합니다.
- **모니터링 및 감사** : Azure Monitor와 완전히 통합됩니다. Azure Monitor를 통해 모든 활동의 전체 로그를 가져옵니다. 분석 및 경고에 Azure Log Analytics를 사용합니다.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Azure 및 Microsoft PaaS/SaaS 서비스와 통합 

- 키를 생성(또는 [BYOK](hsm-protected-keys-byok.md)를 사용하여 가져오기)하고 이 키를 사용하여 [Azure Storage](../../storage/common/customer-managed-keys-overview.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) 및 [Azure Information Protection](/azure/information-protection/byok-price-restrictions) 등의 Azure 서비스에서 미사용 데이터를 암호화합니다.

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Key Vault와 동일한 API 및 관리 인터페이스 사용

- 관리형 HSM을 사용하도록 자격 증명 모음(다중 테넌트)을 사용하는 기존 애플리케이션을 쉽게 마이그레이션합니다.
- 사용 중인 키 관리 솔루션에 관계 없이 모든 애플리케이션에 대해 동일한 애플리케이션 개발 및 배포 패턴 사용: 다중 테넌트 자격 증명 모음 또는 단일 테넌트 관리형 HSM

### <a name="import-keys-from-your-on-premise-hsms"></a>온-프레미스 HSM에서 키 가져오기

- 온-프레미스 HSM에서 HSM 보호 키를 생성하고 관리형 HSM으로 안전하게 가져오기

## <a name="next-steps"></a>다음 단계
- [빠른 시작: Azure CLI를 사용하여 관리형 HSM 프로비저닝 및 활성화](quick-create-cli.md)를 참조하여 관리형 HSM을 만들고 활성화합니다.
- [Azure Key Vault 관리형 HSM을 사용하는 모범 사례](best-practices.md)를 참조하세요.
