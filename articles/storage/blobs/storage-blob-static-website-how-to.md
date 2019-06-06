---
title: Azure Storage에서 정적 웹 사이트 호스트
description: Azure Storage GPv2 계정에서 컨테이너에서 직접 정적 콘텐츠 (HTML, CSS, JavaScript 및 이미지 파일)를 제공 하는 방법에 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 7a1aef14a2a32266c893933482527c361f17d7fb
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428562"
---
# <a name="host-a-static-website-in-azure-storage"></a>Azure Storage에서 정적 웹 사이트 호스트

Azure Storage GPv2 계정에서 컨테이너에서 직접 정적 콘텐츠 (HTML, CSS, JavaScript 및 이미지 파일)를 제공할 수 있습니다. 자세한 내용은 참조 하세요 [Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website.md)합니다.

이 문서에서는 Azure portal, Azure CLI 또는 PowerShell을 사용 하 여 정적 웹 사이트를 호스팅할 수 있도록 하는 방법을 보여 줍니다.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Azure Portal 사용

단계별 자습서를 참조 하세요. [자습서: Blob Storage에서 정적 웹 사이트 호스트](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)를 완료하세요.

정적 웹 사이트 호스팅에 사용 하도록 설정한 후에 웹 사이트의 공용 URL을 사용 하 여 브라우저에서 사이트의 페이지를 볼 수 있습니다.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Azure portal을 사용 하 여 웹 사이트 URL을 찾으려면

저장소 계정의 계정 개요 페이지 옆에 나타나는 창에서 선택 **정적 웹 사이트**합니다. 사이트의 URL에 표시 된 **기본 끝점** 필드입니다.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Azure CLI 사용

정적 웹 사이트를 사용 하 여 호스팅 가능 합니다 [Azure 명령줄 인터페이스 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)합니다.

1. 먼저 엽니다는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), 했다면 또는 [설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI를 로컬로 같은 Windows PowerShell 명령 콘솔 응용 프로그램을 엽니다.

2. 열려 있는 명령 창에서 저장소 미리 보기 확장을 설치 합니다.

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. 본인에 둘 이상의 구독과 연결 되어 설정 활성 정적 웹 사이트를 호스트 하는 저장소 계정의 구독을 구독 합니다.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   대체는 `<subscription-id>` 구독의 ID 사용 하 여 자리 표시자 값입니다.

4. 정적 웹 사이트를 호스팅할 수 있도록 합니다.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   * 대체는 `<error-document-name>` 자리 표시자를 브라우저에 존재 하지 않는 사이트의 페이지를 요청 하는 경우 사용자에 게 표시 되는 오류 문서의 이름입니다.

   * 대체는 `<index-document-name>` 자리 표시자 인덱스 문서 이름입니다. 이 문서는 일반적으로 "index.html".

5. 소스 디렉터리의 *$web* 컨테이너에 개체를 업로드합니다.

   > [!NOTE]
   > Azure Cloud Shell을 사용 하는 경우 추가 해야는 `\` 이스케이프 문자를 나타낼 때 합니다 `$web` 컨테이너 (예: `\$web`). Azure CLI의 로컬 설치를 사용 하는 경우 이스케이프 문자를 사용할 필요가 없습니다.

   Azure Cloud Shell 세션에서 명령을 실행 하는 것이 전제로 합니다.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   * 대체는 `<source-path>` 업로드 하려는 파일의 위치에 대 한 경로 사용 하 여 자리 표시자입니다.

   > [!NOTE]
   > Azure CLI를 설치 하는 위치를 사용 하는 경우 로컬 컴퓨터에서 모든 위치에 경로 사용할 수 있습니다 (예: `C:\myFolder`합니다.
   >
   > Azure Cloud Shell을 사용 하는 경우에 Cloud Shell에 표시 되는 파일 공유 참조 해야 합니다. 이 위치는 클라우드 파일 공유 자체 또는 Cloud Shell에서 탑재 하는 기존 파일 공유를 공유할 수 있습니다. 이 작업을 수행 하는 방법에 알아보려면 참조 [Azure Cloud Shell에서 파일 유지](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)합니다.

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 웹 사이트 URL을 찾으려면

웹 사이트의 공용 URL을 사용 하 여 브라우저에서 콘텐츠를 볼 수 있습니다.

다음 명령을 사용 하 여 URL을 찾으려면

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

* 대체는 `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름입니다.

<a id="powershell" />

## <a name="use-powershell"></a>PowerShell 사용

Azure PowerShell 모듈을 사용 하 여 정적 웹 사이트를 호스트 하는 것이 가능 합니다.

1. Windows PowerShell 명령 창을 엽니다.

2. Azure PowerShell 모듈 Az 버전 0.7 이상이 있는지 확인 합니다.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

3. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

4. 본인에 둘 이상의 구독과 연결 되어 설정 활성 정적 웹 사이트를 호스트 하는 저장소 계정의 구독을 구독 합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   대체는 `<subscription-id>` 구독의 ID 사용 하 여 자리 표시자 값입니다.

5. 사용 하려는 저장소 계정을 정의 하는 storage 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * 대체는 `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름입니다.

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

6. 정적 웹 사이트를 호스팅할 수 있도록 합니다.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 대체는 `<error-document-name>` 자리 표시자를 브라우저에 존재 하지 않는 사이트의 페이지를 요청 하는 경우 사용자에 게 표시 되는 오류 문서의 이름입니다.

   * 대체는 `<index-document-name>` 자리 표시자 인덱스 문서 이름입니다. 이 문서는 일반적으로 "index.html".

7. 소스 디렉터리의 *$web* 컨테이너에 개체를 업로드합니다.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * 대체는 `<path-to-file>` 업로드 하려는 파일에 정규화 된 경로 사용 하 여 자리 표시자 값 (예: `C:\temp\index.html`).

   * 대체는 `<blob-name>` 생성 되는 blob을 지정 하려는 이름의 자리 표시자 값 (예: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>PowerShell을 사용 하 여 웹 사이트 URL을 찾으려면

웹 사이트의 공용 URL을 사용 하 여 브라우저에서 콘텐츠를 볼 수 있습니다.

다음 명령을 사용 하 여 URL을 찾으려면

```powershell
$context = Get-AzSubscription -SubscriptionId <subscription-d>
Set-AzContext $context
```

대체는 `<subscription-id>` 구독의 ID 사용 하 여 자리 표시자 값입니다.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>정적 웹 사이트 페이지에 메트릭을 사용 하도록 설정

메트릭을 사용 하도록 설정한 후에 파일에 대 한 통계를 트래픽 합니다 **$web** 컨테이너 메트릭 대시보드에 보고 됩니다.

1. 클릭할 **설정을** > **모니터링** > **메트릭**합니다.

   여러 메트릭 API에 연결하여 메트릭 데이터가 생성됩니다. 데이터를 반환하는 멤버에만 집중하기 위해 특정 시간 프레임에 사용된 API 멤버만 포털에 표시됩니다. 필요한 API 멤버를 선택할 수 있도록 먼저 시간 프레임을 확장 하는 것입니다.

2. 선택한 시간 프레임 단추를 클릭 **최근 24 시간** 을 클릭 한 다음 **적용**합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 시간 범위](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. 선택 **Blob** 에서 합니다 *Namespace* 드롭다운 합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 네임스페이스](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 그런 다음, **송신** 메트릭을 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. *집계* 선택기에서 **합계**를 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 집계](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 클릭 합니다 **필터 추가** 단추를 선택 **API 이름** 에서 합니다 *속성* 선택기입니다.

   ![Azure Storage 정적 웹 사이트 메트릭 API 이름](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. 도대체 상자 옆에 **GetWebContent** 에 *값* 메트릭 보고서를 채우는 데 선택기입니다.

   ![Azure Storage 정적 웹 사이트 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>다음 단계

* [Azure Storage에서 정적 웹 사이트 호스팅](storage-blob-static-website.md)
* [Azure CDN를 사용 하 여 HTTPS를 통해 사용자 지정 도메인을 사용 하 여 blob 액세스](storage-https-custom-domain-cdn.md)
* [Blob 또는 웹 엔드포인트에 대한 사용자 지정 도메인 이름 구성](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [첫 번째 서버 없는 웹앱 빌드](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [자습서: Azure DNS에서 도메인 호스트](../../dns/dns-delegate-domain-azure-dns.md)
