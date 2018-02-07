## <a name="rest"></a>REST API를 사용하여 배포 
 
[배포 서비스 REST API](https://github.com/projectkudu/kudu/wiki/REST-API)를 사용하여 .zip 파일을 Azure의 앱에 배포할 수 있습니다. POST 요청을 https://<app_name>.scm.azurewebsites.net/api/zipdeploy에 전송하면 됩니다. POST 요청은 메시지 본문에 .zip 파일을 포함해야 합니다. 앱에 대한 배포 자격 증명은 HTTP 기본 인증을 사용하여 요청으로 제공됩니다. 자세한 내용은 [.zip 푸시 배포 참조](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)를 참조하세요. 

다음 예제에서는 cURL 도구를 사용하여 .zip 파일을 포함하는 요청을 보냅니다. Mac 또는 Linux 컴퓨터의 터미널에서 cURL을 실행하거나 Windows에서 Bash를 사용하여 실행할 수 있습니다. `<zip_file_path>` 자리 표시자를 프로젝트 .zip 파일의 위치로 바꿉니다. 또한 `<app_name>`을 앱의 고유 이름으로 바꿉니다.

`<deployment_user>` 자리 표시자를 배포 자격 증명의 사용자 이름으로 바꿉니다. cURL에서 프롬프트가 표시되면 암호를 입력합니다. 앱에 대한 배포 자격 증명을 설정하는 방법을 알아보려면 [사용자 수준 자격 증명 설정 및 다시 설정](../articles/app-service/app-service-deployment-credentials.md#userscope)을 참조하세요.   

```bash
curl POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

이 요청은 업로드된 .zip 파일에서 푸시 배포를 트리거합니다. 다음 cURL 예제에 표시된 대로 https://<app_name>.scm.azurewebsites.net/api/deployments 끝점을 사용하여 현재 및 과거 배포를 검토할 수 있습니다. 다시 `<app_name>`을 앱의 이름으로 바꾸고 `<deployment_user>`를 배포 자격 증명의 사용자 이름으로 바꿉니다.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```