---
title: Dedicated HSM이란? - Azure 전용 HSM | Microsoft Docs
description: Azure 전용 HSM 개요에서는 Azure 내에서 FIPS 140-2 수준 3 인증을 충족하는 키 스토리지 기능을 제공합니다.
services: dedicated-hsm
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 88a49c9c124c5399749d2b60595d7f5c7ec77b20
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "62117995"
---
# <a name="what-is-azure-dedicated-hsm"></a>Azure 전용 HSM이란?

Azure 전용 HSM은 Azure에 암호화 키 스토리지를 제공하는 Azure 서비스입니다. 전용 HSM은 가장 엄격한 보안 요구 사항을 충족합니다. FIPS 140-2 수준 3 인증을 충족한 디바이스와 HSM 어플라이언스에 대한 완벽하고 독점적인 컨트롤이 필요한 고객에게 이상적인 솔루션입니다. 

 HSM 디바이스는 여러 Azure 지역에 전역적으로 배포됩니다. 한 쌍의 디바이스로 손쉽게 프로비전하고 고가용성을 위해 구성할 수 있습니다. HSM 디바이스를 여러 지역에 걸쳐 프로비전하여 지역 수준의 장애 조치(failover)를 보장할 수도 있습니다. Microsoft는 Gemalto의 [SafeNet Luna Network HSM 7(모델 A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) 어플라이언스를 사용하여 Dedicated HSM 서비스를 제공합니다. 이 디바이스는 최고 수준의 성능과 암호화 통합 옵션을 제공합니다. 

HSM 디바이스는 프로비전된 후에 고객의 가상 네트워크에 직접 연결됩니다. 또한 지점 및 사이트 간 또는 사이트 간 VPN 연결을 구성할 때 온-프레미스 애플리케이션 및 관리 도구를 통해 액세스할 수도 있습니다. 고객은 Gemalto의 지원 포털에서 HSM 디바이스를 구성하고 관리하는 소프트웨어 및 설명서를 구할 수 있습니다.

## <a name="why-use-azure-dedicated-hsm"></a>Azure Dedicated HSM을 사용하는 이유

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 수준 3 규정 준수

많은 조직에는 암호화 키 스토리지가 [FIPS 140-2 수준 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) 요구 사항을 준수해야 한다고 명시하는 엄격한 산업 규정이 있습니다. Microsoft의 다중 테넌트 Azure Key Vault 서비스는 현재 FIPS 140-2 수준 2 인증만 제공합니다. Azure Dedicated HSM은 금융 서비스 산업, 정부 기관 및 기타 FIPS 140-2 수준 3 요구 사항을 준수해야 하는 조직의 실제 요구 사항을 충족합니다.

### <a name="single-tenant-devices"></a>단일 테넌트 디바이스

대부분의 고객은 암호화 스토리지 디바이스의 단일 테넌트에 대한 요구 사항이 있습니다. Azure Dedicated HSM 서비스를 통해 Microsoft의 전역적으로 배포된 데이터 센터 중 하나에서 물리적 디바이스를 프로비전할 수 있습니다. 디바이스가 고객에게 프로비전되면 해당 고객만 디바이스에 액세스할 수 있습니다.

### <a name="full-administrative-control"></a>완전한 관리 제어

많은 고객이 관리를 목적으로 자체 디바이스에 대한 완전한 관리 제어와 단독 액세스를 필요로 합니다. 디바이스가 프로비전된 후에는 해당 고객만 디바이스에 대한 관리 또는 애플리케이션 수준 액세스 권한을 갖습니다.

 고객에 디바이스에 처음으로 액세스한 후 암호를 변경하면 Microsoft에 관리 제어 권한이 없습니다. 이 시점부터 고객은 완전한 관리 제어 권한과 애플리케이션 관리 권한이 있는 진정한 단일 테넌트가 됩니다. Microsoft는 직렬 포트 연결을 통해 원격 분석에 대한 모니터링 수준 액세스(관리자 역할 아님)를 유지합니다. 이 액세스는 온도, 전원 공급 장치 상태 및 팬 상태와 같은 하드웨어 모니터링을 포함합니다. 
 
 고객은 필요에 따라 이 모니터링을 사용하지 않도록 자유롭게 설정할 수 있습니다. 단, 사용하지 않도록 설정하면 Microsoft로부터 사전 예방적인 상태 경고를 받을 수 없습니다.

### <a name="high-performance"></a>고성능

이 서비스를 위해 Gemalto 디바이스가 선택된 이유는 여러 가지입니다. 광범위한 암호화 알고리즘을 지원하고, 다양한 운영 체제를 지원하며, 광범위한 API가 지원됩니다. 배포된 특정 모델은 RSA-2048의 경우 초당 작업 10,000개로 뛰어난 성능을 제공합니다. 고유한 애플리케이션 인스턴스에 사용할 수 있는 파티션 10개를 지원합니다. 이 디바이스는 짧은 대기 시간, 대용량, 높은 처리량을 지원하는 디바이스입니다.

### <a name="unique-cloud-based-offering"></a>고유한 클라우드 기반 제품

Microsoft는 고유한 고객층에서 특정한 수요를 인식했으며, 신규 고객에게 FIPS 140-2 수준 3 인증을 받은 Dedicated HSM 서비스를 제공하고, 이처럼 광범위한 클라우드 기반 및 온-프레미스 애플리케이션 통합을 제공하는 유일한 클라우드 공급자입니다.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Azure Dedicated HSM이 적합한지 여부

Azure Dedicated HSM은 특정 유형의 대규모 조직을 위해 고유한 요구 사항을 해결하는 전문 서비스입니다. 따라서 많은 Azure 고객은 이 서비스의 사용 프로필에 적합하지 않을 것으로 예상됩니다. Azure Key Vault 서비스가 더 적합하고 비용 효율적인 고객이 많을 것으로 예상됩니다. 요구 사항에 맞는지 판단하는 데 도움이 될 수 있도록 다음과 같은 기준을 마련했습니다.

### <a name="best-fit"></a>가장 적합한 경우

Azure Dedicated HSM은 HSM 디바이스에 직접 단독으로 액세스해야 하는 “리프트 앤 시프트” 시나리오에 가장 적합합니다. 다음은 이러한 템플릿의 예입니다.

- 온-프레미스에서 Azure Virtual Machines로 애플리케이션 마이그레이션
- Amazon AWS EC2에서 AWS Cloud HSM Classic 서비스(Amazon에서는 신규 고객에게 이 서비스를 제공하지 않음)를 사용하는 가상 머신으로 애플리케이션 마이그레이션
- Azure Virtual Machines에서 Apache/Ngnix SSL Offload, Oracle TDE 및 ADCS와 같은 축소 래핑된 소프트웨어 실행 

### <a name="not-a-fit"></a>적합하지 않은 경우

Azure Dedicated HSM는 다음과 같은 유형의 시나리오에는 적합하지 않습니다. Azure Dedicated HSM과 통합되지 않은 고객 관리형 키로 암호화를 지원하는 Microsoft 클라우드 서비스(예: Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database 및 Office 365용 고객 키).

### <a name="it-depends"></a>경우에 따라 다른 경우

Azure Dedicated HSM이 적합할지 여부는 가능하거나 가능하지 않은 잠재적으로 복잡한 요구 사항과 절충안의 혼합에 달려 있습니다. 한 가지 예는 FIPS 140-2 수준 3 요구 사항입니다. 이 요구 사항은 일반적이며, Dedicated HSM은 현재 이것을 충족할 수 있는 유일한 옵션입니다. 위임된 요구 사항이 관련이 없는 경우에는 Azure Key Vault와 Dedicated HSM 중에서 선택해야 하는 경우가 많습니다. 결정을 내리기 전에 요구 사항을 평가하십시오.

옵션을 평가해야 하는 경우는 다음과 같습니다. 

- 새 코드가 고객의 Azure 가상 머신에서 실행되는 경우
- SQL Server TDE가 Azure 가상 머신에 있는 경우
- Azure Storage 클라이언트 쪽 암호화
- SQL Server 및 Azure SQL DB Always Encrypted

## <a name="next-steps"></a>다음 단계

이 서비스는 매우 전문적인 서비스입니다. 따라서 이 문서에서 주요 개념(예: 가격 책정, 고객 지원팀, 서비스 수준 계약)을 완전히 이해하는 것이 좋습니다. 

[Gemalto 통합 가이드](https://safenet.gemalto.com/partners/microsoft/)는 기존 가상 네트워크 환경에 HSM을 쉽게 프로비전하는 데 도움이 됩니다. 배포 아키텍처를 설정하는 방법을 결정하는 데 도움이 되는 방법 가이드도 있습니다.

* [고가용성](high-availability.md)
* [물리적 보안](physical-security.md)
* [네트워킹](networking.md)
* [지원 가능성](supportability.md)
* [모니터링](monitoring.md)
