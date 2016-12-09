---
title: "Microsoft Power BI Embedded 미리 보기 문제 해결"
description: "Microsoft Power BI Embedded 미리 보기 문제 해결"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: c8aee652-ed8b-4c66-9c63-f798b7a655b4
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 10/04/2016
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f4cf4a04e7a921f8e11c94f74d53ac80c231933


---
# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Microsoft Power BI Embedded 미리 보기 문제 해결
이 문서에서는 **Power BI Embedded** 문제를 해결하는 방법에 대한 답변을 제공합니다.

<a name="connection-string"/>

## <a name="setting-sql-server-connection-strings"></a>SQL Server 연결 문자열 설정
SQL Server 연결 문자열을 설정하려면 특정 형식을 따라야 합니다. SQL Server에 대한 연결 문자열의 예는 아래와 같습니다.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

SQL Server 연결 문자열에 대해 자세히 알아보려면 다음 문서를 참조합니다.

* [SQL Server 연결 문자열](https://msdn.microsoft.com/library/jj653752.aspx)
* [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>

## <a name="setting-credentials"></a>자격 증명 설정
사용자 이름 및 암호와 같은 개발 또는 스테이징 환경에 대한 자격 증명이 있는 경우 프로덕션 솔루션과 일치하는 자격 증명을 업데이트해야 합니다.

## <a name="see-also"></a>참고 항목
* [샘플 시작](power-bi-embedded-get-started-sample.md)
* [Power BI Embedded란](power-bi-embedded-what-is-power-bi-embedded.md)




<!--HONumber=Nov16_HO3-->


