로컬 터미널 창에서 로컬 Git 리포지토리에 Azure 원격을 추가합니다. 이 Azure 원격은 [웹앱 만들기](#create-a-web-app)에서 만들었습니다.

```bash
git remote add azure <URI from previous step>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. 암호를 입력하라는 메시지가 표시되는 경우 Azure Portal에 로그인할 때 사용하는 암호가 아닌, [배포 사용자 구성](#configure-a-deployment-user)에서 만든 암호를 입력해야 합니다.

```bash
git push azure master
```

이전 명령은 다음 예와 유사한 정보를 출력합니다.
