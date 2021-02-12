# AsciiDoc Project

https://asciidoctor.github.io/asciidoctor-gradle-plugin/master/user-guide/

## 문서 생성

AsciiDoc으로 작성한 문서를 생성하기 위해서 다음의 커맨드를 사용합니다.

```
gradle build --console=verbose --no-build-cache
```

만약에 Gradle 캐쉬로 문서 생성이 잘 되지 않는다면 다음과 같이 Gradle 캐쉬를 삭제하십시오.

```
rm -rf .gradle
rm -rf build
```
