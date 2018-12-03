---
title: Dedicated HSM이란? | Microsoft Docs
description: Azure Dedicated HSM은 FIPS 140-2 레벨 3 인증을 충족하는 Azure 내에서 키 스토리지 기능을 제공함
services: dedicated-hsm
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 11/26/2018
ms.author: barclayn
ms.openlocfilehash: 92d77ec886a0f37c28f5e3031a7e14f63299c8aa
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52427117"
---
# <a name="what-is-dedicated-hsm"></a>Dedicated HSM이란?

Azure Dedicated HSM은 Azure에 가장 엄격한 보안 요구 사항을 충족하는 암호화 키 스토리지를 제공합니다. Dedicated HSM은 FIPS 140-2 수준 3 인증을 충족한 디바이스와 HSM 어플라이언스에 대한 완벽하고 독점적인 컨트롤이 필요한 고객에게 이상적인 솔루션입니다. HSM 디바이스는 여러 Azure 지역 간에 전역적으로 배포되며, 디바이스 쌍으로 손쉽게 프로비전할 수 있고, 고가용성을 지원하도록 구성됩니다. 또한 HSM을 지역 간에 프로비전하여 지역 수준의 장애 조치(failover)를 보장할 수 있습니다. Microsoft는 Gemalto의 [SafeNet Luna Network HSM 7(모델 A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) 어플라이언스를 사용하여 Dedicated HSM 서비스를 제공해 왔습니다. 이 디바이스는 최고 수준의 성능과 암호화 통합 옵션을 제공합니다. 프로비전이 완료되면 HSM이 고객의 가상 네트워크에 직접 연결되고, 지점과 사이트 간 또는 사이트 간 VPN 연결을 구성하여 온-프레미스 애플리케이션 및 관리 도구로 액세스될 수도 있습니다. 고객은 Gemalto의 지원 포털에서 소프트웨어 및 설명서를 받고 HSM 디바이스를 구성하고 관리하게 됩니다.

## <a name="why-use-azure-dedicated-hsm"></a>Azure Dedicated HSM을 사용하는 이유

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 수준 3 규정 준수

많은 조직에는 암호화 키 스토리지가 [FIPS 140-2 수준 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) 요구 사항을 준수해야 한다고 명시하는 엄격한 산업 규정이 있습니다. Microsoft의 다중 테넌트 Azure Key Vault 서비스는 현재 FIPS 140-2 수준 2 인증만 제공합니다. Azure Dedicated HSM은 금융 서비스 산업, 정부 기관 및 기타 FIPS 140-2 수준 3 요구 사항을 준수해야 하는 조직의 실제 요구 사항을 충족합니다.

### <a name="single-tenant-devices"></a>단일 테넌트 서비스

대부분의 고객은 암호화 스토리지 디바이스의 단일 테넌트에 대한 요구 사항이 있습니다. Azure Dedicated HSM 서비스는 Microsoft의 전역적으로 배포된 데이터 센터 중 하나에서 물리적 디바이스의 프로비전을 허용합니다. 디바이스가 고객에게 프로비전되면 해당 고객만 디바이스에 액세스할 수 있게 됩니다.

### <a name="full-administrative-control"></a>완전한 관리 제어

대다수의 고객은 단일 테넌트 디바이스뿐만 아니라 완전한 관리 제어와 관리 전용 액세스도 필요로 합니다. 디바이스가 프로비전되면 해당 고객만 관리 또는 애플리케이션 수준에서 디바이스에 액세스할 수 있습니다. 고객이 첫 액세스 후 암호를 변경하면 Microsoft는 관리 제어 권한을 상실하게 됩니다. 이때부터 고객은 완전한 관리 제어 권한과 애플리케이션 관리 권한을 가진 진정한 단일 테넌트입니다. Microsoft는 직렬 포트 연결을 통해 원격 분석에 대한 모니터 수준 액세스(관리자 역할 아님)를 유지하여 온도, 전원 공급 장치 상태, 팬 상태와 같은 하드웨어 모니터를 처리합니다. 고객은 필요할 경우 이러한 기능을 언제든지 사용하지 않을 수 있지만 그럴 경우 Microsoft에서 보내는 자동 관리 상태 알림을 받지 못하게 됩니다.

### <a name="high-performance"></a>고성능

Gemalto 디바이스가 이 서비스에 선택된 이유는 광범위한 암호화 알고리즘, 다양한 운영 체제, 광범위한 API를 지원하기 때문입니다. 배포된 특정 모델은 RSA-2048의 경우 초당 작업 10,000개로 우수한 성능을 제공합니다. 고유한 애플리케이션 인스턴스에 사용할 수 있는 파티션 10개를 지원합니다. 이는 짧은 대기 시간, 대용량, 높은 처리량을 지원하는 디바이스입니다.

### <a name="unique-cloud-based-offering"></a>고유한 클라우드 기반 솔루션

Microsoft는 고유한 고객층에서 특정한 수요를 인식했으며, 신규 고객에게 FIPS 140-2 수준 3 인증을 받은 Dedicated HSM 서비스를 제공하고, 이처럼 광범위한 클라우드 기반 및 온-프레미스 애플리케이션 통합을 제공하는 유일한 클라우드 공급자입니다.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure Dedicated HSM이 적합한지 여부

Azure Dedicated HSM은 특정 유형의 대규모 조직에 존재하는 고유한 요구 사항을 해결하는 전문 서비스입니다. 따라서 이 서비스의 사용 프로필에 대다수의 Azure 고객이 적합하지 않을 것으로 예상됩니다. 대다수는 Azure Key Vault 서비스가 더 적합하고 비용 효율이 높을 것입니다. 요구 사항에 맞게 적합한 서비스를 결정하는 데 도움이 될 수 있도록 다음과 같은 기준을 마련했습니다.

### <a name="best-fit"></a>가장 적합한 경우

HSM 디바이스에 직접 단독으로 액세스해야 하는 “리프트 앤 시프트” 시나리오에 가장 적합합니다. 다음은 이러한 템플릿의 예입니다.

- 온-프레미스에서 Azure Virtual Machines로 애플리케이션 마이그레이션
- AWS Cloud HSM Classic 서비스(Amazon에서는 신규 고객에게 이 서비스를 제공하지 않음)를 사용하는 Amazon AWS EC2의 애플리케이션을 Azure Virtual Machines로 마이그레이션
- Azure Virtual Machines에서 Apache/Ngnix SSL Offload, Oracle TDE 및 ADCS 같은 축소 래핑된 소프트웨어 실행

### <a name="not-a-fit"></a>적합하지 않은 경우

고객 관리 키를 사용한 암호화를 지원하는 Microsoft 클라우드 서비스(예: Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL, Office 365 Customer Key)는 Azure Dedicated HSM과 통합되지 않습니다.

### <a name="it-depends"></a>경우에 따라 다른 경우

대부분의 경우 복잡한 요구 사항의 조합, 절충 가능 여부에 따라 달라집니다. 한 가지 예로 FIPS 140-2 수준 3 요구 사항이 있습니다. 이는 필수적인 경우가 많으므로 현재 Dedicated HSM이 유일한 옵션입니다.  이러한 필수 요구 사항과 관련이 없는 경우에는 요구 사항의 조합을 평가한 결과에 따라 Azure Key Vault와 Dedicated HSM 중에 하나를 결정하는 경우가 많습니다. 다음은 이러한 템플릿의 예입니다.

- 고객의 Azure Virtual Machine에서 실행되는 새로운 코드
- Azure Virtual Machine의 SQL Server TDE
- Azure Storage 클라이언트 쪽 암호화
- SQL Server 및 Azure SQL DB Always Encrypted

## <a name="next-steps"></a>다음 단계

이 서비스는 매우 전문적이므로 이 설명서 세트에 나온 주요 개념을 완전히 이해하고, 가격 책정, 지원 및 서비스 수준 계약을 완전히 이해한 후 기존 가상 네트워크 환경으로 HSM을 용이하게 프로비전할 수 있게 해주는 자습서를 보는 것이 좋습니다. [Gemalto 통합 가이드](https://safenet.gemalto.com/partners/microsoft/) 및 배포 아키텍처 결정 방법 가이드도 유용한 리소스입니다.

* [고가용성](high-availability.md)
* [물리적 보안](physical-security.md)
* [네트워킹](networking.md)
* [지원 가능성](supportability.md)
* [모니터링](monitoring.md)
