---
title: MySQL 호스팅 서버에 Azure 스택 | Microsoft Docs
description: MySQL 어댑터 리소스 공급자를 통해 프로 비전에 대 한 MySQL 인스턴스를 추가 하는 방법
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 5522eb1b8b0398aeb6f1b0dd8578b906880b4e89
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938552"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>MySQL 리소스 공급자에 대 한 호스팅 서버를 추가 합니다.

가상 컴퓨터 (VM)에서 MySQL 인스턴스를 호스트할 [Azure 스택](azure-stack-poc.md), 또는 MySQL 리소스 공급자의 인스턴스에 연결할 수 만큼 Azure 스택 환경 외부 VM에서 합니다.

## <a name="connect-to-a-mysql-hosting-server"></a>MySQL 호스팅 서버에 연결

시스템 관리자 권한이 있는 계정의 자격 증명을가지고 있는지 확인 합니다. 호스팅 서버를 추가 하려면 다음이 단계를 수행 합니다.

1. 서비스 관리자로 Azure 스택 연산자 포털에 로그인
2. **추가 서비스**를 선택합니다.
3. 선택 **관리 리소스** > **MySQL 호스팅 서버** > **+ 추가**합니다. 열립니다는 **MySQL 호스팅 서버 추가** 대화 상자에서 다음 화면 캡처에 표시 된 것입니다.

   ![호스팅 서버를 구성 합니다.](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. MySQL Server 인스턴스의 연결 세부 정보를 제공 합니다.

   * 에 대 한 **MySQL 호스팅 서버 이름**, 유효한 IPv4 주소 또는 정규화 된 도메인 이름 (FQDN)을 제공 합니다. 짧은 VM 이름을 사용 하지 마십시오.
   * 기본 MySQL 인스턴스를 지정 해야 하므로 제공 되지 않는 **크기의 호스팅 서버 gb에서**합니다. 데이터베이스 서버 용량에 근접 하는 크기를 입력 합니다.
   * 에 대 한 기본 설정을 유지 **구독**합니다.
   * 에 대 한 **리소스 그룹**새 대시보드를 만들거나 기존 그룹을 사용 합니다.

   > [!NOTE]
   > 테 넌 트 및 Azure 리소스 관리자 관리자에서 MySQL 인스턴스를 액세스할 수 있는, 경우에 리소스 공급자에 의해 제어 넣을 수 있습니다. 하지만, MySQL 인스턴스 **해야** 리소스 공급자에만 할당할 수 있습니다.

5. 선택 **Sku** 열려는 **만들 SKU** 대화 상자.

   ![MySQL SKU 만들기](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU **이름** 사용자가 적절 한 SKU에 데이터베이스를 배포할 수 있도록 SKU의 속성을 반영 해야 합니다.

   >[!IMPORTANT]
   >특수 문자, 공백 및 마침표를 포함 하 여에서 지원 되지 않습니다 **이름** 또는 **계층** MySQL 리소스 공급자에 대 한 SKU를 만들 때.

6. 선택 **확인** SKU를 만들려고 합니다.
7. 아래 **MySQL 호스팅 서버 추가**선택, **만들기**합니다.

서버를 추가 하면 서비스 제공을 구분 하는 새로운 또는 기존 SKU에 할당 합니다. 예를 들어, 데이터베이스 증가 및 자동 백업을 제공 하는 MySQL enterprise 인스턴스를 사용할 수 있습니다. 조직에서 다른 부서에 대 한이 고성능 서버를 예약할 수 있습니다.

## <a name="increase-backend-database-capacity"></a>백 엔드 데이터베이스 용량 늘리기

Azure 스택 포털에 더 많은 MySQL 서버를 배포 하 여 백 엔드 데이터베이스 용량을 늘릴 수 있습니다. 이러한 서버를 기존 또는 새 SKU를 추가 합니다. 기존 SKU로 서버를 추가 하는 경우 서버 특성 SKU에서 다른 서버와 동일한 지 확인 합니다.

## <a name="make-mysql-database-servers-available-to-your-users"></a>사용자가을 MySQL 데이터베이스 서버를 사용할 수 있도록

계획 및 사용자에 게 MySQL 데이터베이스 서버 제안을 만듭니다. Microsoft.MySqlAdapter 서비스 계획에 추가 다음 기본 할당량을 추가 하거나 새 할당량 만들기.

![계획 및 데이터베이스에 대 한 제안 만들기](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="next-steps"></a>다음 단계

[MySQL 데이터베이스 만들기](azure-stack-mysql-resource-provider-databases.md)