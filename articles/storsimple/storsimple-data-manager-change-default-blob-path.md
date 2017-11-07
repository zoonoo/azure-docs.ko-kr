---
title: "기본값에서 Blob 경로 변경 | Microsoft Docs"
description: "Azure 함수를 설정하여 Blob 파일 경로의 이름을 변경하는 방법 알아보기(비공개 미리 보기)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 057d4d7370207859617eb63238bf425bfa6d3e16
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="change-a-blob-path-from-the-default-path-private-preview"></a>기본 경로에서 Blob 경로 변경(비공개 미리 보기)

이 문서에서는 Azure 함수를 설정하여 기본 Blob 파일 경로의 이름을 변경하는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건

리소스 그룹 내의 하이브리드 데이터 리소스에서 작업 정의가 올바르게 구성되어 있는지 확인합니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기

다음 단계를 수행하여 Azure 함수를 만듭니다.

1. [Azure 포털](http://portal.azure.com/)로 이동합니다.

2. 왼쪽 창 맨 위에 있는 **새로 만들기**를 클릭합니다. 

3. **검색** 상자에 **함수 앱**을 입력한 다음 Enter 키를 누릅니다.

    ![검색 상자에 “함수 앱” 입력](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

4. **결과** 목록에서 **함수 앱**을 클릭합니다.

    ![결과 목록에서 함수 앱 리소스 선택](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

    **함수 앱** 창이 열립니다.

5. **만들기**를 클릭합니다.

    ![함수 앱 창의 “만들기” 단추](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

6. **함수 앱** 구성 블레이드에서 다음을 수행합니다.

    a. **앱 이름** 상자에 앱 이름을 입력합니다.
    
    b. **구독** 상자에 구독의 이름을 입력합니다.

    c. **리소스 그룹**에서 **새로 만들기**를 클릭하고 리소스 그룹의 이름을 입력합니다.

    ㄹ. **호스팅 계획** 상자에 **소비 계획**을 입력합니다.

    e. **위치** 상자에 위치를 입력합니다.

    f. **저장소 계정**에서 기존 저장소 계정을 선택하거나 새 저장소 계정을 만듭니다. 저장소 계정은 함수에 대해 내부적으로 사용됩니다.

    ![새 함수 앱 구성 데이터 입력](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

7. **만들기**를 클릭합니다.  
    함수 앱이 생성됩니다.

8. 왼쪽 창에서 **더 많은 서비스**를 클릭한 후 다음을 수행합니다.
    
    a. **필터** 상자에 **App Services**를 입력합니다.
    
    b. **App Services**를 클릭합니다. 

    ![왼쪽 창의 “더 많은 서비스”](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

9. App Services 목록에서 함수 앱의 이름을 클릭합니다.  
    함수 앱 페이지가 열립니다.

10. 왼쪽 창에서 **새 함수**를 클릭한 후 다음을 수행합니다. 

    a. **언어** 목록에서 **C#**을 선택합니다.
    
    b. 템플릿 타일 배열에서 **QueueTrigger-CSharp**를 선택합니다.

    c. **함수 이름 지정** 상자에 함수의 이름을 입력합니다.

    d. **큐 이름** 상자에 데이터 변환 작업 정의 이름을 입력합니다.

    e. **저장소 계정 연결**에서 **새로 만들기**를 클릭하고 데이터 변환 작업에 해당하는 계정을 선택합니다.  
        연결 이름을 기록해 둡니다. 이 이름은 Azure 함수에서 나중에 필요합니다.

       ![새 C# 함수 만들기](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

    f. **만들기**를 클릭합니다.  
    **함수** 창이 열립니다.

11. **함수** 창에서 _.csx_ 파일을 실행하고 다음을 수행합니다.

    a. 다음 코드를 붙여 넣습니다.

    ```
    using System;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage.Queue;
    using Microsoft.WindowsAzure.Storage;
    using System.Collections.Generic;
    using System.Linq;

    public static void Run(QueueItem myQueueItem, TraceWriter log)
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

        string storageAccUriEndswith = "windows.net/";
        string uri = myQueueItem.TargetLocation.Replace("%20", " ");
        log.Info($"Blob Uri: {uri}");

        // Remove storage account uri string
        uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

        string containerName = uri.Substring(0, uri.IndexOf("/")); 

        // Remove container name string
        uri = uri.Substring(containerName.Length + 1);

        // Current blob path
        string blobName = uri; 

        string volumeName = uri.Substring(containerName.Length + 1);
        volumeName = uri.Substring(0, uri.IndexOf("/"));

        // Remove volume name string
        uri = uri.Substring(volumeName.Length + 1);

        string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
        string newBlobName = uri.Substring(newContainerName.Length + 1);

        log.Info($"Container name: {containerName}");
        log.Info($"Volume name: {volumeName}");
        log.Info($"New container name: {newContainerName}");

        log.Info($"Blob name: {blobName}");
        log.Info($"New blob name: {newBlobName}");

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Container reference
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
        newContainer.CreateIfNotExists();

        if(!container.Exists())
        {
            log.Info($"Container - {containerName} not exists");
            return;
        }

        if(!newContainer.Exists())
        {
            log.Info($"Container - {newContainerName} not exists");
            return;
        }

        CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
        if (!blob.Exists())
        {
            // Skip to copy the blob to new container, if source blob doesn't exist
            log.Info($"The specified blob does not exist.");
            log.Info($"Blob Uri: {blob.Uri}");
            return;
        }

        CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
        if (!blobCopy.Exists())
        {
            blobCopy.StartCopy(blob);
            // Delete old blob, after copy to new container
            blob.DeleteIfExists();
            log.Info($"Blob file path renamed completed successfully");
        }
        else
        {
            log.Info($"Blob file path renamed already done");
            // Delete old blob, if already exists.
            blob.DeleteIfExists();
        }
    }

    public class QueueItem
    {
        public string SourceLocation {get;set;}
        public long SizeInBytes {get;set;}
        public string Status {get;set;}
        public string JobID {get;set;}
        public string TargetLocation {get; set;}
    }

    ```

    b. 11번 줄에서 **STORAGE_CONNECTIONNAME**을 사용자의 저장소 계정 연결로 바꿉니다(포인트 8c 참조).

    c. 왼쪽 위에서 **저장**을 클릭합니다.

    ![함수 저장](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. 함수를 완료하려면 다음을 수행하여 파일을 하나 더 추가합니다.

    a. **파일 보기**를 클릭합니다.

       ![“파일 보기” 링크](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    b. **추가**를 클릭합니다.
    
    c. **project.json**을 입력하고 Enter 키를 누릅니다.
    
    ㄹ. **project.json** 파일에 다음 코드를 붙여 넣습니다.

    ```
    {
    "frameworks": {
        "net46":{
        "dependencies": {
            "windowsazure.storage": "8.1.1"
        }
        }
    }
    }

    ```

    e. **Save**를 클릭합니다.

Azure Function이 만들어졌습니다. 이 함수는 데이터 변환 작업에서 새 Blob이 생성될 때마다 트리거됩니다.

## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).
