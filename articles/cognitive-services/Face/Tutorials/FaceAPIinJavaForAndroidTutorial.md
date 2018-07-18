---
title: Android용 Face API Java 자습서 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 이미지에서 사람 얼굴을 감지하고 포착하는 Cognitive Services Face API를 사용하는 간단한 Android 앱을 만듭니다.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377814"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Android용 Java에서 Face API 시작 자습서

이 자습서에서는 Face API를 호출하는 간단한 Android 응용 프로그램을 개발하여 이미지에서 사람 얼굴을 감지하는 방법을 알아봅니다. 응용 프로그램에서는 감지하는 얼굴을 포착하여 결과를 보여줍니다.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> 준비

자습서를 사용하려면 다음 필수 조건이 필요합니다.

- Android Studio 및 SDK 설치
- Android 장치(테스트의 경우 선택 사항)

## <a name="step1"></a>1단계: Face API 구독 및 구독 키 받기

Face API를 사용하려면 먼저 Microsoft Cognitive Services 포털에서 등록하여 Face API를 구독해야 합니다. [구독](https://azure.microsoft.com/try/cognitive-services/)을 참조하세요. 기본 키와 보조 키 모두 이 자습서에서 사용할 수 있습니다.

## <a name="step2"></a>2단계: 응용 프로그램 프레임워크 만들기

이 단계에서는 이미지를 선택하고 표시할 기본 UI를 구현하도록 Android 응용 프로그램 프로젝트를 만듭니다. 아래의 지침을 따르면 됩니다. 

1. Android Studio를 엽니다.
2. 파일 메뉴에서 **새 프로젝트...** 를 클릭합니다.
3. 응용 프로그램 이름을 **MyFirstApp**으로 지정한 다음, 다음을 클릭합니다. 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. 필요에 따라 대상 플랫폼을 선택한 다음, 다음을 클릭합니다. 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. **기본 작업**을 선택한 다음, 다음을 클릭합니다.
6. 다음과 같이 작업 이름을 지정한 다음, 마침을 클릭합니다. 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. **activity_main.xml**을 열면 이 작업의 레이아웃 편집기가 표시됩니다.
8. 다음과 같이 텍스트 원본 파일을 확인한 다음, 작업 레이아웃을 편집합니다.

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. **MainActivity.java**를 열고 파일의 시작 부분에 다음 import 지시문을 삽입합니다.

    ```java
    import java.io.*; 
    import android.app.*; 
    import android.content.*; 
    import android.net.*; 
    import android.os.*; 
    import android.view.*; 
    import android.graphics.*; 
    import android.widget.*; 
    import android.provider.*;
    ```
      
    둘째, 클래스를 다음과 같이 수정합니다.  
    
    ```java
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;
         
    @Override
    protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
           Button button1 = (Button)findViewById(R.id.button1);
           button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

이제 앱은 갤러리에서 사진을 찾아보고 아래 이미지와 비슷하게 창에 표시될 수 있습니다.

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>3단계: Face API 클라이언트 라이브러리 구성

Face API는 HTTPS 요청을 사용하여 호출할 수 있는 클라우드 API입니다. .NET 플랫폼 응용 프로그램에서 Face API를 사용하는 편리한 방법으로 클라이언트 라이브러리도 제공되어 웹 요청을 캡슐화합니다. 이 예제에서는 클라이언트 라이브러리를 사용하여 작업을 간소화합니다. 

클라이언트 라이브러리를 구성하려면 아래 지침을 따르세요. 

1. 예제에서 보여준 대로 프로젝트 패널에서 프로젝트의 최상위 **build.gradle** 파일을 찾습니다. 프로젝트 트리에 여러 다른 **build.gradle** 파일이 있지만 처음에 최상위 **build.gradle** 파일을 열어야 합니다.
2. **mavenCentral()** 을 프로젝트의 리포지토리에 추가합니다. jcenter()가 mavenCentral()의 상위 집합이므로 Android Studio의 기본 리포지토리인 jcenter()를 사용할 수도 있습니다.  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. '앱' 프로젝트에서 **build.gradle** 파일을 엽니다.
4. Maven 중앙 리포지토리에 저장된 클라이언트 라이브러리의 종속성을 추가합니다.

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. '앱' 프로젝트에서 **MainActivity.java**를 열고 다음 import 지시문을 삽입합니다. 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   그런 다음, 클래스에 다음 코드를 삽입합니다.

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   위의 첫 번째 매개 변수를 1단계에서 키에 할당된 API 엔드포인트로 바꿉니다. 예: 
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   두 번째 매개 변수를 1단계에서 얻은 구독 키로 바꿉니다.
   
6. '앱' 프로젝트에서 **AndroidManifest.xml** 파일을 엽니다. 다음 요소를 **매니페스트** 요소의 자식으로 삽입합니다.  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. 이제 응용 프로그램에서 Face API를 호출할 준비가 되었습니다. 

## <a name="step4"></a>4단계: 이미지를 업로드하여 얼굴 감지

얼굴을 감지하는 가장 간단한 방법은 이미지 파일을 직접 업로드하여 [얼굴 - 감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API를 호출하는 것입니다. 클라이언트 라이브러리를 사용하는 경우 **FaceServiceClient** 클래스의 비동기 메서드 **DetectAsync**를 사용하여 이 작업을 수행할 수 있습니다. 반환된 각 얼굴에는 해당 위치를 표시하는 사각형이 일련의 선택적 얼굴 특성과 함께 포함되어 있습니다. 이 예제에서는 얼굴 위치를 검색해야 합니다. 여기서 얼굴 감지를 위해 **MainActivity** 클래스에 메서드를 삽입해야 합니다. 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
                        return null;
                    }
                }
                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };
        detectTask.execute(inputStream);
    }
```

## <a name="step5"></a>5단계: 이미지에서 얼굴 표시

이 마지막 단계에서는 위의 모든 단계를 함께 결합하고 이미지의 프레임을 사용하여 감지된 얼굴을 표시합니다. 먼저 **MainActivity.java**를 열고 사각형을 그리는 도우미 메서드를 삽입합니다. 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
        if (faces != null) {
            for (Face face : faces) {
                FaceRectangle faceRectangle = face.faceRectangle;
                canvas.drawRect(
                        faceRectangle.left,
                        faceRectangle.top,
                        faceRectangle.left + faceRectangle.width,
                        faceRectangle.top + faceRectangle.height,
                        paint);
            }
        }
        return bitmap;
    }
```

이제 얼굴을 포착하고 상태를 보고하기 위해 **detectAndFrame** 메서드에서 TODO 파트를 마무리합니다.

```java
    @Override
    protected void onPreExecute() {
        detectionProgressDialog.show();
    }
    @Override
    protected void onProgressUpdate(String... progress) {
        detectionProgressDialog.setMessage(progress[0]);
    }
    @Override
    protected void onPostExecute(Face[] result) {
        detectionProgressDialog.dismiss();
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
마지막으로 다음과 같이 **onActivityResult** 메서드에서 **detectAndFrame** 메서드에 대한 호출을 추가합니다. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

이 응용 프로그램을 실행하고 얼굴이 포함된 이미지를 찾습니다. 클라우드 API가 응답할 수 있도록 몇 초 동안 기다리세요. 그러면 아래 이미지와 비슷한 결과가 나타납니다. 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> 요약

이 자습서에서는 Face API를 사용하는 기본 프로세스를 배웠으며, 이미지에 얼굴 표식을 표시하는 응용 프로그램을 만들었습니다. Face API에 대한 자세한 내용은 방법 및 [API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 참조하세요. 

## <a name="related"></a> 관련 자습서

- [CSharp에서 Face API 시작 자습서](FaceAPIinCSharpTutorial.md)
- [Python에서 Face API 시작 자습서](FaceAPIinPythonTutorial.md)
