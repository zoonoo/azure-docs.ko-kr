Cloud Shell에서 [az webapp deployment user set](/cli/azure/webapp/deployment/user#set) 명령을 사용하여 배포 자격 증명을 만듭니다.

배포 사용자는 웹앱에 대한 FTP 및 로컬 Git 배포에 필요합니다. 사용자 이름과 암호는 계정 수준이며, _Azure 구독 자격 증명과 다릅니다._

다음 명령에서 *\<사용자 이름>* 및 *\<암호>*를 새 사용자 이름 및 암호로 바꿉니다. 사용자 이름은 고유해야 합니다. 암호는 글자, 숫자, 기호와 같은 세 가지 요소 중 두 가지를 포함하여 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

` 'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. ` 'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다.

이 배포 사용자는 한 번만 만들며, 모든 Azure 배포에 사용할 수 있습니다.

> [!NOTE]
> 사용자 이름과 암호는 나중에 웹앱을 배포하는 데 필요하므로 기록해 둡니다.
>
>