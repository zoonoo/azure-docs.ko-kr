---
title: Azure Database for MySQL 유연한 서버에서 TLS/SSL을 사용 하 여 암호화 된 연결
description: Azure Database for MySQL 유연한 서버에서 TLS/SSL을 사용 하 여 연결 하는 방법에 대 한 지침 및 정보입니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: ce6150cf404f1ca68c93285a2f4a29a6373a55c0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110027"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-over-tls12ssl"></a>TLS 1.2/SSL을 통해 Azure Database for MySQL 유연한 서버에 연결

> [!IMPORTANT]
> Azure Database for MySQL 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Database for MySQL 유연한 서버는 TLS (전송 계층 보안)를 사용 하 여 클라이언트 응용 프로그램을 MySQL 서비스에 연결 하는 것을 지원 합니다 (이전에는 SSL (SSL(Secure Sockets Layer))). TLS는 데이터베이스 서버와 클라이언트 응용 프로그램 간에 암호화 된 네트워크 연결을 보장 하 여 규정 준수 요구 사항을 준수할 수 있는 업계 표준 프로토콜입니다.

Azure Database for MySQL 유연한 서버는 TLS 1.2 (Transport Layer Security)를 사용 하는 암호화 된 연결만 지원 하 고 TLS 1.0 및 TLS 1.1를 사용 하는 모든 들어오는 연결은 거부 됩니다. 모든 유연한 서버에서 TLS 연결의 적용을 사용 하도록 설정 하 고 유연한 서버에 연결 하는 데 TLS/SSL을 사용 하지 않도록 설정할 수 없습니다.

## <a name="download-the-public-ssl-certificate"></a>공용 SSL 인증서 다운로드
Appliations와 함께 사용 하려면 [공용 SSL 인증서](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)를 다운로드 하세요.

인증서 파일을 원하는 위치에 저장 합니다. 예를 들어이 자습서에서는 `c:\ssl` `\var\www\html\bin` 응용 프로그램이 호스팅되는 클라이언트 환경 또는 로컬 환경에서 또는를 사용 합니다. 이렇게 하면 응용 프로그램에서 SSL을 통해 데이터베이스에 안전 하 게 연결할 수 있습니다. 

### <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>TLS/SSL로 mysql 명령줄 클라이언트를 사용 하 여 연결

*프라이빗 액세스(VNet 통합)* 를 사용하여 유연한 서버를 만든 경우 서버와 동일한 VNet 내의 리소스에서 서버에 연결해야 합니다. 가상 머신을 만들고 유연한 서버를 사용하여 만든 VNet에 추가할 수 있습니다.

*퍼블릭 액세스(허용되는 IP 주소)* 를 사용하여 유연한 서버를 만든 경우 서버의 방화벽 규칙 목록에 로컬 IP 주소를 추가할 수 있습니다.

[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 또는 [MySQL 워크 벤치](./connect-workbench.md)를 선택 하 > 로컬 환경에서 서버에 연결할 수 있습니다. 

다음 예에서는 mysql 명령줄 인터페이스를 사용 하 여 서버에 연결 하는 방법을 보여 줍니다. `--ssl-mode=REQUIRED`연결 문자열 설정을 사용 하 여 TLS/SSL 인증서 확인을 적용 합니다. 로컬 인증서 파일 경로를 `--ssl-ca` 매개 변수로 전달 합니다. 값을 실제 서버 이름 및 암호로 바꿉니다. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> 에 전달 된 값이 `--ssl-ca` 저장 한 인증서의 파일 경로와 일치 하는지 확인 합니다.

### <a name="verify-the-tlsssl-connection"></a>TLS/SSL 연결 확인

Mysql **상태** 명령을 실행 하 여 TLS/SSL을 사용 하 여 mysql 서버에 연결 했는지 확인 합니다.

```dos
mysql> status
```
다음을 표시 해야 하는 출력을 검토 하 여 연결이 암호화 되었는지 확인 합니다.  **SSL: Cipher in use가 AES256** 입니다. 이 암호 그룹은 예를 표시 하 고 클라이언트를 기반으로 다른 암호 그룹을 볼 수 있습니다.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>응용 프로그램 또는 프레임 워크에서 TLS 연결을 지원 하는지 확인

데이터베이스 서비스에 MySQL을 사용 하는 일부 응용 프로그램 프레임 워크는 설치 하는 동안 기본적으로 TLS를 사용 하도록 설정 하지 않습니다. MySQL 서버는 TLS 연결을 적용 하지만 응용 프로그램이 TLS로 구성 되지 않은 경우 응용 프로그램이 데이터베이스 서버에 연결 하지 못할 수 있습니다. 응용 프로그램의 설명서를 참조 하 여 TLS 연결을 사용 하도록 설정 하는 방법을 알아보세요.

## <a name="sample-code"></a>샘플 코드
Azure Portal 서버에 사용할 수 있는 "연결 문자열" 페이지에서 미리 정의 된 연결 문자열에는 TLS/SSL을 사용 하 여 데이터베이스 서버에 연결 하는 데 사용할 수 있는 공용 언어의 필수 매개 변수가 포함 되어 있습니다. TLS/SSL 매개 변수는 커넥터에 따라 달라 집니다. 예: "useSSL = true", "sslmode = required" 또는 "ssl_verify_cert = true" 및 기타 변형.

응용 프로그램에서 TLS/SSL을 통해 유연한 서버에 암호화 된 연결을 설정 하려면 다음 코드 샘플을 참조 하세요.

### <a name="wordpress"></a>WordPress
[SSL 공용 인증서](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) 를 다운로드 하 고 줄 뒤에 wp-config.php에 다음 줄을 추가 ```// ** MySQL settings - You can get this info from your web host ** //``` 합니다.

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (PDO 사용)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python(MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python(PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (Java 용 MySQL 커넥터)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (Java 용 MariaDB 커넥터)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET(MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>다음 단계
- [MySQL 워크 벤치를 사용 하 여 Azure Database for MySQL 유연한 서버에서 데이터 연결 및 쿼리](./connect-workbench.md)
- [PHP를 사용 하 여 Azure Database for MySQL 유연한 서버에서 데이터 연결 및 쿼리](./connect-php.md)
- [Azure CLI를 사용 하 여 Azure Database for MySQL 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
- [유연한 서버 Azure Database for MySQL의 네트워킹](./concepts-networking.md) 에 대 한 자세한 정보
- [Azure Database for MySQL 유연한 서버 방화벽 규칙](./concepts-networking.md#public-access-allowed-ip-addresses) 에 대해 자세히 알아보기
