## <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 Git 리포지토리에 Azure 원격을 추가합니다.

```bash
git remote add azure <URI from previous step>
```

Azure 원격에 푸시하여 앱을 배포합니다. 앞서 배포 사용자를 만들 때 만든 암호를 묻는 메시지가 나타납니다. Azure Portal에 로그인할 때 사용하는 암호가 아닌, [배포 사용자 구성](#configure-a-deployment-user)에서 만든 암호를 입력해야 합니다.

```bash
git push azure master
```

이전 명령은 다음 예와 유사한 정보를 출력합니다.
