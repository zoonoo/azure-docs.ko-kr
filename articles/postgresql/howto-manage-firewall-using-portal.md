---
title: "Azure Portal을 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리 | Microsoft Docs"
description: "Azure Portal을 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 96e917d1ea147e3b53b00002675ed16facb69255
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-the-azure-portal"></a>Azure Portal을 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리
관리자는 서버 수준 방화벽 규칙을 사용하여 특정 IP 주소 또는 IP 주소 범위에서 PostgreSQL용 Azure Database 서버에 액세스할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- 서버 [PostgreSQL용 Azure Database 만들기](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal에서 서버 수준 방화벽 규칙 만들기
1. PostgreSQL 서버 페이지의 설정 머리글에서 **연결 보안**을 클릭하여 Azure Database for PostgreSQL에 대한 연결 보안 페이지를 엽니다.

  ![Azure Portal - 보안 연결 클릭](./media/howto-manage-firewall-using-portal/1-connection-security.png)

2. 도구 모음에서 **내 IP 추가**를 선택합니다. 이 작업은 Azure 시스템에서 감지한 컴퓨터의 공용 IP 주소를 사용하는 방화벽 규칙을 자동으로 만듭니다.

  ![Azure Portal - 내 IP 추가 클릭](./media/howto-manage-firewall-using-portal/2-add-my-ip.png)

3. 구성을 저장하기 전에 사용자의 IP 주소를 확인합니다. 상황에 따라 Azure Portal에서 관찰하는 IP 주소는 인터넷 및 Azure 서버에 액세스할 때 사용된 IP 주소와 다릅니다. 따라서, 규칙 함수를 예상대로 만들기 위해 시작 IP 및 끝 IP를 변경해야 할 수도 있습니다.
검색 엔진 또는 기타 온라인 도구를 사용하여 사용자 고유의 IP 주소를 확인합니다(예를 들어, "내 IP 주소는 무엇입니까" Bing 검색).

  ![내 IP 주소는 무엇입니까에 대한 Bing 검색](./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 추가 주소 범위를 추가합니다. PostgreSQL용 Azure Database에 대한 방화벽 규칙에서 단일 IP 주소 또는 주소 범위를 지정할 수 있습니다. 단일 IP 주소에 규칙을 제한하려는 경우 시작 IP 및 끝 IP에 대한 필드에 동일한 주소를 입력합니다. 방화벽을 열면 관리자, 사용자 및 응용 프로그램이 유효한 자격 증명이 있는 PostgreSQL 서버의 데이터베이스에 로그인할 수 있습니다.

  ![Azure Portal - 방화벽 규칙 ](./media/howto-manage-firewall-using-portal/4-specify-addresses.png)

5. 도구 모음에서 **저장**을 클릭하여 이 서버 수준 방화벽 규칙을 저장합니다. 방화벽 규칙에 대한 업데이트가 성공적으로 수행되었는지 확인될 때까지 기다립니다.

  ![Azure Portal - 저장 클릭](./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png)


## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Azure 포털을 통해 기존 서버 수준 방화벽 규칙 관리
방화벽 규칙을 관리하는 단계를 반복합니다.
* 현재 컴퓨터를 추가하려면 **+ 내 IP 추가** 단추를 클릭합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 추가 IP 주소를 추가 하려면 규칙 이름, 시작 IP 주소 및 끝 IP 주소를 입력 합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 수정 하려면 규칙의 필드 중 하나를 클릭 후 변경 합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 삭제하려면 줄임표 [...]를 클릭하고 **삭제**를 클릭하여 규칙을 제거합니다. **저장**을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계
- 마찬가지로 [Azure 명령줄 인터페이스를 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-cli.md)를 읽을 수 있습니다.
- Azure Database for PostgreSQL 서버 연결에 대한 도움말은 [Azure Database for PostgreSQL에 대한 연결 라이브러리](concepts-connection-libraries.md)를 참조하세요.
