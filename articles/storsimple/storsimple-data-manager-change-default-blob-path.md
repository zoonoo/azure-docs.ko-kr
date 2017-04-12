---
title: "기본값에서 Blob 경로 변경 | Microsoft Docs"
description: "Azure Function을 설정하여 Blob 파일 경로의 이름을 변경하는 방법 알아보기(비공개 미리 보기)"
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
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>기본값에서 Blob 경로 변경(비공개 미리 보기)

이 문서에서는 Azure Function을 설정하여 기본 Blob 파일 경로의 이름을 변경하는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목이 있어야 합니다.
* 리소스 그룹 내의 하이브리드 데이터 리소스에서 올바르게 구성된 작업 정의입니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기

다음 단계를 수행하여 Azure Function을 만듭니다.

#### <a name="to-create-an-azure-function"></a>Azure Function을 만들려면 다음을 수행합니다.

1. [Azure 포털](http://portal.azure.com/)로 이동합니다.

2. 왼쪽 위 모서리에서 **+ 새로 만들기**를 클릭합니다. **검색** 텍스트 상자에서 "함수 앱"을 입력하고 **Enter** 키를 누릅니다.

    ![함수 앱 리소스로 이동](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. 결과에서 **함수 앱**을 클릭합니다.

    ![함수 앱 리소스 선택](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. **함수 앱** 창을 열고 **만들기**를 클릭합니다.

    ![새로운 함수 앱 만들기](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. **구성** 블레이드에서 모든 입력 사항을 입력하고 **만들기**를 클릭합니다.

    1. 앱 이름
    2. 구독
    3. 리소스 그룹
    4. 호스팅 계획 - **소비 계획**
    5. 위치
    6. 저장소 계정 - 기존 저장소 계정을 사용하거나 새 저장소 계정을 만듭니다. 저장소 계정은 함수에 대해 내부적으로 사용됩니다.

        ![새 함수 앱 구성 데이터 입력](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. 함수 앱이 만들어지면 왼쪽 아래에 **추가 서비스 >**로 이동합니다. **필터** 텍스트 상자에 "App Services"를 입력하고 **App Services**를 클릭합니다.

    ![추가 서비스 >](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. App Services의 목록에서 **함수 앱 이름**을 클릭합니다.

8. **+ 새 함수**를 클릭합니다. **언어** 드롭다운에서 **C#**을 선택합니다. 템플릿 목록에서 **QueueTrigger-CSharp** 옵션을 선택합니다. 모든 입력 사항을 입력합니다.

   1. 이름 - 사용자의 함수에 사용할 이름을 지정합니다.
   2. 큐 이름 - **데이터 변환 작업 정의 이름**을 입력합니다.
   3. 저장소 계정 연결 - **새로 만들기** 옵션을 클릭합니다. 데이터 변환 작업에 해당하는 계정을 선택합니다.
      
      `Connection name`을 기록해 둡니다. 이 이름은 Azure Function에서 나중에 필요합니다.

   4. **만들기** 단추를 클릭합니다.

       ![새 C# 함수 만들기 >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. **함수** 창에서 _.csx_ 파일을 실행합니다. 다음 코드를 복사하여 붙여넣습니다.

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
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
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

   1. 12번 줄에서 정확한 **STORAGE_CONNECTIONNAME**을 사용자의 저장소 계정 연결로 바꿉니다(포인트 8c 참조).
   2. 왼쪽 위에서 **저장** 단추를 클릭합니다.

       ![함수 저장 >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  오른쪽 모서리에서 **파일 보기**를 클릭합니다.

    ![파일 보기](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. **+ 추가**를 클릭합니다. **project.json**을 입력하고 **Enter** 키를 누릅니다.
   2. 다음 코드를 **project.json** 파일에 복사하여 붙여넣습니다.

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

   2. **Save**를 클릭합니다.

Azure Function이 만들어졌습니다. 이 함수는 데이터 변환 작업에서 새 Blob이 생성될 때마다 트리거됩니다.

## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).

