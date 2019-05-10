---
title: '빠른 시작: 사용자 지정 음성 우선 가상 도우미(미리 보기), Java (Windows, Linux) - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java 콘솔 애플리케이션에서 Cognitive Services Speech SDK(소프트웨어 개발 키트)를 사용하는 방법을 알아봅니다. 이전에 Direct Line Speech 채널을 사용하고 음성 우선 가상 도우미 환경을 사용하도록 만들고 구성한 Bot Framework 봇에 애플리케이션을 연결하는 방법을 알아봅니다.
services: cognitive-services
author: bidishac
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: bidishac
ms.openlocfilehash: 83149a8422db25106a97b1711c0ae9ce3c6603eb
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465675"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-with-the-speech-sdk-java"></a>빠른 시작: Speech SDK Java를 사용하여 음성 우선 가상 도우미 만들기

빠른 시작은 [음성 텍스트 변환](quickstart-java-jre.md) 및 [음성 번역](quickstart-translate-speech-java-jre.md)에도 사용할 수 있습니다.

이 문서에서는 [Cognitive Services Speech SDK](speech-sdk.md)를 사용하여 Java 콘솔 애플리케이션을 만듭니다. 이 애플리케이션은 이전에 Direct Line Speech 채널을 사용하도록 만들고 구성한 봇에 연결하고, 음성 요청을 보내고, 음성 응답 작업을 반환합니다(구성된 경우). 이 애플리케이션은 Speech SDK Maven 패키지와 Windows, Ubuntu Linux 또는 macOS 기반의 Eclipse Java IDE를 사용하여 빌드됩니다. 64비트 Java 8 JRE(Java Runtime Environment)에서 실행됩니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에는 다음이 필요합니다.

* 운영 체제: Windows(64비트), Ubuntu Linux 16.04/18.04(64비트) 또는 macOS 10.13 이상
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 또는 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Speech Service에 대한 Azure 구독 키 [무료로 가져올 수 있습니다](get-started.md).
* Bot Framework 버전 4.2 이상을 사용하여 만든 미리 구성된 봇. 봇은 음성 입력을 수신할 수 있도록 새로운 "Direct Line Speech" 채널을 구독해야 합니다.

    > [!NOTE]
    > 미리 보기로 제공되는 Direct Line Speech 채널은 현재 **westus2** Azure 지역만 지원합니다.

    > [!NOTE]
    > [Speech Service 평가판 사용해 보기](get-started.md)에 설명된 표준 가격 계층의 30일 평가판은 **westus**(**westus2** 아님)로 제한되며, 따라서 Direct Line Speech와 호환되지 않습니다. 무료 및 표준 계층 **westus2** 구독은 호환됩니다.

Ubuntu 16.04/18.04를 실행하는 경우 Eclipse를 시작하기 전에 다음과 같은 종속 요소가 설치되어 있는지 확인합니다.

```console
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

Windows(64비트)를 실행하는 경우 플랫폼에 맞는 Microsoft Visual C++ 재배포 가능 패키지가 설치되어 있는지 확인합니다.
* [Visual Studio 2017용 Microsoft Visual C++ 재배포 가능 패키지 다운로드](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="optional-get-started-fast"></a>선택 사항: 빠른 시작

이 빠른 시작에서는 음성 지원 봇에 연결하는 간단한 클라이언트 애플리케이션을 만드는 방법을 단계별로 설명합니다. 바로 시작하려면 이 빠른 시작에 사용되는 즉시 컴파일 가능한 완전한 소스 코드를 사용하면 됩니다. 이 소스 코드는 `quickstart` 폴더의 [Speech SDK 샘플](https://aka.ms/csspeech/samples) 아래에 있습니다.

## <a name="create-and-configure-project"></a>프로젝트 만들기 및 구성

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

또한 로깅을 사용하려면 다음 종속성을 포함하도록 **pom.xml** 파일을 업데이트합니다.

   ```xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.5</version>
    </dependency>
   ```

## <a name="add-sample-code"></a>샘플 코드 추가

1. Java 프로젝트에 새로운 빈 클래스를 추가하려면 **파일** > **새로 만들기** > **클래스**를 선택합니다.

1. **새 Java 클래스** 창에서, **패키지** 필드에 **speechsdk.quickstart**를 입력하고, **이름** 필드에 **기본**을 입력합니다.

   ![새 Java 클래스 창의 스크린샷](media/sdk/qs-java-jre-06-create-main-java.png)

1. 새로 만든 **Main** 클래스를 열고 `Main.java` 파일의 내용을 다음 시작 코드로 바꿉니다.

    ```java
    package speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.BotConnectorConfig;
    import com.microsoft.cognitiveservices.speech.dialog.SpeechBotConnector;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;

    import javax.sound.sampled.AudioFormat;
    import javax.sound.sampled.AudioSystem;
    import javax.sound.sampled.DataLine;
    import javax.sound.sampled.SourceDataLine;
    import java.io.InputStream;

    public class Main {
        final Logger log = LoggerFactory.getLogger(Main.class);

        public static void main(String[] args) {
            // New code will go here
        }

        private void playAudioStream(PullAudioOutputStream audio) {
            ActivityAudioStream stream = new ActivityAudioStream(audio);
            final ActivityAudioStream.ActivityAudioFormat audioFormat = stream.getActivityAudioFormat();
            final AudioFormat format = new AudioFormat(
                    AudioFormat.Encoding.PCM_SIGNED,
                    audioFormat.getSamplesPerSecond(),
                    audioFormat.getBitsPerSample(),
                    audioFormat.getChannels(),
                    audioFormat.getFrameSize(),
                    audioFormat.getSamplesPerSecond(),
                    false);
            try {
                int bufferSize = format.getFrameSize();
                final byte[] data = new byte[bufferSize];

                SourceDataLine.Info info = new DataLine.Info(SourceDataLine.class, format);
                SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);
                line.open(format);

                if (line != null) {
                    line.start();
                    int nBytesRead = 0;
                    while (nBytesRead != -1) {
                        nBytesRead = stream.read(data);
                        if (nBytesRead != -1) {
                            line.write(data, 0, nBytesRead);
                        }
                    }
                    line.drain();
                    line.stop();
                    line.close();
                }
                stream.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }
    ```

1. **main** 메서드에서는 먼저 `BotConnectorConfig`를 구성하고 `SpeechBotConnector` 인스턴스를 만드는 데 사용할 것입니다. 이렇게 하면 Direct Line Speech 채널에 연결되어 봇과 상호 작용하게 됩니다. `AudioConfig` 인스턴스는 오디오 입력의 소스를 지정할 때도 사용됩니다. 이 예제에서는 `AudioConfig.fromDefaultMicrophoneInput()`을 통해 기본 마이크를 사용합니다.

    * `YourSubscriptionKey` 문자열을 해당 구독 키로 바꿉니다. 구독 키는 [여기](get-started.md)서 얻을 수 있습니다.
    * `YourServiceRegion` 문자열을 구독과 연결된 [Azure 지역](regions.md)으로 바꿉니다.
    * `YourChannelSecret` 문자열을 Direct Line Speech 비밀로 바꿉니다.

    > [!NOTE]
    > 미리 보기로 제공되는 Direct Line Speech 채널은 현재 **westus2** Azure 지역만 지원합니다.

    > [!NOTE]
    > [Speech Service 평가판 사용해 보기](get-started.md)에 설명된 표준 가격 계층의 30일 평가판은 **westus**(**westus2** 아님)로 제한되며, 따라서 Direct Line Speech와 호환되지 않습니다. 무료 및 표준 계층 **westus2** 구독은 호환됩니다.

    ```java
    final String channelSecret = "YourChannelSecret"; // Your channel secret
    final String subscriptionKey = "YourSubscriptionKey"; // your subscription key
    final String region = "YourServiceRegion"; // Your service region. Currently assumed to be westus2
    final BotConnectorConfig botConnectorConfig = BotConnectorConfig.fromSecretKey(channelSecret, subscriptionKey, region);

    // Configure audio input from microphone.
    final AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();

    // Create a SpeechjBotConnector instance
    final SpeechBotConnector botConnector = new SpeechBotConnector(botConnectorConfig, audioConfig);
    ```

1. `SpeechBotConnector`는 여러 이벤트를 사용하여 봇 작업, 음성 인식 결과 및 기타 정보를 전달합니다. 그 후에는 다음 이벤트 수신기를 추가합니다.

    ```java
    // Recognizing will provide the intermediate recognized text while an audio stream is being processed
    botConnector.recognizing.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognizing speech event text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // Recognized will provide the final recognized text once audio capture is completed
    botConnector.recognized.addEventListener((o, speechRecognitionResultEventArgs) -> {
        log.info("Recognized speech event reason text: {}", speechRecognitionResultEventArgs.getResult().getText());
    });

    // SessionStarted will notify when audio begins flowing to the service for a turn
    botConnector.sessionStarted.addEventListener((o, sessionEventArgs) -> {
        log.info("Session Started event id: {} ", sessionEventArgs.getSessionId());
    });

    // SessionStopped will notify when a turn is complete and it's safe to begin listening again
    botConnector.sessionStopped.addEventListener((o, sessionEventArgs) -> {
        log.info("Session stopped event id: {}", sessionEventArgs.getSessionId());
    });

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    botConnector.canceled.addEventListener((o, canceledEventArgs) -> {
        log.info("Canceled event details: {}", canceledEventArgs.getErrorDetails());
        botConnector.disconnectAsync();
    });

    // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
    botConnector.activityReceived.addEventListener((o, activityEventArgs) -> {
        final String act = activityEventArgs.getActivity().serialize();
            log.info("Received activity {} audio", activityEventArgs.hasAudio() ? "with" : "without");
            if (activityEventArgs.hasAudio()) {
                playAudioStream(activityEventArgs.getAudio());
            }
        });
    ```

1. `connectAsync()` 메서드를 호출하여 `SpeechBotConnector`를 Direct Line Speech에 연결합니다. 봇을 테스트하려면 `listenOnceAsync` 메서드를 호출하여 마이크로 오디오 입력을 보내면 됩니다. `sendActivityAsync` 메서드를 사용하여 사용자 지정 작업을 직렬화된 문자열로 보낼 수도 있습니다. 이러한 사용자 지정 작업은 봇이 대화에 사용할 추가 데이터를 제공할 수 있습니다.

    ```java
    botConnector.connectAsync();
    // Start listening.
    System.out.println("Say something ...");
    botConnector.listenOnceAsync();

    // botConnector.sendActivityAsync(...)
    ```

1. 변경 내용을 `Main` 파일에 저장합니다.

1. 응답 재생 지원을 쉽게 처리할 수 있도록 getAudio() API에서 반환된 PullAudioOutputStream 개체를 java InputStream으로 변환하는 추가 클래스를 추가합니다. 이 ActivityAudioStream은 "Direct Line 음성 채널"의 오디오 응답을 처리하는 특수 클래스입니다. 재생 처리에 필요한 오디오 형식 정보를 가져오는 접근자를 제공합니다. 이 경우 **파일** > **새로 만들기** > **클래스**를 차례로 선택합니다.

1. **새 Java 클래스** 창에서 **패키지** 필드에는 **speechsdk.quickstart**를 입력하고, **이름** 필드에는 **ActivityAudioStream**을 입력합니다.

1. 새로 만든 **ActivityAudioStream** 클래스를 열고 아래에 제공된 코드로 바꿉니다.

    ```java
    package com.speechsdk.quickstart;

    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;

    import java.io.IOException;
    import java.io.InputStream;


    public final class ActivityAudioStream extends InputStream {
        /**
         * The number of samples played per second. (16 kHz)
         */
        public static final long SAMPLE_RATE = 16000;
        /**
         * The number of bits in each sample of a sound that has this format. (16 bits)
         */
        public static final int BITS_PER_SECOND = 16;
        /**
         * The number of audio channels in this format (1 for mono).
         */
        public static final int CHANNELS = 1;
        /**
         * The number of bytes in each frame of a sound that has this format (2).
         */
        public static final int FRAME_SIZE = 2;

        /**
         * Reads up to a specified maximum number of bytes of data from the audio
         * stream, putting them into the given byte array.
         *
         * @param b   the buffer into which the data is read
         * @param off the offset, from the beginning of array <code>b</code>, at which
         *            the data will be written
         * @param len the maximum number of bytes to read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b, int off, int len) {
            byte[] tempBuffer = new byte[len];
            int n = (int) this.pullStreamImpl.read(tempBuffer);
            for (int i = 0; i < n; i++) {
                if (off + i > b.length) {
                    throw new ArrayIndexOutOfBoundsException(b.length);
                }
                b[off + i] = tempBuffer[i];
            }
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Reads the next byte of data from the activity audio stream if available.
         *
         * @return the next byte of data, or -1 if the end of the stream is reached
         * @see #read(byte[], int, int)
         * @see #read(byte[])
         * @see #available
         * <p>
         */
        @Override
        public int read() {
            byte[] data = new byte[1];
            int temp = read(data);
            if (temp <= 0) {
                // we have a weird situation if read(byte[]) returns 0!
                return -1;
            }
            return data[0] & 0xFF;
        }

        /**
         * Reads up to a specified maximum number of bytes of data from the activity audio stream
         * putting them into the given byte array.
         *
         * @param b the buffer into which the data is read
         * @return the total number of bytes read into the buffer, or -1 if there
         * is no more data because the end of the stream has been reached
         */
        @Override
        public int read(byte[] b) {
            int n = (int) pullStreamImpl.read(b);
            if (n == 0) {
                return -1;
            }
            return n;
        }

        /**
         * Skips over and discards a specified number of bytes from this
         * audio input stream.
         *
         * @param n the requested number of bytes to be skipped
         * @return the actual number of bytes skipped
         * @throws IOException if an input or output error occurs
         * @see #read
         * @see #available
         */
        @Override
        public long skip(long n) {
            if (n <= 0) {
                return 0;
            }
            if (n <= Integer.MAX_VALUE) {
                byte[] tempBuffer = new byte[(int) n];
                return read(tempBuffer);
            }
            long count = 0;
            for (long i = n; i > 0; i -= Integer.MAX_VALUE) {
                int size = (int) Math.min(Integer.MAX_VALUE, i);
                byte[] tempBuffer = new byte[size];
                count += read(tempBuffer);
            }
            return count;
        }

        /**
         * Closes this audio input stream and releases any system resources associated
         * with the stream.
         */
        @Override
        public void close() {
            this.pullStreamImpl.close();
        }

        /**
         * Fetch the audio format for the ActivityAudioStream. The ActivityAudioFormat defines the sample rate, bits per sample and the # channels
         *
         * @return instance of the ActivityAudioFormat associated with the stream
         */
        public ActivityAudioStream.ActivityAudioFormat getActivityAudioFormat() {
            return activityAudioFormat;
        }

        /**
         * Returns the maximum number of bytes that can be read (or skipped over) from this
         * audio input stream without blocking.
         *
         * @return the number of bytes that can be read from this audio input stream without blocking.
         * As this implementation does not buffer this will be defaulted to 0
         */
        @Override
        public int available() {
            return 0;
        }

        public ActivityAudioStream(final PullAudioOutputStream stream) {
            pullStreamImpl = stream;
            this.activityAudioFormat = new ActivityAudioStream.ActivityAudioFormat(SAMPLE_RATE, BITS_PER_SECOND, CHANNELS, FRAME_SIZE, AudioEncoding.PCM_SIGNED);
        }

        private PullAudioOutputStream pullStreamImpl;

        private ActivityAudioFormat activityAudioFormat;

        /**
         * ActivityAudioFormat is an internal format which contains metadata regarding the type of arrangement of
         * audio bits in this activity audio stream.
         */
        static class ActivityAudioFormat {

            private long samplesPerSecond;
            private int bitsPerSample;
            private int channels;
            private int frameSize;
            private AudioEncoding encoding;

            public ActivityAudioFormat(long samplesPerSecond, int bitsPerSample, int channels, int frameSize, AudioEncoding encoding) {
                this.samplesPerSecond = samplesPerSecond;
                this.bitsPerSample = bitsPerSample;
                this.channels = channels;
                this.encoding = encoding;
                this.frameSize = frameSize;
            }

            /**
             * Fetch the number of samples played per second for the associated audio stream format.
             *
             * @return the number of samples played per second
             */
            public long getSamplesPerSecond() {
                return samplesPerSecond;
            }

            /**
             * Fetch the number of bits in each sample of a sound that has this audio stream format.
             *
             * @return the number of bits per sample
             */
            public int getBitsPerSample() {
                return bitsPerSample;
            }

            /**
             * Fetch the number of audio channels used by this audio stream format.
             *
             * @return the number of channels
             */
            public int getChannels() {
                return channels;
            }

            /**
             * Fetch the default number of bytes in a frame required by this audio stream format.
             *
             * @return the number of bytes
             */
            public int getFrameSize() {
                return frameSize;
            }

            /**
             * Fetch the audio encoding type associated with this audio stream format.
             *
             * @return the encoding associated
             */
            public AudioEncoding getEncoding() {
                return encoding;
            }
        }

        /**
         * Enum defining the types of audio encoding supported by this stream
         */
        public enum AudioEncoding {
            PCM_SIGNED("PCM_SIGNED");

            String value;

            AudioEncoding(String value) {
                this.value = value;
            }
        }
    }

    ```

1. 변경 내용을 `ActivityAudioStream` 파일에 저장합니다.

## <a name="build-and-run-the-app"></a>앱 빌드 및 실행

F11 키를 누르거나 **실행** > **디버그**를 선택합니다.
콘솔에 "Say something"이라는 메시지가 표시됩니다. 지금은 문구 또는 문장을 영어로 말해야 봇이 이해할 수 있습니다. Direct Line Speech 채널을 통해 음성이 봇으로 전송되면 봇이 음성을 인식하여 처리하고, 응답이 작업으로 반환됩니다. 봇이 응답으로 음성을 반환하는 경우 `AudioPlayer` 클래스를 사용하여 오디오가 재생됩니다.

![인식에 성공한 후의 콘솔 출력 스크린샷](media/sdk/qs-java-jre-08-console-output.png)

## <a name="next-steps"></a>다음 단계

오디오 파일에서 음성을 읽는 방법 등의 추가 샘플은 GitHub에서 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub에서 Java 샘플 살펴보기](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>참고 항목

- [빠른 시작: 음성 변환, Java(Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
