### 작성기를 통해 설치
1. [Git를 설치][install-git]합니다. Windows에서는 PATH 환경 변수에도 Git 실행 파일을 추가해야 합니다. 
2. 프로젝트 루트에 **composer.json**이라는 파일을 만들고 다음 코드를 추가합니다.
   
    ```
    {
      "require": {
        "microsoft/windowsazure": "^0.4"
      }
    }
    ```
3. 프로젝트 루트에 **[composer.phar][composer-phar]**을 다운로드합니다.
4. 명령 프롬프트를 열고 프로젝트 루트에서 다음 명령을 실행합니다.
   
    ```
    php composer.phar install
    ```

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar

<!---HONumber=AcomDC_0601_2016-->