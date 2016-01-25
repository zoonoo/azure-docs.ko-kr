<properties
	pageTitle="Blob 저장소를 사용하는 온-프레미스 응용 프로그램(Java) | Microsoft Azure"
	description="Azure에 이미지를 업로드한 다음 브라우저에 해당 이미지를 표시하는 콘솔 응용 프로그램을 만드는 방법에 대해 알아봅니다. 코드 샘플은 Java로 작성되었습니다."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="micurd"/>

# Blob 저장소를 사용하는 온-프레미스 응용 프로그램

## 개요

다음 예제에서는 Azure 저장소를 사용하여 이미지를 Azure에 저장할 수 있는 방법을 보여 줍니다. 이 문서의 코드는 Azure에 이미지를 업로드한 다음 그 이미지를 브라우저에 표시하는 HTML 파일을 만드는 콘솔 응용 프로그램용 코드입니다.

## 필수 조건

- JDK(Java Developer Kit) 버전 1.6 이상이 설치되어 있어야 합니다.
- Azure SDK가 설치되어 있어야 합니다.
- Java용 Azure 라이브러리를 위한 JAR 및 해당하는 종속성 JAR이 설치되어 있어야 하며 Java 컴파일러가 사용하는 빌드 경로에 있어야 합니다. Java용 Azure 라이브러리 설치에 대한 자세한 내용은 [Java용 Azure SDK 다운로드][]를 참조하세요.
- Azure 저장소 계정이 설정되어 있어야 합니다. 이 문서의 코드에서는 저장소 계정의 계정 이름 및 계정 키를 사용합니다. 저장소 계정 만들기에 대한 내용은 [저장소 계정을 만드는 방법]을, 계정 키 검색에 대한 내용은 [저장소 계정을 관리하는 방법][]을 참조하세요.
- 이름을 지정한 로컬 이미지 파일을 만들어 c:\\myimages\\image1.jpg 경로에 저장해야 합니다. 또는 예제에서 **FileInputStream** 생성자를 수정하여 다른 이미지 경로 및 파일 이름을 사용합니다.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Azure Blob 저장소를 사용하여 파일을 업로드하려면

여기서는 단계별 절차를 제공합니다. 건너뛰고 싶은 경우 이 문서의 뒷부분에 전체 코드가 제공됩니다.

Azure 핵심 저장소 클래스, Azure Blob 클라이언트 클래스, Java IO 클래스, **URISyntaxException** 클래스에 대한 가져오기를 포함하여 코드를 시작합니다.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

이름이 **StorageSample**인 클래스를 선언하고 여는 중괄호(**{**)를 포함합니다.

    public class StorageSample {

**StorageSample** 클래스 내에서 Azure 저장소 계정에 지정된 것과 같이 기본 끝점 프로토콜, 저장소 계정 이름, 저장소 액세스 키가 포함될 문자열 변수를 선언합니다. **your\_account\_name** 및 **your\_account\_key** 자리 표시자 값을 각각 자신의 계정 이름 및 계정 키로 바꿉니다.

    public static final String storageConnectionString =
           "DefaultEndpointsProtocol=http;" +
               "AccountName=your_account_name;" +
               "AccountKey=your_account_name";

**main** 선언을 추가하고 **try** 블록을 포함하며 필요한 여는 중괄호(**{**)를 포함합니다.

    public static void main(String[] args)
    {
        try
        {

다음 유형의 변수를 선언합니다. 이 예제에서 변수가 어떻게 사용되는지 설명되어 있습니다.

-   **CloudStorageAccount**: Azure 저장소 계정 이름 및 키로 계정 개체를 초기화하고 Blob 클라이언트 개체를 만드는 데 사용됩니다.
-   **CloudBlobClient**: Blob 서비스에 액세스하는 데 사용됩니다.
-   **CloudBlobContainer**: Blob 컨테이너를 만들고, 컨테이너에서 Blob을 나열하고, 컨테이너를 삭제하는 데 사용됩니다.
-   **CloudBlockBlob**: 로컬 이미지 파일을 컨테이너에 업로드하는 데 사용됩니다.

<!-- -->

    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;

**account** 변수에 값을 할당합니다.

    account = CloudStorageAccount.parse(storageConnectionString);

**serviceClient** 변수에 값을 할당합니다.

    serviceClient = account.createCloudBlobClient();

**container** 변수에 값을 할당합니다. **gettingstarted**라는 컨테이너에 대한 참조를 얻게 됩니다.

    // Container name must be lower case.
    container = serviceClient.getContainerReference("gettingstarted");

컨테이너를 만듭니다. 이 메서드는 컨테이너가 없을 경우 컨테이너를 만들고 **true**를 반환합니다. 컨테이너가 있으면 **false**를 반환합니다. **createIfNotExists** 대신 **create** 메서드(컨테이너가 이미 있는 경우 오류를 반환함)를 사용할 수 있습니다.

    container.createIfNotExists();

컨테이너에 대해 익명 액세스를 설정합니다.

    // Set anonymous access on the container.
    BlobContainerPermissions containerPermissions;
    containerPermissions = new BlobContainerPermissions();
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
    container.uploadPermissions(containerPermissions);

블록 Blob에 대한 참조를 가져오며 Azure 저장소의 Blob을 나타냅니다.

    blob = container.getBlockBlobReference("image1.jpg");

**File** 생성자를 사용하여, 로컬로 만들어졌으며 업로드할 파일에 대한 참조를 가져옵니다. 이 파일은 코드를 실행하기 전에 만들어야 합니다.

    File fileReference = new File ("c:\\myimages\\image1.jpg");

**CloudBlockBlob.upload** 메서드에 대한 호출을 통해 로컬 파일을 업로드합니다. **CloudBlockBlob.upload** 메서드의 첫 번째 매개 변수는 Azure 저장소에 업로드할 로컬 파일을 나타내는 **FileInputStream** 개체입니다. 두 번째 매개 변수는 파일의 크기(바이트)입니다.

    blob.upload(new FileInputStream(fileReference), fileReference.length());

**MakeHTMLPage**라는 도우미 함수를 호출하여 지금 Azure 저장소 계정에 있는 Blob에 대한 원본 집합에 **&lt;image&gt;** 요소를 포함하는 기본 HTML 페이지를 만듭니다. **MakeHTMLPage**에 대한 코드는 이 항목의 뒷부분에서 다룹니다.

    MakeHTMLPage(container);

만들어진 HTML 페이지에 대한 상태 메시지 및 정보를 출력합니다.

    System.out.println("Processing complete.");
    System.out.println("Open index.html to see the images stored in your storage account.");

다음 닫는 중괄호(**}**)를 삽입하여 **try** 블록을 닫습니다.

다음 예외를 처리합니다.

-   **FileNotFoundException**: **FileInputStream** 또는 **FileOutputStream** 생성자가 throw할 수 있습니다.
-   **StorageException**: Azure 클라이언트 저장소 라이브러리가 throw할 수 있습니다.
-   **URISyntaxException**: **ListBlobItem.getUri** 메서드가 throw할 수 있습니다.
-   **Exception**: 일반적인 예외 처리입니다.

<!-- -->

    catch (FileNotFoundException fileNotFoundException)
    {
        System.out.print("FileNotFoundException encountered: ");
        System.out.println(fileNotFoundException.getMessage());
        System.exit(-1);
    }
    catch (StorageException storageException)
    {
        System.out.print("StorageException encountered: ");
        System.out.println(storageException.getMessage());
        System.exit(-1);
    }
    catch (URISyntaxException uriSyntaxException)
    {
        System.out.print("URISyntaxException encountered: ");
        System.out.println(uriSyntaxException.getMessage());
        System.exit(-1);
    }
    catch (Exception e)
    {
        System.out.print("Exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

다음 닫는 중괄호(**}**)를 삽입하여 **main**을 닫습니다.

이름이 **MakeHTMLPage**인 메서드를 만들어 기본 HTML 페이지를 만듭니다. 이 메서드는 업로드된 Blob 목록에서 반복하는 데 사용될 **CloudBlobContainer** 유형의 매개 변수를 가집니다. 이 메서드는 **FileOutputStream** 생성자가 throw할 수 있는 **FileNotFoundException** 유형 및 **ListBlobItem.getUri** 메서드가 throw할 수 있는 **URISyntaxException** 유형의 예외를 throw합니다. 여는 중괄호(**{**)를 포함합니다.

    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {

이름이 **index.html**인 로컬 파일을 만듭니다.

    PrintStream stream;
    stream = new PrintStream(new FileOutputStream("index.html"));

**&lt;html&gt;**, **&lt;header&gt;** 및 **&lt;body&gt;** 요소에서 추가하여 로컬 파일에 씁니다.

    stream.println("<html>");
    stream.println("<header/>");
    stream.println("<body>");

업로드된 Blob 목록에서 반복합니다. Blob이 Azure 저장소 계정에 있는 경우 각 Blob에 대해 HTML 페이지에서 Blob의 URI에 보낸 **src** 특성이 있는 **&lt;img&gt;** 요소를 만듭니다. 이 샘플에서는 이미지를 하나만 추가했지만 더 추가하면 이 코드는 모든 이미지를 반복합니다.

간단히 하기 위해 이 예제는 업로드된 각 Blob이 이미지라고 가정합니다. 이미지가 아닌 Blob을 업데이트했거나 블록 Blob이 아닌 페이지 Blob을 업데이트했으면 필요에 따라 코드를 조정합니다.

    // Enumerate the uploaded blobs.
    for (ListBlobItem blobItem : container.listBlobs()) {
    // List each blob as an <img> element in the HTML body.
    stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
    }

**&lt;body&gt;** 요소 및 **&lt;html&gt;** 요소를 닫습니다.

    stream.println("</body>");
    stream.println("</html>");

로컬 파일을 닫습니다.

    stream.close();

다음 닫는 중괄호(**}**)를 삽입하여 **MakeHTMLPage**를 닫습니다.

다음 닫는 중괄호(**}**)를 삽입하여 **StorageSample**을 닫습니다.

다음은 이 예제의 완성 코드입니다. 계정 이름 및 계정 키를 사용하도록 **your\_account\_name** 및 **your\_account\_key** 자리 표시자 값을 각각 수정해야 합니다.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import java.io.*;
    import java.net.URISyntaxException;

    // Create an image, c:\myimages\image1.jpg, prior to running this sample.
    // Alternatively, change the value used by the FileInputStream constructor
    // to use a different image path and file that you have already created.
    public class StorageSample {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                       "AccountName=your_account_name;" +
                       "AccountKey=your_account_name";

        public static void main(String[] args) {
            try {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;
                CloudBlockBlob blob;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.createIfNotExists();

                // Set anonymous access on the container.
                BlobContainerPermissions containerPermissions;
                containerPermissions = new BlobContainerPermissions();
                containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
                container.uploadPermissions(containerPermissions);

                // Upload an image file.
                blob = container.getBlockBlobReference("image1.jpg");

                File fileReference = new File("c:\\myimages\\image1.jpg");
                blob.upload(new FileInputStream(fileReference), fileReference.length());

                // At this point the image is uploaded.
                // Next, create an HTML page that lists all of the uploaded images.
                MakeHTMLPage(container);

                System.out.println("Processing complete.");
                System.out.println("Open index.html to see the images stored in your storage account.");

            } catch (FileNotFoundException fileNotFoundException) {
                System.out.print("FileNotFoundException encountered: ");
                System.out.println(fileNotFoundException.getMessage());
                System.exit(-1);
            } catch (StorageException storageException) {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            } catch (URISyntaxException uriSyntaxException) {
                System.out.print("URISyntaxException encountered: ");
                System.out.println(uriSyntaxException.getMessage());
                System.exit(-1);
            } catch (Exception e) {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }

        // Create an HTML page that can be used to display the uploaded images.
        // This example assumes all of the blobs are for images.
        public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
        {
            PrintStream stream;
            stream = new PrintStream(new FileOutputStream("index.html"));

            // Create the opening <html>, <header>, and <body> elements.
            stream.println("<html>");
            stream.println("<header/>");
            stream.println("<body>");

            // Enumerate the uploaded blobs.
            for (ListBlobItem blobItem : container.listBlobs()) {
                // List each blob as an <img> element in the HTML body.
                stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
            }

            stream.println("</body>");

            // Complete the <html> element and close the file.
            stream.println("</html>");
            stream.close();
        }
    }

이 예제 코드는 로컬 이미지 파일을 Azure 저장소에 업로드할 뿐만 아니라 브라우저에서 열어 업로드된 이미지를 볼 수 있는 namedindex.html 로컬 파일도 만듭니다.

코드에 계정 이름 및 계정 키가 포함되어 있으므로 소스 코드가 안전한지 확인하세요.

## 컨테이너를 삭제하려면

저장소에 대한 요금이 부과되므로 이 예제를 테스트한 후에 **gettingstarted** 컨테이너를 삭제할 수 있습니다. 컨테이너를 삭제하려면 **CloudBlobContainer.delete** 메서드를 사용합니다.

    container = serviceClient.getContainerReference("gettingstarted");
    container.delete();

**CloudBlobContainer.delete** 메서드를 호출하기 위해 **CloudStorageAccount**, **ClodBlobClient**, **CloudBlobContainer** 개체를 초기화하는 프로세스는 **createIfNotExist** 메서드의 경우와 동일합니다. 다음은 **gettingstarted**라는 컨테이너를 삭제하는 완성 예제입니다.

    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

    public class DeleteContainer {

        public static final String storageConnectionString =
                "DefaultEndpointsProtocol=http;" +
                   "AccountName=your_account_name;" +
                   "AccountKey=your_account_key";

        public static void main(String[] args)
        {
            try
            {
                CloudStorageAccount account;
                CloudBlobClient serviceClient;
                CloudBlobContainer container;

                account = CloudStorageAccount.parse(storageConnectionString);
                serviceClient = account.createCloudBlobClient();
                // Container name must be lower case.
                container = serviceClient.getContainerReference("gettingstarted");
                container.delete();

                System.out.println("Container deleted.");

            }
            catch (StorageException storageException)
            {
                System.out.print("StorageException encountered: ");
                System.out.println(storageException.getMessage());
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.print("Exception encountered: ");
                System.out.println(e.getMessage());
                System.exit(-1);
            }
        }
    }

기타 Blob 저장소 클래스 및 메서드에 대한 개요는 [Java에서 Blob 저장소 서비스를 사용하는 방법]을 참조하세요.

## 다음 단계

더 복잡 한 저장소 작업에 대한 자세한 내용을 보려면 다음 링크를 따라가세요.

- [Java용 Azure 저장소 SDK][]
- [Azure 저장소 클라이언트 SDK 참조][]
- [Azure 저장소 REST API][]
- [Azure 저장소 팀 블로그][]

  [Java용 Azure SDK 다운로드]: http://go.microsoft.com/fwlink/?LinkID=525671
  [저장소 계정을 만드는 방법]: storage-create-storage-account.md#create-a-storage-account
  [저장소 계정을 관리하는 방법]: storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys
  [Java에서 Blob 저장소 서비스를 사용하는 방법]: storage-java-how-to-use-blob-storage.md
  [Java용 Azure 저장소 SDK]: https://github.com/azure/azure-storage-java
  [Azure 저장소 클라이언트 SDK 참조]: http://dl.windowsazure.com/storage/javadoc/
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=AcomDC_0114_2016-->