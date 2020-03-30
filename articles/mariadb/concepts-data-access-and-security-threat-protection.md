---
title: 고급 위협 보호 - MariaDB용 Azure 데이터베이스
description: Advanced Threat Protection은 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532181"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>MariaDB 고급 위협 보호를 위한 Azure 데이터베이스

MariaDB용 Azure 데이터베이스에 대한 고급 위협 보호는 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 감지합니다.

> [!IMPORTANT]
> 고급 위협 보호는 공개 미리 보기입니다.

Advanced Threat Detection은 고급 보안 기능용 통합 패키지인 Advanced Data Security 제품의 일부입니다. 고급 위협 보호는 [Azure 포털을](https://portal.azure.com)통해 액세스하고 관리할 수 있습니다. 이 기능은 범용 및 메모리 최적화 된 서버에 사용할 수 있습니다.

> [!NOTE]
> Advanced Threat Protection 기능을 사용할 수 **없는** Azure 정부 및 소버린 클라우드 지역은 US Gov 텍사스, US Gov 애리조나, US Gov 아이오와, US Gov 버지니아, US DoD 동부, US DoD 중부, 독일 중부, 독일 북부, 중국 동부, 중국 동부 2입니다. 전반적인 제품 사용 가능성을 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.


## <a name="what-is-advanced-threat-protection"></a>Advanced Threat Protection 소개

MariaDB용 Azure 데이터베이스에 대한 고급 위협 보호는 비정상적인 활동에 대한 보안 경고를 제공하여 잠재적인 위협을 감지하고 대응할 수 있는 새로운 보안 계층을 제공합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성, 그리고 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고를 받습니다. MariaDB용 Azure 데이터베이스에 대한 고급 위협 보호는 의심스러운 활동에 대한 세부 정보를 포함하는 [Azure 보안 센터와](https://azure.microsoft.com/services/security-center/)경고를 통합하고 위협을 조사하고 완화하는 방법에 대한 작업을 권장합니다. MariaDB용 Azure 데이터베이스에 대한 고급 위협 보호를 사용하면 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대한 잠재적 위협을 쉽게 해결할 수 있습니다. 

![Advanced Threat Protection 개념](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection 경고 
MariaDB용 Azure 데이터베이스에 대한 고급 위협 보호는 데이터베이스에 액세스하거나 악용하려는 비정상적인 활동을 감지하고 다음 경고를 트리거할 수 있습니다.
- **비정상적인 위치에서의 액세스**: 이 경고는 누군가가 특이한 지리적 위치에서 MariaDB 서버의 Azure 데이터베이스에 로그온한 MariaDB 서버의 Azure 데이터베이스에 대한 액세스 패턴이 변경될 때 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션 또는 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **비정상적인 Azure 데이터 센터에서의 액세스**: 이 경고는 최근 기간 동안 이 서버에서 볼 수 있는 비정상적인 Azure 데이터 센터에서 누군가 서버에 로그온한 MariaDB 서버용 Azure 데이터베이스에 대한 액세스 패턴이 변경될 때 트리거됩니다. 경우에 따라 경고는 합법적인 작업(Azure, Power BI의 새 응용 프로그램)을 검색합니다. 다른 경우에서 경고는 Azure 리소스/서비스의 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **익숙하지 않은 보안 주체의 액세스**: 이 경고는 누군가가 비정상적인 보안 주체(MariaDB 사용자를 위한 Azure 데이터베이스)를 사용하여 서버에 로그온한 MariaDB 서버용 Azure 데이터베이스에 대한 액세스 패턴이 변경될 때 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션, 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **잠재적으로 위험한 애플리케이션에서 액세스**: 잠재적으로 위험한 애플리케이션이 데이터베이스에 액세스하는 데 사용되는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 일반적인 공격 도구를 사용하여 공격을 검색합니다.
- **[무차별 암호 대입] 자격 증명에 대한 Azure Database**: 이 경고는 다른 자격 증명을 가진 실패한 로그인수가 비정상적으로 많을 때 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 무차별 암호 대입 공격(brute force attack)을 검색합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 보안 센터에](https://docs.microsoft.com/azure/security-center/security-center-intro) 대해 자세히 알아보기
* 가격 책정에 대한 자세한 내용은 [MariaDB 가격 책정 페이지의 Azure 데이터베이스를](https://azure.microsoft.com/pricing/details/mariadb/) 참조하십시오. 
* [Azure 포털을 사용하여 MariaDB 고급 위협 보호를 위한 Azure 데이터베이스](howto-database-threat-protection-portal.md) 구성  
