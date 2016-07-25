<properties
   pageTitle="Microsoft Power BI Embedded 미리 보기 문제 해결"
   description="Microsoft Power BI Embedded 미리 보기 문제 해결"
   services="power-bi-embedded"
   documentationCenter=""
   authors="minewiskan"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="06/29/2016"
   ms.author="owend"/>

# Microsoft Power BI Embedded 미리 보기 문제 해결
이 문서에서는 **Power BI Embedded** 문제를 해결하는 방법에 대한 답변을 제공합니다.

<a name="connection-string"/>
## SQL Server 연결 문자열 설정
SQL Server 연결 문자열을 설정하려면 특정 형식을 따라야 합니다. SQL Server에 대한 연결 문자열의 예는 아래와 같습니다.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

SQL Server 연결 문자열에 대해 자세히 알아보려면 다음 문서를 참조합니다.

-	[SQL Server 연결 문자열](https://msdn.microsoft.com/library/jj653752.aspx)
-	[SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## 자격 증명 설정
사용자 이름 및 암호와 같은 개발 또는 스테이징 환경에 대한 자격 증명이 있는 경우 프로덕션 솔루션과 일치하는 자격 증명을 업데이트해야 합니다.

## 참고 항목
- [샘플 시작](power-bi-embedded-get-started-sample.md)
- [Power BI Embedded란](power-bi-embedded-what-is-power-bi-embedded.md)

<!---HONumber=AcomDC_0713_2016-->