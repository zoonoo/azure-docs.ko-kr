---
title: "MySQL용 Azure 데이터베이스에 대한 SSL 연결 | Microsoft Docs"
description: "SSL 연결을 올바르게 사용하도록 MySQL용 Azure 데이터베이스 및 연결된 응용 프로그램을 구성하는 방법에 대한 정보"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: 4b03b3a2dbfad92cc0cfa84777b38ddff90452cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>MySQL용 Azure 데이터베이스의 SSL 연결
MySQL용 Azure 데이터베이스는 SSL(Secure Sockets Layer)을 사용하여 데이터베이스 서버를 클라이언트 응용 프로그램에 연결하도록 지원합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간 SSL 연결을 적용하면 서버와 응용 프로그램 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

## <a name="default-settings"></a>기본 설정
기본적으로 데이터베이스 서비스는 MySQL에 연결할 때 SSL 연결이 필요하도록 구성되어야 합니다.  가능하면 SSL 옵션을 해제하지 않는 것이 좋습니다. 

Azure Portal 및 CLI를 통해 새로운 MySQL용 Azure 데이터베이스 서버를 프로비전할 때 SSL 연결 적용이 기본적으로 활성화됩니다. 

마찬가지로, Azure Portal의 해당 서버에 있는 “연결 문자열” 설정에서 미리 정의된 연결 문자열에는 SSL을 사용하여 데이터베이스 서버에 연결하기 위한 일반적인 언어에 대한 필수 매개 변수가 포함되어 있습니다. SSL 매개 변수는 커넥터에 따라 달라집니다. 예를 들어 "ssl = true", "sslmode=require" 또는 "sslmode=required"이거나 다른 변형이 사용될 수 있습니다.

응용 프로그램을 개발할 때 SSL 연결을 사용하거나 사용하지 않도록 설정하는 방법을 알아보려면 [SSL 구성 방법](howto-configure-ssl.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[MySQL용 Azure 데이터베이스에 대한 연결 라이브러리](concepts-connection-libraries.md)
