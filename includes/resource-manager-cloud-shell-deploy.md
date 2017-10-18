## <a name="deploy-template-from-cloud-shell"></a>Cloud Shell에서 템플릿 배포

[Cloud Shell](../articles/cloud-shell/overview.md)을 사용하여 템플릿을 배포할 수 있습니다. 그러나 먼저 Cloud Shell용 파일 공유에 템플릿을 로드해야 합니다. Cloud Shell을 사용해 본 적이 없다면 [Azure Cloud Shell 개요](../articles/cloud-shell/overview.md)에서 Cloud Shell 설정 방법을 참조하세요.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. Cloud Shell 리소스 그룹을 선택합니다. 이름 패턴은 `cloud-shell-storage-<region>`입니다.

   ![리소스 그룹 선택](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Cloud Shell용 저장소 계정을 선택합니다.

   ![저장소 계정 선택](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. **파일**을 선택합니다.

   ![파일 선택](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Cloud Shell용 파일 공유를 선택합니다. 이름 패턴은 `cs-<user>-<domain>-com-<uniqueGuid>`입니다.

   ![파일 공유 선택](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. **디렉터리 추가**를 선택합니다.

   ![디렉터리 추가](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. 이름을 **templates**로 지정하고 **확인**을 선택합니다.

   ![디렉터리 이름 지정](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. 새 디렉터리를 선택합니다.

   ![디렉터리 선택](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. **업로드**를 선택합니다.

   ![업로드 선택](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. 템플릿을 찾아서 업로드합니다.

   ![파일 업로드](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. 프롬프트를 엽니다.

   ![Cloud Shell 열기](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
