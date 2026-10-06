# AsciiDoc Project

AsciiDoc으로 작성한 문서를 HTML과 PDF로 생성하는 템플릿 프로젝트입니다.

* Asciidoctor Gradle Plugin 사용자 가이드: https://asciidoctor.github.io/asciidoctor-gradle-plugin/master/user-guide/
* Asciidoctor PDF 테마 가이드: https://docs.asciidoctor.org/pdf-converter/latest/theme/

## 요구 사항

* JDK 17 이상 (JDK 21 권장)
* Gradle은 별도로 설치할 필요 없음 (Gradle Wrapper 포함)

## 구성

| 구성 요소 | 버전 |
|---|---|
| Gradle (Wrapper) | 9.8.0 |
| Asciidoctor Gradle Plugin | 4.0.5 |
| AsciidoctorJ | 3.0.1 |
| AsciidoctorJ PDF | 2.3.27 |

## 문서 생성

AsciiDoc으로 작성한 문서를 생성하기 위해서 다음의 커맨드를 사용합니다.

```
./gradlew build
```

생성 결과는 다음 위치에 만들어집니다.

* HTML: `build/docs/asciidoc/index.html`
* PDF: `build/docs/asciidocPdf/index.pdf`
* 배포 ZIP: `build/distributions/`

HTML 또는 PDF만 따로 생성하려면 `./gradlew asciidoctor` 또는 `./gradlew asciidoctorPdf`를 사용하십시오.

만약에 Gradle 캐쉬로 문서 생성이 잘 되지 않는다면 다음과 같이 빌드 결과를 삭제한 후 다시 생성하십시오.

```
./gradlew clean build
```
