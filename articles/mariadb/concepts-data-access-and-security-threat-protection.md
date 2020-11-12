---
title: Advanced Threat Protection-Azure Database for MariaDB
description: Advanced Threat Protection은 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 018ba33271f8a3a81c3b7109607334b076d8609c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536197"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB Advanced Threat Protection

Azure Database for MariaDB에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다.

> [!IMPORTANT]
> Advanced Threat Protection은 공개 미리 보기로 제공 됩니다.

Advanced Threat Detection은 고급 보안 기능용 통합 패키지인 Advanced Data Security 제품의 일부입니다. 고급 위협 방지는 [Azure Portal](https://portal.azure.com)를 통해 액세스 하 고 관리할 수 있습니다. 이 기능은 범용 및 메모리 액세스에 최적화 된 서버에서 사용할 수 있습니다.

> [!NOTE]
> Advanced Threat Protection 기능을 사용할 수 **없는** Azure 정부 및 소버린 클라우드 지역은 US Gov 텍사스, US Gov 애리조나, US Gov 아이오와, US Gov 버지니아, US DoD 동부, US DoD 중부, 독일 중부, 독일 북부, 중국 동부, 중국 동부 2입니다. 전반적인 제품 사용 가능성을 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.


## <a name="what-is-advanced-threat-protection"></a>Advanced Threat Protection 소개

Azure Database for MariaDB에 대 한 Advanced Threat Protection은 비정상적인 활동에 대 한 보안 경고를 제공 하 여 고객이 발생할 때 잠재적 위협을 감지 하 고 대응할 수 있도록 하는 새로운 보안 계층을 제공 합니다. 사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성, 그리고 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고를 받습니다. Azure Database for MariaDB에 대 한 Advanced Threat Protection은 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합 하며, 의심 스러운 활동의 세부 정보를 포함 하 고 위협을 조사 하 고 완화 하는 방법을 권장 합니다. Azure Database for MariaDB에 대 한 advanced Threat Protection을 사용 하면 보안 전문가가 되거나 고급 보안 모니터링 시스템을 관리할 필요 없이 데이터베이스에 대 한 잠재적인 위협을 쉽게 해결할 수 있습니다. 

![Advanced Threat Protection 개념](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection 경고 
Azure Database for MariaDB에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 하 고 다음 경고를 트리거할 수 있습니다.
- **비정상적인 위치에서 액세스** :이 경고는 다른 사람이 비정상적인 지리적 위치에서 Azure Database for MariaDB 서버에 로그온 한 Azure Database for MariaDB 서버에 대 한 액세스 패턴이 변경 된 경우에 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션 또는 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **비정상적인 azure 데이터 센터에서 액세스** :이 경고는 사용자가 최근 기간 동안이 서버에 표시 된 비정상적인 Azure 데이터 센터에서 서버에 로그온 한 Azure Database for MariaDB 서버에 대 한 액세스 패턴이 변경 된 경우에 트리거됩니다. 경우에 따라 경고는 합법적인 작업 (Azure에서 새 응용 프로그램, Power BI)을 검색 합니다. 다른 경우에서 경고는 Azure 리소스/서비스의 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **익숙하지 않은 사용자의 액세스** :이 경고는 사용자가 비정상적인 보안 주체 (Azure Database for MariaDB 사용자)를 사용 하 여 서버에 로그온 한 Azure Database for MariaDB 서버에 대 한 액세스 패턴이 변경 된 경우에 트리거됩니다. 일부 경우에서 경고는 합법적인 작업(새 애플리케이션, 개발자 유지 관리)을 검색합니다. 다른 경우에서 경고는 악의적인 작업(퇴사 직원, 외부 공격자)을 검색합니다.
- **잠재적으로 위험한 애플리케이션에서 액세스** : 잠재적으로 위험한 애플리케이션이 데이터베이스에 액세스하는 데 사용되는 경우 이 경고가 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 일반적인 공격 도구를 사용하여 공격을 검색합니다.
- **무차별 암호 대입 Azure Database for MariaDB 자격 증명** :이 경고는 다른 자격 증명을 사용 하 여 실패 한 로그인 수가 비정상적으로 많은 경우에 트리거됩니다. 일부 경우에서 경고는 작업의 침투 테스트를 검색합니다. 다른 경우에서 경고는 무차별 암호 대입 공격(brute force attack)을 검색합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Security Center](../security-center/security-center-introduction.md)에 대한 자세한 정보
* 가격 책정에 대 한 자세한 내용은 [Azure Database for MariaDB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/mariadb/) 를 참조 하세요. 
* Azure Portal를 사용 하 여 [Azure Database for MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) 구성