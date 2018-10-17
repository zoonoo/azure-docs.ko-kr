---
title: '자습서: 이미지에서 얼굴 감지 및 포착 - Face API, Android용 Java'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Face API를 사용하여 이미지에서 얼굴을 감지하고 포착하는 샘플 Android 앱을 만듭니다.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: 50185588b77f01bced9462d5fd1ad67bb5de6e08
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129729"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>자습서: 이미지에서 얼굴을 감지하고 포착하는 Android 앱 만들기

이 자습서에서는 Face 서비스 Java 클래스 라이브러리를 사용하여 이미지에서 사람 얼굴을 감지하는 간단한 Android 응용 프로그램을 만듭니다. 응용 프로그램은 선택된 이미지를 감지된 각 얼굴을 사각형으로 포착하여 보여줍니다. 전체 샘플 코드는 [Android의 이미지에서 얼굴 감지 및 포착](https://github.com/Azure-Samples/cognitive-services-face-android-sample)의 GitHub에서 사용할 수 있습니다.

![빨간색 직사각형으로 포착된 얼굴이 있는 사진의 Android 스크린샷](../Images/android_getstarted2.1.PNG)

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> - Android 응용 프로그램 만들기
> - Face 서비스 클라이언트 라이브러리 설치
> - 클라이언트 라이브러리를 사용하여 이미지에서 얼굴 감지
> - 감지된 각 얼굴 주위에 프레임 그리기

## <a name="prerequisites"></a>필수 조건

- 샘플을 실행하려면 구독 키가 있어야 합니다. [Cognitive Services 시도](https://azure.microsoft.com/try/cognitive-services/?api=face-api)에서 평가판 구독 키를 가져올 수 있습니다.
- 최소 SDK 22의 [Android Studio](https://developer.android.com/studio/)(Face 클라이언트 라이브러리에 필요).
- Maven의 [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) Face 클라이언트 라이브러리. 패키지를 다운로드할 필요는 없습니다. 설치 지침은 아래에 제공됩니다.

## <a name="create-the-project"></a>프로젝트 만들기

다음 단계에 따라 Android 응용 프로그램 프로젝트를 만듭니다.

1. Android Studio를 엽니다. 이 자습서는 Android Studio 3.1을 사용합니다.
1. **새 Android Studio 프로젝트 시작**을 선택합니다.
1. **Android 프로젝트 만들기** 화면에서 필요한 경우 기본 필드를 수정한 다음, **다음**을 클릭합니다.
1. **대상 Android 장치** 화면에서 드롭다운 선택기를 사용하여 **API 22** 이상을 선택한 다음, **다음**을 클릭합니다.
1. **빈 활동**을 선택한 다음, **다음**을 클릭합니다.
1. **이전 버전과의 호환성**을 선택 취소한 다음, **마침**을 클릭합니다.

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>이미지를 선택하고 표시하기 위한 UI 만들기

*activity_main.xml*을 열면 레이아웃 편집기가 표시됩니다. **텍스트** 탭을 선택하고 콘텐츠를 다음 코드로 바꿉니다.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

*MainActivity.java*를 연 다음, 첫 번째 `package` 명령문을 제외한 모든 항목을 다음 코드로 바꿉니다.

이 코드는 사용자가 그림을 선택할 수 있도록 새로운 활동을 시작하는 이벤트 처리기를 `Button`에 설정합니다. 선택하면 그림이 `ImageView`에 표시됩니다.

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

public class MainActivity extends Activity {
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
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

이제 앱은 사진을 찾고 아래 이미지와 비슷하게 창에 표시할 수 있습니다.

![얼굴이 있는 사진의 Android 스크린샷](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Face 클라이언트 라이브러리 구성

Face API는 HTTPS 요청을 사용하여 호출할 수 있는 클라우드 API입니다. 이 자습서에서는 이러한 웹 요청을 캡슐화하는 Face 클라이언트 라이브러리를 사용하여 작업을 간소화합니다.

**Project** 창에서 드롭다운 선택기를 사용하여 **Android**를 선택합니다. **Gradle 스크립트**를 확장한 다음, *build.gradle(Module: app)* 을 엽니다.

아래 스크린샷처럼 Face 클라이언트 라이브러리 `com.microsoft.projectoxford:face:1.4.3`에 대한 종속성을 추가한 다음, **지금 동기화**를 클릭합니다.

![앱 build.gradle 파일의 Android Studio 스크린샷](../Images/face-tut-java-gradle.png)

**MainActivity.java**를 열고 다음 Import 지시문을 추가합니다.

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Face 클라이언트 라이브러리 코드 추가

`onCreate` 메서드 위의 `MainActivity` 클래스에 다음 코드를 삽입합니다.

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

`<API endpoint>`를 키에 할당된 API 엔드포인트로 바꿉니다. 평가판 구독 키는 **westcentralus** 영역에서 생성됩니다. 평가판 구독 키를 사용하는 경우 다음과 같은 문구가 표시됩니다.

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

`<Subscription Key>`를 구독 키로 바꿉니다. 예: 

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

**프로젝트** 창에서 **앱**, **매니페스트**를 확장하고 *AndroidManifest.xml*을 엽니다.

다음 요소를 `manifest` 요소의 직계 하위 항목으로 삽입합니다.

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

오류를 확인하기 위해 프로젝트를 빌드합니다. 이제 Face 서비스를 호출할 준비가 되었습니다.

## <a name="upload-an-image-to-detect-faces"></a>얼굴을 감지할 이미지 업로드

얼굴을 감지하는 가장 간단한 방법은 `FaceServiceClient.detect` 메서드를 호출하는 것입니다. 이 메서드는 API [감지](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 메서드를 래핑하고 `Face`의 배열을 반환합니다.

반환된 각 `Face`에는 해당 위치를 표시하는 사각형이 일련의 선택적 얼굴 특성과 함께 포함되어 있습니다. 이 예제에서는 얼굴 위치만 필요합니다.

오류가 발생하면 `AlertDialog`가 근본적인 이유를 표시합니다.

`MainActivity` 클래스에 다음 메서드를 삽입합니다.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
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

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>이미지에서 얼굴 포착

`MainActivity` 클래스에 다음과 같은 도우미 메서드를 삽입합니다. 이 메서드는 감지된 각 얼굴 주위에 사각형을 그립니다.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
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

`detectAndFrame` 메서드에서 `TODO` 주석으로 표시된 `AsyncTask` 메서드를 완료합니다. 성공하면 선택한 이미지가 `ImageView`에 얼굴이 포착된 상태로 표시됩니다.

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
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

마지막으로 아래와 같이 `onActivityResult` 메서드에서 `detectAndFrame` 메서드 호출의 주석 처리를 제거합니다.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>앱 실행

응용 프로그램을 실행하고 얼굴이 있는 이미지를 찾습니다. Face 서비스가 응답할 수 있도록 몇 초 동안 기다립니다. 그러면 아래 이미지와 비슷한 결과가 나타납니다.

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>요약

이 자습서에서는 Face 서비스를 사용하는 기본 프로세스를 배웠으며, 이미지에서 포착된 얼굴을 표시하는 응용 프로그램을 만들었습니다.

## <a name="next-steps"></a>다음 단계

얼굴 이정표를 탐지하고 사용하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [이미지에서 얼굴을 감지하는 방법](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

포즈, 성별, 나이, 머리 포즈, 수염, 안경 등 얼굴 및 그 속성을 감지하는 데 사용되는 Face API에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Face API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).