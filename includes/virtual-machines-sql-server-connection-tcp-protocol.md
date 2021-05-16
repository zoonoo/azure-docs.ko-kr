---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: f7af6962c3343cd9d3e35e96d88650aa6a42c6b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95555871"
---
1. 원격 데스크톱으로 가상 컴퓨터에 연결되어 있는 동안 **구성 관리자** 를 검색합니다.

    ![SSCM 열기](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. SQL Server 구성 관리자의 콘솔 창에서 **SQL Server 네트워크 구성** 을 확장합니다.

1. 콘솔 창에서 **MSSQLSERVER용 프로토콜**(기본 인스턴스 이름)을 클릭합니다. 세부 정보 창에서 **TCP** 를 마우스 오른쪽 단추로 클릭하고 아직 사용하도록 설정되어 있지 않은 경우 **사용** 을 클릭합니다.

    ![TCP 사용](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. 콘솔 창에서 **SQL Server 서비스** 를 클릭합니다. 세부 정보 창에서 **SQL Server(*인스턴스 이름*)**(기본 인스턴스는 **SQL Server(MSSQLSERVER)**)를 마우스 오른쪽 단추로 클릭한 후 **다시 시작** 을 클릭하여 SQL Server의 인스턴스를 중지했다가 다시 시작합니다.

    ![데이터베이스 엔진 다시 시작](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. SQL Server 구성 관리자를 닫습니다.

SQL Server 데이터베이스 엔진용 프로토콜 사용 설정에 대한 자세한 내용은 [서버 네트워크 프로토콜 설정 또는 해제](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol)를 참조하십시오.