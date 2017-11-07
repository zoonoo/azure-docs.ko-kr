## <a name="install-wordpress"></a>WordPress 설치

스택을 사용하려는 경우 샘플 앱을 설치합니다. 예를 들어, 다음 단계는 오픈 소스 [WordPress](https://wordpress.org/) 플랫폼을 설치하여 웹 사이트 및 블로그를 만듭니다. [Drupal](http://www.drupal.org) 및 [Moodle](https://moodle.org/)을 포함하려는 다른 워크로드입니다. 

이 WordPress 설정은 개념 증명을 위한 것입니다. 프로덕션 설치에 대한 자세한 내용 및 설정은 [WordPress 설명서](https://codex.wordpress.org/Main_Page)를 참조하세요. 



### <a name="install-the-wordpress-package"></a>WordPress 패키지 설치

다음 명령을 실행합니다.

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>WordPress 구성

MySQL 및 PHP를 사용하도록 WordPress를 구성합니다. 다음 명령을 실행하여 원하는 텍스트 편집기를 열고 `/etc/wordpress/config-localhost.php` 파일을 만듭니다.

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
파일에 다음 줄을 복사하여 *yourPassword*에 대한 데이터베이스 암호를 대체합니다(다른 값을 변경하지 않고 둠). 그런 다음 파일을 저장합니다.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```

작업 디렉터리에서 `wordpress.sql` 텍스트 파일을 만들어 WordPress 데이터베이스를 구성합니다. 

```bash
sudo sensible-editor wordpress.sql
```

다음 명령을 추가하여 *yourPassword*에 대한 데이터베이스 암호를 대체합니다(다른 값을 변경하지 않고 둠). 그런 다음 파일을 저장합니다.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```


다음 명령을 실행하여 데이터베이스를 만듭니다.

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

명령이 완료된 후 `wordpress.sql` 파일을 삭제합니다.

웹 서버 문서 루트로 WordPress 설치를 이동합니다.

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

이제 WordPress 설정을 완료하고 플랫폼에 게시할 수 있습니다. 웹 브라우저를 열고 `http://yourPublicIPAddress/wordpress`로 이동합니다. VM의 공용 IP 주소를 대체합니다. 이 이미지와 유사하게 표시됩니다.

![WordPress 설치 페이지](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)