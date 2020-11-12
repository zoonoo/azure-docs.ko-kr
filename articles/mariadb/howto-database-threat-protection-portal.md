---
title: Advanced Threat Protection-Azure Portal-Azure Database for MariaDB
description: Azure Database for MariaDB에 대 한 위협 방지는 데이터베이스에 대 한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색 합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7734feddabb1a4a86e7932da3ef4adc57352637e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542697"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Azure Database for MariaDB에 대 한 Advanced Threat Protection

Azure Database for MariaDB에 대 한 Advanced Threat Protection은 비정상적인 활동을 감지 하 여 데이터베이스에 액세스 하거나 악용 하려는 비정상적인 시도를 감지 합니다.

Advanced Threat Detection은 고급 보안 기능용 통합 패키지인 Advanced Data Security 제품의 일부입니다. 고급 위협 방지는 [Azure Portal](https://portal.azure.com)를 통해 액세스 하 고 관리할 수 있습니다.

> [!IMPORTANT]
> Advanced Threat Protection은 공개 미리 보기로 제공 됩니다. 이 기능은 Azure Database for MariaDB가 범용 및 메모리 최적화 서버용으로 배포된 모든 Azure 지역에서 사용할 수 있습니다.

> [!NOTE]
> Advanced Threat Protection 기능을 사용할 수 **없는** Azure 정부 및 소버린 클라우드 지역은 US Gov 텍사스, US Gov 애리조나, US Gov 아이오와, US Gov 버지니아, US DoD 동부, US DoD 중부, 독일 중부, 독일 북부, 중국 동부, 중국 동부 2입니다. 전반적인 제품 사용 가능성을 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.

## <a name="set-up-threat-detection"></a>위협 감지 설정
1. 에서 Azure Portal를 시작 [https://portal.azure.com](https://portal.azure.com) 합니다.
2. 보호 하려는 Azure Database for MariaDB 서버의 구성 페이지로 이동 합니다. 보안 설정에서 **Advanced Threat Protection(미리 보기)** 을 선택합니다.
3. **Advanced Threat Protectio(미리 보기)** 구성 페이지에서 다음을 수행합니다.

   - 서버에서 Advanced Threat Protection을 사용하도록 설정.
   - **Advanced Threat Detection 설정** 의 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 발견 시 보안 경고를 받을 이메일 목록을 입력합니다.
  
   ![위협 감지 설정](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>비정상적인 데이터베이스 활동 살펴보기

비정상적인 데이터베이스 활동이 감지되면 메일 알림을 받습니다. 메일에는 비정상적인 활동의 특징, 데이터베이스 이름, 서버 이름, 애플리케이션 이름, 이벤트 시간을 비롯한 의심스러운 보안 이벤트에 대한 정보가 제공됩니다. 또한 전자 메일에는 가능한 원인에 대한 정보 및 데이터베이스에 대한 잠재적인 위협을 조사하고 완화시키기 위해 권장되는 조치가 제공됩니다.
 
1. 이메일에서 **최근 경고 보기** 링크를 클릭하여 Azure Portal을 시작하고, SQL 데이터베이스에서 검색된 활성 SQL 위협을 대략적으로 보여주는 Azure Security Center 경고 페이지를 표시합니다.
    
    ![비정상적인 활동 보고서](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    활성 위협 보기:

    ![활성 위협](./media/howto-database-threat-protection-portal/active-threats.png)

2. 특정 경고를 클릭하여 이 위협을 조사하고 향후 위협을 수정하기 위한 추가 세부 정보 및 조치를 확인합니다.
    
    ![특정 경고](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>위협 검색 경고 살펴보기

SQL Database 위협 감지는 경고를 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합합니다. Azure Portal의 데이터베이스 및 SQL ATP 블레이드 내에 있는 라이브 SQL 위협 감지 타일은 활성 위협의 상태를 추적합니다.

**보안 위협 경고** 를 클릭하면 Azure Security Center 경고 페이지가 열리고 데이터베이스에서 감지된 활성 SQL 위협에 대한 개요가 제공됩니다.

   ![위협 감지 경고](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>다음 단계

* [Azure Security Center](../security-center/security-center-introduction.md)에 대한 자세한 정보
* 가격 책정에 대 한 자세한 내용은 [Azure Database for MariaDB 가격 책정 페이지](https://azure.microsoft.com/pricing/details/mariadb/) 를 참조 하세요.