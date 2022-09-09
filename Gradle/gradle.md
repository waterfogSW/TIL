## Gradle

Gradle 은 DAG(비순환 그래프)의 형태로 의존성을 찾아 빌드하는 빌드 자동화 도구다

![image](https://user-images.githubusercontent.com/28651727/189249018-57d585f0-d222-497c-a9df-a1a6fdfbaa6f.png)

gradle 패키지 매니저 설치(mac os)

```groovy
brew install gradle
```

## Task

gradle의 task를 통해 소스코드 빌드, 테스트, 린트 등의 다양한 작업을 수행할 수 있다. gradle에서 유저는 쉽게 task를 정의하고 실행할 수 있다.

간단한 task 예시

```groovy
tasks.register('hello') {
    doLast {
        println 'Hello world!'
    }
}
```

많은 경우 직접 Task를 정의 할 일이 없다. Gradle의 Java 플러그인이 유용한 Task들을 미리 정의해주기 때문에 보통은 이미 정의된 task를 수정만 하면 된다.

Gradle의 BasePlugin에는 build, assemble, check 등의 task들이 정의되어있다. 이들은 [Lifecycle task](https://docs.gradle.org/current/userguide/more_about_tasks.html#sec:lifecycle_tasks)라고 부른다. 우리가 `./gradlew build`를 호출하면 실행되는 task가 바로 이 build task다.

build.gradle에 특별한 내용이 없어도 `./gradlew build`를 하면 build가 된다.

일반적인 자바 프로젝트에서 사용하는 build.gradle파일을 보면 다음 코드조각처럼 java 플러그인을 사용한다.

```
plugins {
    id 'java'
}
```

java 플러그인은 내부에서 BasePlugin을 로드한다. BasePlugin은 build task를 정의한다. java 플러그인은 jar task를 정의한 뒤 build task가 jar task에 의존하게 만든다.

## Gradle Wrapper

`gradle init` 또는 `gradle wrapper` 명령어를 사용하면 다음과 같은 디렉토리 구조가 생성된다.

```bash
.
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
└── gradlew.bat
```

gradle wrapper는 환경에 종속되지않고 빌드할 수 있도록 도와준다.

**`gradle-wrapper.jar`**

환경에 맞춰 gradle 배포를 다운로드받을 수 있는 코드가 포함된 jar파일이다. 

**`gradle-wrapper.properties`**

Wrapper 설정파일이다. 

**`gradlew`, `gradlew.bat`**

build를 수행하기 위한 쉘 스크립트와 윈도우 배치 스크립트이다.