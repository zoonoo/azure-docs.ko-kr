---
title: "MySQL용 Azure Database에 안전하게 연결하기 위한 SSL 연결 구성 | Microsoft Docs"
description: "SSL 연결을 올바르게 사용하기 위해 MySQL용 Azure Database 및 연결된 응용 프로그램을 올바르게 구성하는 방법에 대한 지침"
services: mysql
author: seanli1988
ms.author: seanli
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 38e68712699b3e89a10c3d44d8ec313f531fcbdc
ms.contentlocale: ko-kr
ms.lasthandoff: 09/15/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>MySQL용 Azure Database에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성
MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 응용 프로그램 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 응용 프로그램 간 SSL 연결을 적용하면 서버와 응용 프로그램 간 데이터 스트림을 암호화함으로써 “메시지 가로채기(man in the middle)” 공격으로부터 보호할 수 있습니다.

## <a name="step-1-obtain-ssl-certificate"></a>1단계: SSL 인증서 받기
[https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)에서 SSL을 통해 MySQL 서버용 Azure Database와 통신하는 데 필요한 인증서를 다운로드하고 인증서 파일을 로컬 드라이브에 저장합니다(이 자습서에서는 c:\ssl 사용).
**Microsoft Internet Explorer 및 Microsoft Edge:** 다운로드가 완료된 후 인증서 이름을 BaltimoreCyberTrustRoot.crt.pem으로 변경합니다.

## <a name="step-2-bind-ssl"></a>2단계: SSL 바인딩
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>SSL로 MySQL 워크벤치를 사용하는 서버에 연결
SSL을 통해 안전하게 연결하도록 MySQL Workbench를 구성합니다. 새 연결 설정 대화 상자의 MySQL Workbench에서 **SSL** 탭으로 이동합니다. **SSL CA 파일:** 필드에 **BaltimoreCyberTrustRoot.crt.pem**의 파일 위치를 입력합니다.
![사용자 지정된 타일 저장](./media/howto-configure-ssl/mysql-workbench-ssl.png)

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>SSL로 MySQL CLI를 사용하는 서버에 연결
MySQL 명령줄 인터페이스를 사용하여 다음 명령을 실행합니다.
```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -u Username@mysqlserver4demo -p --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>3단계: Azure에 SSL 연결 적용 
### <a name="using-the-azure-portal"></a>Azure Portal 사용
Azure Portal을 사용하여 MySQL용 Azure Database 서버를 방문한 다음 **연결 보안**을 클릭합니다. 설정/해제 단추를 사용하여 **SSL 연결 적용** 설정을 사용하거나 사용하지 않도록 설정한 다음 **저장**을 클릭합니다. Microsoft는 향상된 보안을 위해 항상 **SSL 연결 적용**을 활성화하는 것을 권장합니다.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI 사용
Azure CLI에서 Enabled 또는 Disabled 값을 각각 사용하여 **ssl-enforcement** 매개 변수를 사용하거나 사용하지 않도록 설정할 수 있습니다.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>4단계: SSL 연결 확인
mysql **status** 명령을 실행하여 SSL로 MySQL 서버에 연결되어 있는지 확인합니다.
```dos
mysql> status
```
출력을 검토하여 연결이 암호화되었는지 확인합니다. **SSL: 사용 중인 암호 그룹은 AES256-SHA**를 표시해야 합니다. 

## <a name="sample-code"></a>샘플 코드
### <a name="php"></a>PHP
```
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'myserver4demo.mysql.database.azure.com', 'myadmin@myserver4demo', 'yourpassword', 'quickstartdb', 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python"></a>파이썬
```
try:
conn = mysql.connector.connect(user='myadmin@myserver4demo',password='yourpassword',database='quickstartdb',host='myserver4demo.mysql.database.azure.com',ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
 print(err)
```

## <a name="next-steps"></a>다음 단계
[MySQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)에 이어지는 다양한 응용 프로그램 연결 옵션 검토

