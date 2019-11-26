---
title: 리디렉션을 사용 하 여 Azure Database for MySQL에 연결
description: 이 문서에서는 리디렉션을 사용 하 여 Azure Database for MySQL에 연결 하도록 응용 프로그램을 구성 하는 방법을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233741"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>리디렉션을 사용 하 여 Azure Database for MySQL에 연결

이 항목에서는 리디렉션 모드를 사용 하 여 Azure Database for MySQL 서버에 응용 프로그램을 연결 하는 방법을 설명 합니다. 리디렉션은 응용 프로그램이 백 엔드 서버 노드에 직접 연결할 수 있도록 하 여 클라이언트 응용 프로그램과 MySQL 서버 간의 네트워크 대기 시간을 줄이는 것을 목표로 합니다.

> [!IMPORTANT]
> PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 의 리디렉션에 대 한 지원은 현재 미리 보기 상태입니다.

## <a name="before-you-begin"></a>시작하기 전에
[Azure Portal](https://portal.azure.com)에 로그인합니다. 엔진 버전 5.6, 5.7 또는 8.0을 사용 하 여 Azure Database for MySQL 서버를 만듭니다. 자세한 내용은 [포털에서 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-portal.md) 또는 [CLI를 사용하여 Azure Database for MySQL 서버 만들기](quickstart-create-mysql-server-database-using-azure-cli.md)를 참조하세요.

현재는 SSL을 사용 하는 경우에만 리디렉션이 지원 됩니다. SSL을 구성하는 방법에 대한 자세한 내용은 [Azure Database for MySQL에 SSL 사용](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure)을 참조하세요. 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>선행 조건 
- PHP 버전 7.2.15 + and 7.3.2 +
- PHP 배 
- php-mysql
- SSL을 사용 하는 Azure Database for MySQL 서버

1. [PECL](https://pecl.php.net/package/mysqlnd_azure)를 사용 하 여 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) 를 설치 합니다.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. 다음을 실행 하 여 확장 디렉터리 (`extension_dir`)를 찾습니다.

    ```bash
    php -i | grep "extension_dir"
    ```

3. 반환 된 폴더로 디렉터리를 변경 하 고 `mysqlnd_azure.so`이 폴더에 있는지 확인 합니다. 

4. 다음을 실행 하 여 .ini 파일의 폴더를 찾습니다. 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. 이 반환 된 폴더로 디렉터리를 변경 합니다. 

6. `mysqlnd_azure`에 대 한 새 .ini 파일을 만듭니다. 모듈이 ini 파일의 이름 순서에 따라 로드 되므로 이름의 사전순은 mysqnld의 뒤에 있어야 합니다. 예를 들어 `mysqlnd` .ini 이름이 `10-mysqlnd.ini`인 경우 mysqlnd ini 이름을 `20-mysqlnd-azure.ini`로 지정 합니다.

7. 새 .ini 파일 내에서 리디렉션을 사용 하도록 설정 하려면 다음 줄을 추가 합니다.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>선행 조건 
- PHP 버전 7.2.15 + and 7.3.2 +
- php-mysql
- SSL을 사용 하는 Azure Database for MySQL 서버

1. 다음 명령을 실행 하 여 x64 또는 x86 버전의 PHP를 실행 하 고 있는지 확인 합니다.

    ```cmd
    php -i | findstr "Thread"
    ```

2. 해당 버전의 PHP와 일치 하는 [PECL](https://pecl.php.net/package/mysqlnd_azure) 에서 해당 하는 x64 또는 x86 버전의 [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL을 다운로드 합니다. 

3. Zip 파일을 추출 하 고 이름이 `php_mysqlnd_azure.dll`인 DLL을 찾습니다.

4. 아래 명령을 실행 하 여 확장 디렉터리 (`extension_dir`)를 찾습니다.

    ```cmd
    php -i | find "extension_dir"s
    ```

5. `php_mysqlnd_azure.dll` 파일을 4 단계에서 반환 된 디렉터리에 복사 합니다. 

6. 다음 명령을 사용 하 여 `php.ini` 파일을 포함 하는 PHP 폴더를 찾습니다.

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. `php.ini` 파일을 수정 하 고 다음 추가 줄을 추가 하 여 리디렉션을 사용 하도록 설정 합니다. 

    동적 확장 섹션에서 다음을 수행 합니다. 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    모듈 설정 섹션에서 다음을 수행 합니다.     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>리디렉션 확인

다음 샘플 PHP 코드를 사용 하 여 리디렉션이 구성 되었는지 확인할 수도 있습니다. `mysqlConnect.php` 이라는 PHP 파일을 만들고 아래 코드를 붙여 넣습니다. 서버 이름, 사용자 이름 및 암호를 사용자 고유의 이름으로 업데이트 합니다. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>다음 단계
연결 문자열에 대한 자세한 내용은 [연결 문자열](howto-connection-string.md)을 참조하세요.

