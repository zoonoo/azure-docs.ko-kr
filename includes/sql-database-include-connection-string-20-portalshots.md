---
title: Azure 포털에서 연결 문자열 가져오기
description: Azure 포털에서 연결 문자열 가져오기
keywords: SQL 연결,연결 문자열
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: dab7623c86bea4e562313e618f238b9b33c0fdc5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117293"
---
### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Azure 포털에서 연결 문자열 가져오기
[Azure Portal](https://portal.azure.com/)에서 클라이언트 프로그램이 Azure SQL Database와 상호 작용하는 데 필요한 연결 문자열을 가져올 수 있습니다.

1. **모든 서비스** > **SQL 데이터베이스**를 클릭합니다.

2. **SQL 데이터베이스** 블레이드 왼쪽 위에 있는 필터 텍스트 상자에 데이터베이스 이름을 입력합니다.

3. 해당 데이터베이스에 대한 행을 선택합니다.

4. 데이터베이스에 대한 블레이드가 표시된 후 시각적 편의를 위해 **최소화** 단추를 선택하여 검색 및 데이터베이스 필터링에 사용한 블레이드를 축소합니다.

5. 데이터베이스 블레이드에서 **데이터베이스 연결 문자열 표시**를 선택합니다.

6. 적절한 연결 문자열을 복사합니다. 즉, ADO.NET 연결 라이브러리를 사용하려는 경우, **ADO.NET** 탭에서 적절한 문자열을 복사합니다.

    ![데이터베이스에 대한 ADO 연결 문자열 복사][20-CopyAdoConnectionString]

7. 필요에 따라 연결 문자열을 편집합니다. 즉, 사용자 이름 또는 서버 이름이 너무 긴 경우, 사용자 이름에서 “@&lt;servername&gt;”을 제거하거나 연결 문자열에 암호를 삽입합니다.

8. 한 가지 형식이나 다른 형식으로 연결 문자열 정보를 클라이언트 프로그램 코드에 붙여넣습니다.

자세한 내용은 [연결 문자열 및 구성 파일](http://msdn.microsoft.com/library/ms254494.aspx)을 참조하세요.

<!-- Image references. -->



[20-CopyAdoConnectionString]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
