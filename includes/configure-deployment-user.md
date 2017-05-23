## <a name="configure-a-deployment-user"></a>배포 사용자 구성  

FTP 및 로컬 Git의 경우 배포에 인증하기 위해 배포 사용자를 서버에 구성해야 합니다.

> [!NOTE]
> 배포 사용자는 Web App에 대한 FTP 및 로컬 Git 배포가 필요합니다.
> `username` 및 `password`는 계정 수준입니다. 따라서 Azure 구독 자격 증명과 다릅니다.
>

[az appservice web deployment user set](/cli/azure/appservice/web/deployment/user#set) 명령을 실행하여 배포 자격 증명을 만듭니다.

```azurecli
az appservice web deployment user set --user-name <username> --password <password>
```

사용자 이름은 고유해야 하고 암호는 강력해야 합니다. ` 'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. ` 'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다.

이 배포 사용자는 한 번만 만들어야 하며 모든 Azure 배포에서 사용할 수 있습니다.

이후 단계에서 앱을 배포할 때 사용하므로 사용자 이름 및 암호를 기록해 둡니다.