## <a name="build-iot-edge"></a>IoT Edge 빌드

이 자습서에서는 사용자 지정 IoT Edge 모듈을 사용하여 미리 구성된 원격 모니터링 솔루션과 통신합니다. 따라서 사용자 지정 소스 코드에서 IoT Edge 모듈을 빌드해야 합니다. 다음 섹션에서는 IoT Edge를 설치하고 사용자 지정 IoT Edge 모듈을 빌드하는 방법에 대해 설명합니다.

### <a name="install-iot-edge"></a>IoT Edge 설치

다음 단계에서는 미리 컴파일된 IoT Edge 소프트웨어를 Intel NUC에 설치하는 방법을 설명합니다.

1. Intel NUC에서 다음 명령을 실행하여 필요한 스마트 패키지 리포지토리를 구성합니다.

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    명령에서 **이 채널을 포함하시겠습니까?**라는 메시지가 표시되면 `y`를 입력합니다.

1. 다음 명령을 실행하여 스마트 패키지 관리자를 업데이트합니다.

    ```bash
    smart update
    ```

1. 다음 명령을 실행하여 Azure IoT Edge 패키지를 설치합니다.

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. "헬로 월드" 샘플을 실행하여 설치를 확인합니다. 이 샘플에서는 5초마다 헬로 월드 메시지를 log.txT 파일에 씁니다. 다음 명령은 "헬로 월드" 샘플을 실행합니다.

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    샘플을 중지할 때 표시되는 **잘못된 인수** 메시지는 무시합니다.

    다음 명령을 사용하여 로그 파일의 내용을 봅니다.

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>문제 해결

"패키지에서 util-linux-dev를 제공하지 않습니다."라는 오류가 표시되면 Intel NUC를 다시 부팅해 보세요.
