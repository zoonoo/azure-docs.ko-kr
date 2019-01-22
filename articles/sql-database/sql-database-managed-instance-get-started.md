---
title: 'Azure Portal: SQL Managed Instance 만들기 | Microsoft Docs'
description: SQL Managed Instance, 네트워크 환경 및 액세스용 클라이언트 VM을 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: Carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 201ba431a4382741815536db2bb4d08f0068be80
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329544"
---
# <a name="quickstart-create-an-azure-sql-database-managed-instance"></a>빠른 시작: Azure SQL Database Managed Instance 만들기

이 빠른 시작에서는 Azure Portal에서 Azure SQL Database [Managed Instance](sql-database-managed-instance.md)를 만드는 방법을 안내합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-managed-instance"></a>Managed Instance 만들기

다음 단계는 Managed Instance를 만드는 방법을 보여줍니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기**를 선택합니다.
2. **Managed Instance**를 찾은 다음, **Azure SQL Managed Instance**를 선택합니다.
3. **만들기**를 선택합니다.

   ![관리되는 인스턴스 만들기](./media/sql-database-managed-instance-get-started/managed-instance-create.png)

4. 다음 표의 정보를 사용하여 요청된 정보가 포함된 **Managed Instance** 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ------ | --------------- | ----------- |
   | **구독** | 사용자의 구독 | 새 리소스를 만들 권한이 있는 구독 |
   |**관리되는 인스턴스 이름**|모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**Managed Instance 관리자 로그인**|모든 유효한 사용자 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. "serveradmin"을 예약된 서버 수준 역할로 사용하지 않습니다.|
   |**암호**|유효한 암호|암호는 16자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
   |**데이터 정렬**|Managed Instance에 사용할 데이터 정렬|데이터 정렬에 대한 자세한 내용은 [데이터 정렬](https://docs.microsoft.com/sql/t-sql/statements/collations)을 참조하세요.|
   |**위치**:|Managed Instance를 만들 위치|지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.|
   |**가상 네트워크**|**새 가상 네트워크 만들기** 또는 유효한 가상 네트워크 및 서브넷을 선택합니다.| 네트워크/서브넷이 회색으로 표시되는 경우 새 Managed Instance의 대상으로 선택하기 전에 [네트워크 요구 사항을 충족하도록 수정](sql-database-managed-instance-configure-vnet-subnet.md)해야 합니다. Managed Instance의 네트워크 환경을 구성하기 위한 요구 사항 관련 정보는 [Azure SQL Database Managed Instance에 대한 VNet 구성](sql-database-managed-instance-connectivity-architecture.md)을 참조하세요. |
   |**리소스 그룹**|새 리소스 그룹 또는 기존 리소스 그룹|유효한 리소스 그룹 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|

   ![관리되는 인스턴스 양식](./media/sql-database-managed-instance-get-started/managed-instance-create-form.png)

5. Managed Instance를 보조 인스턴스 장애 조치 그룹으로 사용하려면 체크 아웃 확인란을 선택하고 DnsAzurePartner 관리되는 인스턴스를 지정합니다. 이 기능은 미리 보기 상태로 제공되며 해당 스크린샷에 표시되지 않습니다.
6. **가격 책정 계층**을 선택하여 계산 및 스토리지 리소스의 크기를 조정하고 가격 책정 옵션을 검토합니다. 메모리 32GB와 Vcore 16개를 제공하는 범용 가격 책정 계층이 기본값입니다.
7. 슬라이더 또는 텍스트 상자를 사용하여 저장소 공간 및 가상 코어 수를 지정합니다.
8. 완료되면 **적용**을 선택하여 선택 사항을 저장합니다.  
9. **만들기**를 선택하여 Managed Instance를 배포합니다.
10. **알림** 아이콘을 선택하여 배포 상태를 확인합니다.

    ![관리되는 인스턴스 배포 진행률](./media/sql-database-managed-instance-get-started/deployment-progress.png)

11. **배포 진행 중**을 선택하여 Managed Instance 창을 열어 배포 진행 상황을 자세히 모니터링합니다.

> [!IMPORTANT]
> 서브넷의 첫 번째 인스턴스에서는 배포 시간이 일반적으로 후속 인스턴스의 경우보다 훨씬 더 깁니다. 배포 작업이 예상보다 오래 지속되므로 취소하지 마세요. 서브넷에 두 번째 Managed Instance를 만드는 작업은 몇 분밖에 안 걸립니다.

## <a name="review-resources-and-retrieve-your-fully-qualified-server-name"></a>리소스를 검토하고 정규화된 서버 이름 검색

배포가 성공적으로 완료된 후에는 만든 리소스를 검토하고 이후에 빠른 시작에서 사용할 정규화된 서버 이름을 검색합니다.

1. Managed Instance의 리소스 그룹을 열고 [Managed Instance 만들기](#create-a-managed-instance) 빠른 시작에서 자동으로 생성된 리소스를 살펴봅니다.

2. Managed Instance를 선택합니다.

   ![Managed Instance 리소스](./media/sql-database-managed-instance-get-started/resources.png)

3. **개요** 탭에서 **Host** 속성을 찾아서 Managed Instance의 정규화된 호스트 주소를 복사합니다.

   ![Managed Instance 리소스](./media/sql-database-managed-instance-get-started/host-name.png)

   이름은 **your_machine_name.a1b2c3d4e5f6.database.windows.net**과 유사합니다.

## <a name="next-steps"></a>다음 단계

- Managed Instance에 연결하는 방법을 알아보려면 다음 항목을 참조하세요.
  - 애플리케이션의 연결 옵션 개요는 [애플리케이션을 Managed Instance에 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.
  - Azure 가상 머신에서 Managed Instance에 연결하는 방법을 보여주는 빠른 시작은 [Azure 가상 머신 연결 구성](sql-database-managed-instance-configure-vm.md)을 참조하세요.
  - 온-프레미스 클라이언트 컴퓨터에서 지점 및 사이트 간 연결을 사용하여 Managed Instance에 연결하는 방법을 보여주는 빠른 시작은 [지점 및 사이트 간 연결 구성](sql-database-managed-instance-configure-p2s.md)을 참조하세요.
- 온-프레미스의 기존 SQL Server 데이터베이스를 Managed Instance로 복원하려면 [마이그레이션용 Azure DMS(Database Migration Service)](../dms/tutorial-sql-server-to-managed-instance.md)를 사용하여 데이터베이스 백업 파일에서 복원하거나 [T-SQL RESTORE 명령](sql-database-managed-instance-get-started-restore.md)을 사용하여 데이터베이스 백업 파일에서 복원하면 됩니다.
- 기본 제공 문제 해결 인텔리전스를 사용하는 Managed Instance 데이터베이스 성능의 고급 모니터링에 대해 자세히 알아보려면 [Azure SQL 분석을 사용하여 Azure SQL Database 모니터링](../azure-monitor/insights/azure-sql.md)을 참조하세요.
