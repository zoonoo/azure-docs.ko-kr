---
title: 데이터 & 저장소 권장 사항-Azure Security Center
description: 이 문서에서는 데이터 및 Azure SQL 서비스를 보호하고 보안 정책을 준수하는 데 도움이 되는 Azure Security Center의 권장 사항에 대해 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: memildin
ms.openlocfilehash: 21da7c49b158345894ee7fdc164d205bcefe1640
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73663904"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Azure Security Center에서 Azure 데이터 및 저장소 서비스 보호
이 항목에서는 데이터 및 저장소 리소스에 대 한 보안 권장 사항을 확인 하 고 구현 하는 방법을 보여 줍니다. Azure Security Center Azure 리소스의 보안 상태를 분석할 때 이러한 권장 사항을 찾았습니다.

## <a name="view-your-data-security-information"></a>데이터 보안 정보 보기

1. **리소스 보안 예방** 섹션에서 **데이터 및 저장소 리소스**를 클릭 합니다.

    ![저장소 리소스 & 데이터](./media/security-center-monitoring/click-data.png)

    데이터 리소스에 대 한 권장 사항이 포함 된 **데이터 보안** 페이지가 열립니다.

    ![데이터 리소스](./media/security-center-monitoring/sql-overview.png)

    이 페이지에서 다음을 수행할 수 있습니다.

    * **개요** 탭을 클릭 하면 수정할 모든 데이터 리소스 권장 사항이 나열 됩니다. 
    * 각 탭을 클릭 하 고 리소스 유형별 권장 사항을 봅니다.

    > [!NOTE]
    > 저장소 암호화에 대 한 자세한 내용은 [미사용 데이터에 대 한 암호화 Azure Storage](../storage/common/storage-service-encryption.md)를 참조 하세요.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>데이터 리소스에 대 한 권장 사항 재구성

1. 리소스 탭에서 리소스를 클릭 합니다. 재구성 될 권장 사항이 나열 된 정보 페이지가 열립니다.

    ![리소스 정보](./media/security-center-monitoring/sql-recommendations.png)

2. 권장 사항을 클릭 합니다. 권장 사항 페이지가 열리고 권장 사항을 구현 하는 **수정 단계가** 표시 됩니다.

   ![수정 단계](./media/security-center-monitoring/remediate1.png)

3. **작업 수행**을 클릭 합니다. 리소스 설정 페이지가 나타납니다.

    ![권장 구성 사용](./media/security-center-monitoring/remediate2.png)

4. **수정 단계** 를 수행 하 고 **저장**을 클릭 합니다.

## <a name="data-and-storage-recommendations"></a>데이터 및 스토리지 권장 사항

|리소스 종류|보안 점수|권장 사항|설명|
|----|----|----|----|
|Storage 계정|20|Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.|보안 전송은 사용자의 스토리지 계정이 보안 연결(HTTPS)에서 오는 요청만 수락하도록 강제 적용하는 옵션입니다. HTTPS는 서버와 서비스 간의 인증을 보장 하 고 전송 중인 데이터를 메시지 가로채기 (man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격 으로부터 보호 합니다.|
|Redis|20|Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.|Azure Cache for Redis에 SSL을 통한 연결만 사용하도록 설정합니다. 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다.|
|SQL|15|SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.|투명한 데이터 암호화를 사용하도록 설정하여 미사용 데이터를 보호하고 규정 준수 요구 사항을 충족합니다.|
|SQL|15|SQL server 감사를 사용 하도록 설정 해야 합니다.|Azure SQL 서버에 대 한 감사를 사용 하도록 설정 합니다. (Azure SQL 서비스에만 해당됩니다. 가상 머신에서 실행 중인 SQL을 포함하지 않습니다.)|
|Data Lake Analytics|5|Data Lake Analytics의 진단 로그를 사용 하도록 설정 해야 합니다.|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|Data Lake Store|5|Azure Data Lake Store의 진단 로그를 사용 하도록 설정 해야 합니다.|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|SQL|30|SQL 데이터베이스의 취약성을 수정해야 합니다.|SQL 취약성 평가는 데이터베이스에서 보안 취약점을 검사 하 고 잘못 된 오류, 과도 한 권한, 보호 되지 않는 중요 한 데이터 등의 모범 사례에 대 한 모든 편차를 제공 합니다. 발견된 취약성을 해결하면 데이터베이스 보안 상태가 크게 향상될 수 있습니다.|
|SQL|20|SQL Server에 대한 Azure AD 관리자 프로비전|SQL Server에 대한 Azure AD 관리자를 프로비전하여 Azure AD 인증을 활성화합니다. Azure AD 인증을 사용하면 데이터베이스 사용자 및 기타 Microsoft 서비스의 권한을 간편하게 관리하고 ID를 한 곳에서 집중적으로 관리할 수 있습니다.|
|Storage 계정|15|방화벽 및 가상 네트워크 구성을 사용 하 여 저장소 계정에 대 한 액세스를 제한 해야 합니다.|스토리지 계정 방화벽 설정에서 무제한 네트워크 액세스를 감사합니다. 또는, 허용되는 네트워크의 애플리케이션만 스토리지 계정에 액세스할 수 있도록 네트워크 규칙을 구성합니다. 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 허용 하려면 특정 Azure virtual network 또는 공용 인터넷 IP 주소 범위에서 트래픽에 대 한 액세스 권한을 부여할 수 있습니다.|
|Storage 계정|1|저장소 계정은 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.|저장소 계정에 대해 새로운 Azure Resource Manager v 2를 사용 하 여 더 강력한 액세스 제어 (RBAC), 향상 된 감사, 리소스 관리자 기반 배포 및 거 버 넌 스, 관리 되는 id 액세스, 키 자격 증명 모음 액세스 등의 향상 된 보안 기능을 제공 합니다. 보안 관리를 위해 태그 및 리소스 그룹에 대 한 암호 및 Azure AD 기반 인증 및 지원을 제공 합니다.|

## <a name="see-also"></a>참고 항목
다른 Azure 리소스 유형에 적용 되는 권장 사항에 대해 자세히 알아보려면 다음 항목을 참조 하세요.

* [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-protection.md)
* [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)

Security Center에 대해 자세히 알아보려면 다음 항목을 참조 하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
