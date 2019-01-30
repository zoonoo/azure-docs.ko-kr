---
title: MySQL 호스팅 서버를 Azure Stack의 | Microsoft Docs
description: MySQL 어댑터 리소스 공급자를 통해 프로 비전을 위한 MySQL 인스턴스를 추가 하는 방법
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 3a80e3a95252b60dd39ad4ce20878922aeee5730
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246885"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>MySQL 리소스 공급자에 대 한 호스팅 서버를 추가 합니다.

가상 컴퓨터 (VM)에서 MySQL 인스턴스를 호스트할 수 있습니다 [Azure Stack](azure-stack-poc.md), 또는 MySQL 리소스 공급자 인스턴스에 연결할 수 있는 만큼 Azure Stack 환경 외부 VM.

> [!NOTE]
> MySQL 리소스 공급자 서버의 MySQL 데이터베이스를 만들어야 합니다. 청구 되는 사용자 구독에 MySQL 호스팅 서버를 만들어야 하는 동안 기본 공급자 구독에 MySQL 리소스 공급자를 만들어야 합니다. 리소스 공급자 서버는 사용자 데이터베이스를 호스트에 사용할 수 없습니다.

MySQL 버전 5.6, 5.7 및 8.0 호스팅 서버에 대해 사용할 수 있습니다. MySQL RP caching_sha2_password 인증을 지원 하지 않습니다. 다음 릴리스에서 추가 됩니다. MySQL 8.0 서버 mysql_native_password를 사용 하도록 구성 되어야 합니다. MariaDB도 지원 됩니다.

## <a name="connect-to-a-mysql-hosting-server"></a>MySQL 호스팅 서버에 연결

시스템 관리자 권한이 있는 계정의 자격 증명이 있는지 확인 합니다. 호스팅 서버를 추가 하려면 다음이 단계를 수행 합니다.

1. 서비스 관리자로 Azure Stack 연산자 포털에 로그인
2. **모든 서비스**를 선택합니다.
3. 아래는 **관리 리소스** 범주 선택 **MySQL 호스팅 서버** > **+ 추가**합니다. 열립니다는 **MySQL 호스팅 서버 추가** 대화 상자에서 다음 화면 캡처에 표시 합니다.

   ![호스팅 서버를 구성 합니다.](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. MySQL Server 인스턴스의 연결 세부 정보를 제공 합니다.

   * 에 대 한 **MySQL 호스팅 서버 이름**, 정규화 된 도메인 이름 (FQDN) 또는 유효한 IPv4 주소를 제공 합니다. 짧은 VM 이름을 사용 하지 마세요.
   * 기본 MySQL 인스턴스를이 제공 되지 않습니다 지정 해야 합니다 **의 호스팅 서버의 크기 (GB)** 합니다. 데이터베이스 서버의 용량에 가까운 크기를 입력 합니다.
   * 에 대 한 기본 설정을 유지 **구독**합니다.
   * 에 대 한 **리소스 그룹**새 대시보드를 만들거나 기존 그룹을 사용 합니다.

   > [!NOTE]
   > 테 넌 트 관리자 Azure Resource Manager로 MySQL 인스턴스를 액세스할 수 있는, 경우에 리소스 공급자에 의해 제어 넣을 수 있습니다. 그러나 MySQL 인스턴스에 **해야 합니다** 리소스 공급자에 게 독점적으로 할당 됩니다.

5. 선택 **Sku** 열려는 합니다 **SKU 만들기** 대화 합니다.

   ![MySQL SKU 만들기](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU **이름을** 사용자가 적절 한 SKU를 해당 데이터베이스를 배포할 수 있도록 SKU의 속성을 반영 해야 합니다.

6. 선택 **확인** 는 SKU를 만듭니다.
> [!NOTE]
> Sku는 포털에 표시 되도록 한 시간이 걸릴 수 있습니다. SKU 배포 되어 실행 될 때까지 데이터베이스를 만들 수 없습니다.

7. 아래 **MySQL 호스팅 서버 추가**를 선택 **만들기**합니다.

서버를 추가 하는 대로 서비스 제공을 구분 하기 위해 새 또는 기존 SKU에 할당 합니다. 예를 들어, 증가 된 데이터베이스 및 자동 백업을 제공 하는 MySQL enterprise 인스턴스를 할 수 있습니다. 조직에서 다른 부서에 대 한이 고성능 서버를 예약할 수 있습니다.

## <a name="security-considerations-for-mysql"></a>MySQL에 대 한 보안 고려 사항

다음 정보를 RP 및 MySQL 호스팅 서버에 적용 됩니다.

* TLS 1.2를 사용 하 여 통신에 대 한 모든 호스팅 서버가 구성 되어 있는지 확인 합니다. 참조 [암호화 된 연결을 사용 하는 MySQL 구성](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)합니다.
* 적용할 [투명 한 데이터 암호화](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html)합니다.
* MySQL RP caching_sha2_password 인증을 지원 하지 않습니다.

## <a name="increase-backend-database-capacity"></a>백 엔드 데이터베이스 용량 늘리기

Azure Stack 포털에 더 많은 MySQL 서버를 배포 하 여 백 엔드 데이터베이스 용량을 늘릴 수 있습니다. 기존 또는 새 SKU로 이러한 서버를 추가 합니다. 기존 SKU에는 서버를 추가 하는 경우 서버 특성이 SKU에서 다른 서버와 동일 해야 합니다.

## <a name="sku-notes"></a>SKU 정보
SKU 용량 및 성능과 같은 서버 기능을 설명 하는 SKU 이름을 사용 합니다. 사용자가 적절 한 SKU를 해당 데이터베이스를 배포 하는 데 대 한 지원으로 이름이 사용 됩니다. 예를 들어 다음 특징에 따라 서비스 제공을 구분 하기 위해 SKU 이름을 사용할 수 있습니다.
  
* 큰 용량
* 고성능
* 고가용성

모범 사례로, SKU에서 모든 호스팅 서버가 동일한 리소스 및 성능 특징 있어야 합니다.

Sku는 특정 사용자 또는 그룹에 할당할 수 없습니다.

Sku는 포털에 표시 되도록 한 시간이 걸릴 수 있습니다. 사용자는 SKU를 완벽 하 게 만들 때까지 데이터베이스를 만들 수 없습니다.

SKU를 편집 하려면로 이동 **모든 서비스** > **MySQL 어댑터** > **Sku**합니다. 수정, 필요한 내용을 변경 하 고 클릭 하 고 SKU 선택 **저장** 변경 내용을 저장 합니다. 더 이상 필요 없는 하는 SKU를 삭제 하려면로 이동 **모든 서비스** > **MySQL 어댑터** > **Sku**합니다. 선택한 SKU 이름을 마우스 오른쪽 단추로 클릭 **삭제** 삭제 합니다.

> [!TIP]
> 편집 하거나 동일한 위치에 MySQL 리소스 공급자 할당량을 삭제할 수 있습니다.

## <a name="make-mysql-database-servers-available-to-your-users"></a>사용자에 게 MySQL 데이터베이스 서버를 사용할 수 있도록

계획 및 제품은 사용자를 MySQL 데이터베이스 서버를 사용할 수 있도록 만듭니다. Microsoft.MySqlAdapter 서비스 계획에 추가 하 고 새 할당량을 만듭니다. MySQL은 데이터베이스의 크기를 제한 하는 것을 허용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

[MySQL 데이터베이스 만들기](azure-stack-mysql-resource-provider-databases.md)