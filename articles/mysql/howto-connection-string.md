---
title: "MySQL용 Azure Database에 응용 프로그램을 연결하는 방법 | Microsoft Docs"
description: "이 문서에는 MySQL용 Azure Database와 연결하는 응용 프로그램에 대한 모든 현재 지원되는 연결 문자열이 나열되어 있습니다."
services: mysql
author: wuta
ms.author: wuta
editor: jasonh
manager: jhubbard
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b74f146cd0a75437e85dbb1c941b29534ffab2
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017

---

# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>MySQL용 Azure Database에 응용 프로그램을 연결하는 방법
이 문서에는 템플릿 및 예제와 함께 MySQL용 Azure Database에서 지원되는 모든 연결 문자열 형식이 나열되어 있습니다. 연결 문자열에 다른 매개 변수 및 다른 설정을 할 수도 있습니다.

- 인증서를 가져오려면 [SSL을 구성하는 방법](./howto-configure-ssl.md) 문서를 참조하세요.
- {your_host} = <servername>.mysql.database.azure.com

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password};[SslMode=Required;]
```

예를 들어 서버 이름을 **wpdemo**, 데이터베이스 이름을 **wpdb**, 사용자 이름을 **WPAdmin**, 암호를 **orcas!2**로 가정합니다.

그러면 연결 문자열은 다음과 같아야 합니다.

```ado.net
Server= "wpdemo.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@wpdemo"; Pwd="orcas!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {your_username}, {your_password}";
```

## <a name="nodejs"></a>Node.JS
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {your_username}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={your_username};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {your_username}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={your_username}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>루비
```ruby
client = Mysql2::Client.new(username: {your_username}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-connection-string-from-portal"></a>Portal에서 연결 문자열 가져오기
[Azure Portal](https://portal.azure.com)에서 MySQL용 Azure Database로 이동하고 **연결 문자열**을 클릭하여 다음 인스턴스에 대한 문자열 목록을 가져옵니다.![connection strings on portal](./media/howto-connection-strings/connection-strings-on-portal.png)

문자열은 드라이버, 서버 및 다른 데이터베이스 연결 매개 변수와 같은 세부 정보를 제공합니다. 사용자의 요구로 연결 문자열을 복사한 다음, 데이터베이스 이름, 암호 등과 같은 고유한 매개 변수로 수정합니다. 그런 다음 이 문자열을 사용하여 서버에 연결합니다.

## <a name="next-steps"></a>다음 단계
- 연결 라이브러리에 대한 자세한 내용은 [개념 - 연결 라이브러리](./concepts-connection-libraries.md)를 참조하세요.

