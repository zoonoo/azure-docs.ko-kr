---
title: 자습서 - Azure Databricks를 통해 Key Vault를 사용하여 Blob 스토리지에 액세스
description: 이 자습서에서는 키 자격 증명 모음에 저장된 비밀을 사용하여 Azure Databricks에서 Azure Blob Storage에 액세스하는 방법을 설명합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 27e166a8798f851f6c086c025dd82957b2dcfb84
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849278"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>자습서: Azure Key Vault를 사용하여 Azure Databricks에서 Azure Blob Storage에 액세스

이 자습서에서는 키 자격 증명 모음에 저장된 비밀을 사용하여 Azure Databricks에서 Azure Blob Storage에 액세스하는 방법을 설명합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 스토리지 계정 및 Blob 컨테이너 만들기
> * Azure Key Vault 만들기 및 비밀 추가
> * Azure Databricks 작업 영역 만들기 및 비밀 범위 추가
> * Azure Databricks에서 Blob 컨테이너에 액세스

## <a name="prerequisites"></a>필수 조건

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

> [!Note]
> 이 자습서는 **Azure 평가판 구독**을 사용하여 수행할 수 없습니다.
> 무료 계정이 있는 경우 프로필로 이동하고 구독을 **종량제**로 변경합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요. 그런 다음 [지출 한도를 제거](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)하고 해당 지역의 vCPU에 대한 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)합니다. Azure Databricks 작업 영역을 만드는 경우 **평가판(프리미엄-14일 무료 DBU)** 가격 책정 계층을 선택하여 14일간 무료 프리미엄 Azure Databricks DBU를 위한 작업 영역 액세스 권한을 부여할 수 있습니다.

## <a name="create-a-storage-account-and-blob-container"></a>스토리지 계정 및 Blob 컨테이너 만들기

1. Azure Portal에서 **리소스 만들기** > **스토리지**를 차례로 선택합니다. 그런 다음, **스토리지 계정**을 선택합니다.

   ![Azure 스토리지 계정 리소스 찾기](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. 구독 및 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다. 그런 다음, 스토리지 계정 이름을 입력하고 위치를 선택합니다. **검토 + 만들기**를 선택합니다.

   ![스토리지 계정 속성 설정](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. 유효성 검사에 실패하면 문제를 해결하고 다시 시도합니다. 유효성 검사에 성공하면 **만들기**를 선택하고, 스토리지 계정이 만들어질 때까지 기다립니다.

4. 새로 만든 스토리지 계정으로 이동하여 **개요**페이지의 **서비스** 아래에서 **Blob**을 선택합니다. 그런 다음, **+ 컨테이너**를 선택하고 컨테이너 이름을 입력합니다. **확인**을 선택합니다.

   ![새 컨테이너 만들기](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Blob 스토리지 컨테이너에 업로드하려는 파일을 찾습니다. 파일이 없으면 텍스트 편집기를 사용하여 일부 정보가 포함된 새 텍스트 파일을 만듭니다. 이 예에서 **hw.txt**라는 파일에는 "hello world" 텍스트가 포함되어 있습니다. 텍스트 파일을 로컬로 저장하고 Blob 스토리지 컨테이너에 업로드합니다.

   ![컨테이너에 파일 업로드](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. 스토리지 계정으로 돌아가서 **설정** 아래에서 **액세스 키**를 선택합니다. 이 자습서에서 나중에 사용할 수 있도록 **스토리지 계정 이름** 및 **key1**을 텍스트 편집기에 복사합니다.

   ![스토리지 계정 액세스 키 찾기](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Azure Key Vault 만들기 및 비밀 추가

1. Azure Portal에서 **리소스 만들기**를 선택하고, 검색 상자에서 **Key Vault**를 입력합니다.

   ![Azure 리소스 만들기 검색 상자](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Key Vault 리소스가 자동으로 선택됩니다. **만들기**를 선택합니다.

   ![Key Vault 리소스 만들기](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. **키 자격 증명 모음 만들기** 페이지에서 다음 정보를 입력하고 나머지 필드의 기본값을 유지합니다.

   |자산|설명|
   |--------|-----------|
   |Name|키 자격 증명 모음의 고유한 이름입니다.|
   |Subscription|구독을 선택합니다.|
   |Resource group|리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.|
   |위치|위치를 선택합니다.|

   ![Azure 키 자격 증명 모음 속성](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. 위의 정보를 제공한 후 **만들기**를 선택합니다. 

4. Azure Portal에서 새로 만든 키 자격 증명 모음으로 이동하여 **비밀**을 선택합니다. 그런 다음, **+ 생성/가져오기**를 선택합니다. 

   ![새 키 자격 증명 모음 비밀 생성](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. **비밀 만들기** 페이지에서 다음 정보를 제공하고 나머지 필드의 기본값을 유지합니다.

   |자산|값|
   |--------|-----------|
   |업로드 옵션|설명서|
   |Name|스토리지 계정 키의 이름입니다.|
   |값|스토리지 계정의 key1입니다.|

   ![새 키 자격 증명 모음 비밀에 대한 속성](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. 이 자습서의 뒷부분에서 사용할 수 있도록 키 이름을 텍스트 편집기에 저장하고, **만들기**를 선택합니다. 그런 다음, **속성** 메뉴로 이동합니다. 자습서의 뒷부분에서 사용할 수 있도록 **DNS 이름** 및 **리소스 ID**를 텍스트 편집기에 복사합니다.

   ![Azure Key Vault DNS 이름 및 리소스 ID 복사](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Azure Databricks 작업 영역 만들기 및 비밀 범위 추가

1. Azure Portal에서 **리소스 만들기** > **분석** > **Azure Databricks**를 차례로 선택합니다.

    ![Azure Portal의 Databricks](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. **Azure Databricks 서비스** 아래에서 Databricks 작업 영역을 만드는 데 필요한 다음 값을 제공합니다.

   |자산  |설명  |
   |---------|---------|
   |작업 영역 이름     | Databricks 작업 영역의 이름을 제공합니다.        |
   |Subscription     | 드롭다운에서 Azure 구독을 선택합니다.        |
   |Resource group     | 키 자격 증명 모음이 포함된 동일한 리소스 그룹을 선택합니다. |
   |위치     | Azure Key Vault와 동일한 위치를 선택합니다. 사용 가능한 모든 지역에 대해서는 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/)를 참조하세요.        |
   |가격 책정 계층     |  **표준** 또는 **프리미엄** 중에서 선택합니다. 이러한 계층에 대한 자세한 내용은 [Databricks 가격 페이지](https://azure.microsoft.com/pricing/details/databricks/)를 참조하세요.       |

   ![Databricks 작업 영역 속성](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   **만들기**를 선택합니다.

3. Azure Portal에서 새로 만든 Azure Databricks 리소스로 이동하여 **작업 영역 시작**을 선택합니다.

   ![Azure Databricks 작업 영역 시작](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. 별도의 창에서 Azure Databricks 작업 영역이 열리면 **#secrets/createScope**를 URL에 추가합니다. URL의 형식은 다음과 같습니다. 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. 범위 이름을 입력하고, 앞에서 저장한 Azure Key Vault DNS 이름과 리소스 ID를 입력합니다. 이 자습서의 뒷부분에서 사용할 수 있도록 범위 이름을 텍스트 편집기에 저장합니다. 그런 다음 **만들기**를 선택합니다.

   ![Azure Databricks 작업 영역에서 비밀 범위 만들기](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Azure Databricks에서 Blob 컨테이너에 액세스

1. Azure Databrics 작업 영역의 홈 페이지에서 **일반 작업**  아래에 있는 **새 클러스터**를 선택합니다.

   ![새 Azure Databricks Notebook 만들기](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. 클러스터 이름을 입력하고, **클러스터 만들기**를 선택합니다. 클러스터를 만드는 데 몇 분이 걸립니다.

3. 클러스터가 만들어지면 Azure Databricks 작업 영역의 홈 페이지로 이동하여 **일반 작업** 아래에서 **새 Notebook**을 선택합니다.

   ![새 Azure Databricks Notebook 만들기](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Notebook 이름을 입력하고, 언어를 Python으로 설정합니다. 클러스터를 이전 단계에서 만든 클러스터의 이름으로 설정합니다.

5. 다음 명령을 실행하여 Blob 스토리지 컨테이너를 탑재합니다. 다음 속성의 값을 변경해야 합니다.

   * your-container-name
   * your-storage-account-name
   * mount-name
   * config-key
   * scope-name
   * key-name

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name**은 Blob Storage 컨테이너 또는 컨테이너 내의 폴더가 탑재되는 위치(원본에서 지정됨)를 나타내는 DBFS 경로입니다.
   * **conf-key**는 `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` 또는 `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`일 수 있습니다.
   * **scope-name**은 이전 섹션에서 만든 비밀 범위의 이름입니다. 
   * **key-name**은 키 자격 증명 모음에 있는 스토리지 계정 키에 대해 만든 비밀의 이름입니다.

   ![Notebook에서 Blob 스토리지 탑재 만들기](./media/store-secrets-azure-key-vault/command1.png)

6. 다음 명령을 실행하여 Blob 스토리지 컨테이너의 텍스트 파일을 데이터 프레임으로 읽습니다. 명령의 값을 탑재 이름 및 파일 이름과 일치하도록 변경합니다.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![데이터 프레임으로 파일 읽기](./media/store-secrets-azure-key-vault/command2.png)

7. 다음 명령을 사용하여 파일의 내용을 표시합니다.

   ```python
   df.show()
   ```
   ![데이터 프레임 표시](./media/store-secrets-azure-key-vault/command3.png)

8. Blob 스토리지를 분리하려면 다음 명령을 실행합니다.

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![스토리지 계정 분리](./media/store-secrets-azure-key-vault/command4.png)

9. 탑재가 분리되면 더 이상 Blob 스토리지 계정에서 읽을 수 없습니다.

   ![스토리지 계정 분리 오류](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 리소스 그룹 전체를 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택하고, 리소스 그룹으로 이동합니다.

2. **리소스 그룹 삭제**를 선택하고, 리소스 그룹 이름을 입력합니다. 그런 다음, **삭제**를 선택합니다. 

## <a name="next-steps"></a>다음 단계

Cosmos DB에 서비스 엔드포인트를 사용하도록 설정된 VNet 주입 Databricks 환경을 구현하는 방법을 알아보려면 다음 문서로 계속 진행하세요.
> [!div class="nextstepaction"]
> [자습서: Cosmos DB 엔드포인트를 사용하여 Azure Databricks 구현](service-endpoint-cosmosdb.md)
