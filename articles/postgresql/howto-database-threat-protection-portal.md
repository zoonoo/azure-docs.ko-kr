---
title: 고급 위협 보호-Azure Database for PostgreSQL-단일 서버
description: Threat Protection은 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: a69a8b651188b9566fdbec4bc6de09a8fec164c7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067461"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>단일 서버-PostgreSQL 용 Azure 데이터베이스에 대 한 advanced Threat Protection

Azure Database for PostgreSQL용 Advanced Threat Protection은 비정상적이며 유해할 가능성이 있는 데이터베이스 액세스 또는 악용 시도를 나타내는 비정상적인 활동을 감지합니다.

Advanced Threat Detection은 고급 보안 기능용 통합 패키지인 Advanced Data Security 제품의 일부입니다. [Azure Portal](https://portal.azure.com)을 통해 액세스 및 관리할 수 있는 Advanced Threat Protection은 현재 미리 보기로 제공됩니다.

> [!NOTE]
> 다음 Azure Government 및 소버린 클라우드 지역에서는 Advanced Threat Protection 기능을 사용할 수 **없습니다**. US Gov 텍사스, US Gov 애리조나, US Gov 아이오와, US Gov 버지니아, US DoD 동부, US DoD 중부, 독일 중부, 독일 북부, 중국 동부, 중국 동부 2. 전반적인 제품 사용 가능성을 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.
>

> [!NOTE]
> 이 기능은 Azure Database for PostgreSQL이 범용 및 메모리 최적화 서버용으로 배포된 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="set-up-threat-detection"></a>위협 감지 설정
1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 보호할 Azure Database for PostgreSQL 서버의 구성 페이지로 이동합니다. 보안 설정에서 **Advanced Threat Protection(미리 보기)** 을 선택합니다.
3. **Advanced Threat Protectio(미리 보기)** 구성 페이지에서 다음을 수행합니다.

   - 서버에서 Advanced Threat Protection을 사용하도록 설정.
   - **Advanced Threat Detection 설정**의 **경고 전송 대상** 텍스트 상자에 비정상적인 데이터베이스 활동 발견 시 보안 경고를 받을 이메일 목록을 입력합니다.
  
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

Advanced Threat Protection의 경고는 [Azure Security Center](https://azure.microsoft.com/services/security-center/)에 통합되어 있습니다. 

**Threat Protection** 아래에서 **보안 경고**를 클릭하여 Azure Security Center 경고 페이지를 시작한 다음 데이터베이스에서 검색된 활성 SQL 위협의 개요를 확인합니다.

  ![위협 보호 asc](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>다음 단계

* [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)에 대한 자세한 정보
* 가격 책정에 대한 자세한 내용은 [Azure Database for PostgreSQL 가격 책정 페이지](https://azure.microsoft.com/pricing/details/postgresql/)를 참조하세요.  
