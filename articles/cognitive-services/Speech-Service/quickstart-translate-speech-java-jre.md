---
title: '빠른 시작: 음성 변환, Java(Windows, Linux) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 사용자 음성을 캡처하고, 다른 언어로 변환하고, 명령줄에 텍스트를 출력하는 간단한 Java 애플리케이션을 만듭니다. 이 가이드는 Windows 및 Linux 사용자를 위해 설계되었습니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: e0c5139203b360be1eed2292d3ca07c9948b2ca5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729670"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-java"></a>빠른 시작: Java용 Speech SDK를 사용하여 음성 번역

이 빠른 시작에서는 컴퓨터의 마이크에서 사용자 음성을 캡처하고, 음성을 변환하고, 변환된 텍스트를 실시간으로 명령줄에 변환하는 간단한 Java 애플리케이션을 만듭니다. 이 애플리케이션은 64비트 Windows 또는 64비트 Ubuntu Linux 16.04/18.04에서 실행되도록 설계되었으며 Speech SDK Maven 패키지 및 Eclipse Java IDE를 사용하여 빌드됩니다.

음성 변환에 사용할 수 있는 언어의 전체 목록은 [언어 지원](language-support.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* 운영 체제: 64비트 Windows 또는 64비트 Ubuntu Linux 16.04/18.04
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 또는 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).

Ubuntu 16.04/18.04를 실행하는 경우 Eclipse를 시작하기 전에 이러한 종속성이 설치되어 있는지 확인합니다.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
```

> [!NOTE]
> Speech Devices SDK 및 Roobo 디바이스에 대한 내용은 [Speech Devices SDK](speech-devices-sdk.md)를 참조하세요.

## <a name="create-and-configure-project"></a>프로젝트 만들기 및 구성

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>샘플 코드 추가

1. Java 프로젝트에 새로운 빈 클래스를 추가하려면 **파일** > **새로 만들기** > **클래스**를 선택합니다.

1. **새 Java 클래스** 창에서, **패키지** 필드에 **speechsdk.quickstart**를 입력하고, **이름** 필드에 **기본**을 입력합니다.

   ![새 Java 클래스 창의 스크린샷](media/sdk/qs-java-jre-06-create-main-java.png)

1. `Main.java`의 모든 코드를 다음 코드 조각으로 바꿉니다.

   ```java
   package speechsdk.quickstart;

   import java.io.IOException;
   import java.util.Map;
   import java.util.Scanner;
   import java.util.concurrent.ExecutionException;
   import com.microsoft.cognitiveservices.speech.*;
   import com.microsoft.cognitiveservices.speech.translation.*;

   public class Main {

       public static void translationWithMicrophoneAsync() throws InterruptedException, ExecutionException, IOException
       {
           // Creates an instance of a speech translation config with specified
           // subscription key and service region. Replace with your own subscription key
           // and service region (e.g., "westus").
           SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

           // Sets source and target language(s).
           String fromLanguage = "en-US";
           config.setSpeechRecognitionLanguage(fromLanguage);
           config.addTargetLanguage("de");

           // Sets voice name of synthesis output.
           String GermanVoice = "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda)";
           config.setVoiceName(GermanVoice);

           // Creates a translation recognizer using microphone as audio input.
           TranslationRecognizer recognizer = new TranslationRecognizer(config);
           {
               // Subscribes to events.
               recognizer.recognizing.addEventListener((s, e) -> {
                   System.out.println("RECOGNIZING in '" + fromLanguage + "': Text=" + e.getResult().getText());

                   Map<String, String> map = e.getResult().getTranslations();
                   for(String element : map.keySet()) {
                       System.out.println("    TRANSLATING into '" + element + "': " + map.get(element));
                   }
               });

               recognizer.recognized.addEventListener((s, e) -> {
                   if (e.getResult().getReason() == ResultReason.TranslatedSpeech) {
                       System.out.println("RECOGNIZED in '" + fromLanguage + "': Text=" + e.getResult().getText());

                       Map<String, String> map = e.getResult().getTranslations();
                       for(String element : map.keySet()) {
                           System.out.println("    TRANSLATED into '" + element + "': " + map.get(element));
                       }
                   }
                   if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
                       System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
                       System.out.println("    Speech not translated.");
                   }
                   else if (e.getResult().getReason() == ResultReason.NoMatch) {
                       System.out.println("NOMATCH: Speech could not be recognized.");
                   }
               });

               recognizer.synthesizing.addEventListener((s, e) -> {
                   System.out.println("Synthesis result received. Size of audio data: " + e.getResult().getAudio().length);
               });

               recognizer.canceled.addEventListener((s, e) -> {
                   System.out.println("CANCELED: Reason=" + e.getReason());

                   if (e.getReason() == CancellationReason.Error) {
                       System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
                       System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
                       System.out.println("CANCELED: Did you update the subscription info?");
                   }
               });

               recognizer.sessionStarted.addEventListener((s, e) -> {
                   System.out.println("\nSession started event.");
               });

               recognizer.sessionStopped.addEventListener((s, e) -> {
                   System.out.println("\nSession stopped event.");
               });

               // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
               System.out.println("Say something...");
               recognizer.startContinuousRecognitionAsync().get();

               System.out.println("Press any key to stop");
               new Scanner(System.in).nextLine();

               recognizer.stopContinuousRecognitionAsync().get();
           }
       }

       public static void main(String[] args) {
           try {
               translationWithMicrophoneAsync();
           } catch (Exception ex) {
               System.out.println("Unexpected exception: " + ex.getMessage());
               assert(false);
               System.exit(1);
           }
       }
   }
   ```

1. 문자열 `YourSubscriptionKey`를 구독 키로 바꿉니다.

1. 문자열 `YourServiceRegion`을 구독과 연결된 [지역](regions.md)으로 바꿉니다(예를 들어 평가판 구독에 대해 `westus`).

1. 프로젝트에 대한 변경 내용을 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

F11 키를 누르거나 **실행** > **디버그**를 선택합니다.

마이크에서 나오는 음성 입력이 독일어로 작성되어 콘솔 창에 기록됩니다. 음성 캡처를 중지하려면 "Enter" 키를 누릅니다.

![인식에 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-translate-java-jre-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽고, 합성된 음성으로 변환된 텍스트를 출력하는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 Java 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [빠른 시작: 음성 인식, Java(Windows, Linux)](quickstart-java-jre.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
