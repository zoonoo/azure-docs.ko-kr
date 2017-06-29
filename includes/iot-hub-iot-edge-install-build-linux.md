## <a name="install-the-prerequisites"></a>필수 구성 요소 설치

이 자습서의 단계에서는 Ubuntu Linux를 실행 중이라고 가정합니다.

셸을 열고 다음 명령을 실행하여 필수 구성 요소 패키지를 설치합니다.

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

셸에서 다음 명령을 실행하여 로컬 컴퓨터에 Azure IoT Edge GitHub 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>샘플을 빌드하는 방법

이제 로컬 컴퓨터에 IoT Edge 런타임과 샘플을 빌드할 수 있습니다.

1. 셸을 엽니다.

1. **iot-edge** 리포지토리의 로컬 복사본에 있는 루트 폴더로 이동합니다.

1. 빌드 스크립트를 다음과 같이 실행합니다.

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

이 스크립트는 **cmake** 유틸리티를 사용하여 **iot-edge** 리포지토리의 로컬 복사본 루트 폴더에 **build**라는 폴더를 만들고 메이크파일을 생성합니다. 그러면 스크립트에서 솔루션을 빌드하고 단위 테스트 및 종단간 테스트를 건너뜁니다. 단위 테스트를 빌드하고 실행하려는 경우 `--run-unittests` 매개 변수를 추가합니다. 종단간 테스트를 빌드하고 실행하려는 경우 `--run-e2e-tests`를 추가합니다.

> [!NOTE]
> **build.sh** 스크립트를 실행할 때마다 **iot-edge** 리포지토리의 로컬 복사본 루트 폴더에서 **build** 폴더를 삭제한 다음 다시 만듭니다.