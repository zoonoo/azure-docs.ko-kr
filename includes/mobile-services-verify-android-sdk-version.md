지속적인 개발 때문에 Eclipse에 설치된 Android SDK 버전이 코드의 버전과 일치하지 않을 수 있습니다. 이 자습서에서 언급한 Android SDK 버전은 이 문서 작성 시 최신 버전인 19입니다. 버전 번호는 SDK의 새 릴리스가 공개되면 높아질 수 있으며, 사용 가능한 최신 버전을 사용하는 것이 좋습니다.

버전 불일치의 두 가지 증상은 다음과 같습니다.

1.  Eclipse 콘솔의 맨 아래 창을 살펴봅니다. "**Unable to resolve target 'android-n'**"과 같은 오류 메시지를 확인할 수 있습니다.

2.  `import` 문을 기반으로 확인되는 코드의 표준 Android 개체에서 오류 메시지를 생성할 수 있습니다.

위와 같은 증상 중 하나가 나타나는 경우 Eclipse에 설치된 Android SDK의 버전이 다운로드한 프로젝트의 SDK 대상과 일치하지 않을 수 있습니다. 버전을 확인하려면 다음을 변경하세요.

1.  Eclipse에서 **Window**를 클릭한 후 **Android SDK Manager**를 클릭합니다. 최신 버전의 SDK 플랫폼을 설치하지 않은 경우 클릭하여 설치합니다. 버전 번호를 기록해 둡니다.

2.  프로젝트 파일 **AndroidManifest.xml**을 엽니다. **uses-sdk** 요소에서 **targetSdkVersion**을 설치된 최신 버전으로 설정하세요. **uses-sdk** 태그는 다음과 유사할 수 있습니다.

            <uses-sdk
                android:minSdkVersion="8"
                android:targetSdkVersion="19" />

3.  Eclipse Package Explorer에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하여 **Properties**를 선택하고 왼쪽 열에서 **Android**를 선택합니다. **Project Build Target**을 **targetSdkVersion**과 동일한 SDK 버전으로 설정하세요.



