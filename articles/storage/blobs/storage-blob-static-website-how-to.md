---
title: Azure 저장소에서 정적 웹 사이트 호스트
description: Azure 저장소 GPv2 계정의 컨테이너에서 정적 콘텐츠(HTML, CSS, JavaScript 및 이미지 파일)를 직접 제공하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247013"
---
# <a name="host-a-static-website-in-azure-storage"></a>Azure 저장소에서 정적 웹 사이트 호스트

Azure 저장소 GPv2 계정의 컨테이너에서 직접 정적 콘텐츠(HTML, CSS, JavaScript 및 이미지 파일)를 제공할 수 있습니다. 자세한 내용은 [Azure 저장소에서 호스팅하는 정적 웹 사이트를](storage-blob-static-website.md)참조하십시오.

이 문서에서는 Azure 포털, Azure CLI 또는 PowerShell을 사용하여 정적 웹 사이트 호스팅을 활성화하는 방법을 보여 줍니다.

## <a name="enable-static-website-hosting"></a>정적 웹 사이트 호스팅 사용

정적 웹 사이트 호스팅은 저장소 계정에서 활성화해야 하는 기능입니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **정적 웹 사이트**를 선택하여 정적 웹 사이트에 대한 구성 페이지를 표시합니다.

4. **사용**을 선택하여 스토리지 계정에서 정적 웹 사이트를 호스팅할 수 있습니다.

5. 인덱스 **문서 이름** 필드에서 기본 인덱스 페이지(예: *index.html)를*지정합니다. 

   기본 인덱스 페이지는 사용자가 정적 웹 사이트의 루트로 이동할 때 표시됩니다.  

6. 오류 **문서 경로** 필드에서 기본 오류 페이지(예: *404.html)를*지정합니다. 

   기본 오류 페이지는 사용자가 정적 웹 사이트에서 존재하지 않는 페이지로 이동하려고 할 때 표시됩니다.

7. **저장**을 클릭합니다. 이제 Azure Portal에는 정적 웹 사이트 엔드포인트가 표시됩니다. 

    ![스토리지 계정에서 정적 웹 사이트 호스팅 설정](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

[CLI(Azure 명령줄 인터페이스)를](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)사용하여 정적 웹 사이트 호스팅을 활성화할 수 있습니다.

1. 먼저 Azure [클라우드 셸을](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)열거나 Azure CLI를 로컬로 [설치한](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 경우 Windows PowerShell과 같은 명령 콘솔 응용 프로그램을 엽니다.

2. ID가 두 개 이상의 구독과 연결된 경우 정적 웹 사이트를 호스팅할 저장소 계정의 구독으로 활성 구독을 설정합니다.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독ID로 바꿉습니다.

3. 정적 웹 사이트 호스팅을 활성화합니다.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   * 브라우저가 `<error-document-name>` 존재하지 않는 사이트의 페이지를 요청할 때 사용자에게 표시되는 오류 문서의 이름으로 자리 표시자를 바꿉니다.

   * 자리 `<index-document-name>` 표시자를 인덱스 문서의 이름으로 바꿉니다. 이 문서는 일반적으로 "index.html"입니다.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell" />

Azure PowerShell 모듈을 사용하여 정적 웹 사이트 호스팅을 활성화할 수 있습니다.

1. Windows PowerShell 명령 창을 엽니다.

2. Azure PowerShell 모듈 Az 버전 0.7 이상이 있는지 확인합니다.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

3. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

4. ID가 두 개 이상의 구독과 연결된 경우 정적 웹 사이트를 호스팅할 저장소 계정의 구독으로 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독ID로 바꿉습니다.

5. 사용하려는 저장소 계정을 정의하는 저장소 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

6. 정적 웹 사이트 호스팅을 활성화합니다.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * 브라우저가 `<error-document-name>` 존재하지 않는 사이트의 페이지를 요청할 때 사용자에게 표시되는 오류 문서의 이름으로 자리 표시자를 바꿉니다.

   * 자리 `<index-document-name>` 표시자를 인덱스 문서의 이름으로 바꿉니다. 이 문서는 일반적으로 "index.html"입니다.

---

## <a name="upload-files"></a>파일 업로드 

### <a name="portal"></a>[포털](#tab/azure-portal)

이 지침은 Azure 포털에 나타나는 저장소 탐색기 버전을 사용하여 파일을 업로드하는 방법을 보여 주며, 이 방법을 보여 주시면 됩니다. 그러나 Azure 포털 외부에서 실행되는 [저장소 탐색기](https://azure.microsoft.com/features/storage-explorer/) 버전을 사용할 수도 있습니다. [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI 또는 계정의 **$web** 컨테이너에 파일을 업로드할 수 있는 사용자 지정 응용 프로그램을 사용할 수 있습니다. Visual Studio 코드를 사용하여 파일을 업로드하는 단계별 자습서의 경우 [자습서: Blob Storage에서 정적 웹 사이트 호스트를](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)참조하십시오.

1. **저장소 탐색기(미리 보기)를**선택합니다.

2. **BLOB 컨테이너** 노드를 확장한 다음 **$web** 컨테이너를 선택합니다.

3. **업로드** 버튼을 선택하여 파일을 업로드합니다.

   ![파일 업로드](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. 브라우저에서 파일 내용을 표시하려면 해당 파일의 콘텐츠 형식이 로 `text/html`설정되어 있는지 확인합니다. 

   ![콘텐츠 유형 확인](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > 저장소 탐색기는 이 `text/html` 속성을 에서 와 같이 `.html`일반적으로 인식된 확장으로 자동으로 설정합니다. 그러나 경우에 따라 직접 설정 해야 합니다. 이 속성을 `text/html`로 설정하지 않으면 브라우저에서 콘텐츠를 렌더링하는 대신 파일을 다운로드하라는 메시지가 표시됩니다. 이 속성을 설정하려면 파일을 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

소스 디렉터리에서 *$web* 컨테이너에 개체를 업로드합니다.

> [!NOTE]
> Azure Cloud Shell을 사용하는 경우 `\` `$web` 컨테이너를 참조할 때 이스케이프 문자를 `\$web`추가해야 합니다(예: ). Azure CLI의 로컬 설치를 사용하는 경우 이스케이프 문자를 사용할 필요가 없습니다.

이 예제에서는 Azure Cloud Shell 세션에서 명령을 실행 하는 것으로 가정 합니다.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

* `<source-path>` 자리 표시자를 업로드할 파일의 위치에 대한 경로로 바꿉습니다.

> [!NOTE]
> Azure CLI의 위치 설치를 사용하는 경우 로컬 컴퓨터의 모든 위치에 대한 경로를 사용할 `C:\myFolder`수 있습니다(예: .
>
> Azure 클라우드 셸을 사용하는 경우 클라우드 셸에 표시되는 파일 공유를 참조해야 합니다. 이 위치는 클라우드 공유 자체의 파일 공유 또는 클라우드 셸에서 마운트하는 기존 파일 공유일 수 있습니다. 이 작업을 수행하는 방법에 대해 알아보려면 [Azure Cloud Shell의 파일 유지를](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)참조하십시오.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

소스 디렉터리에서 *$web* 컨테이너에 개체를 업로드합니다.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* `<path-to-file>` 자리 표시자 값을 업로드할 파일에 대한 정규화된 경로로 바꿉니다(예: `C:\temp\index.html`).

* `<blob-name>` 자리 표시자 값을 결과 Blob을 지정할 이름으로 바꿉니다(예: `index.html`).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Azure 포털을 사용하여 웹 사이트 URL 찾기

웹 사이트의 공개 URL을 사용하여 브라우저에서 사이트의 페이지를 볼 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

<a id="portal-find-url" />

저장소 계정계정의 계정 개요 페이지 옆에 표시되는 창에서 **정적 웹 사이트를**선택합니다. 사이트의 URL이 **기본 끝점** 필드에 나타납니다.

![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

다음 명령을 사용하여 정적 웹 사이트의 공용 URL을 찾습니다.

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

* `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell-find-url" />

다음 명령을 사용하여 정적 웹 사이트의 공용 URL을 찾습니다.

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

* `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>정적 웹 사이트 페이지에서 메트릭 사용

메트릭을 사용하도록 설정하면 **$web** 컨테이너의 파일에 대한 트래픽 통계가 메트릭 대시보드에 보고됩니다.

1. 저장소 계정 메뉴의 **모니터** 섹션에서 **메트릭을** 클릭합니다.

   > [!div class="mx-imgBorder"]
   > ![메트릭 링크](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > 여러 메트릭 API에 연결하여 메트릭 데이터가 생성됩니다. 데이터를 반환하는 멤버에만 집중하기 위해 특정 시간 프레임에 사용된 API 멤버만 포털에 표시됩니다. 필요한 API 멤버를 선택할 수 있도록 첫 번째 단계는 시간 프레임을 확장하는 것입니다.

2. 시간 프레임 단추를 클릭하고 시간 프레임을 선택한 다음 **적용을**클릭합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 시간 범위](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. *네임스페이스* 드롭다운에서 **Blob을** 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 네임스페이스](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. 그런 다음, **송신** 메트릭을 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. *집계* 선택기에서 **합계**를 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 집계](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. 필터 **추가** 단추를 클릭하고 *속성* 선택기에서 **API 이름을** 선택합니다.

   ![Azure Storage 정적 웹 사이트 메트릭 API 이름](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. *값* 선택기에서 **GetWebContent** 옆의 확인란을 선택하여 메트릭 보고서를 채웁니다.

   ![Azure Storage 정적 웹 사이트 GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > **GetWebContent** 확인란은 해당 API 멤버가 지정된 기간 내에 사용된 경우에만 나타납니다. 데이터를 반환하는 멤버에만 집중하기 위해 특정 시간 프레임에 사용된 API 멤버만 포털에 표시됩니다. 이 목록에서 특정 API 멤버를 찾을 수 없는 경우 기간을 확장합니다.

## <a name="next-steps"></a>다음 단계

* 정적 웹 사이트를 사용하여 사용자 지정 도메인을 구성하는 방법을 알아봅니다. [Azure Blob 저장소 끝점에 사용자 지정 도메인 매핑을](storage-custom-domain-name.md)참조합니다.

