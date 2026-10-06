# AsciiDoc Project

AsciiDoc으로 작성한 문서를 HTML과 PDF로 생성하는 템플릿 프로젝트입니다.
문서 작성 규칙과 요소별 예제는 생성된 문서의 "작성 가이드" 장에 있습니다.

* Asciidoctor Gradle Plugin 사용자 가이드: https://asciidoctor.github.io/asciidoctor-gradle-plugin/master/user-guide/
* Asciidoctor PDF 테마 가이드: https://docs.asciidoctor.org/pdf-converter/latest/theme/
* AsciiDoc 문법 빠른 참조: https://docs.asciidoctor.org/asciidoc/latest/syntax-quick-reference/

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

## 디렉터리 구조

```
src/docs/
├── asciidoc/
│   ├── index.adoc          # 문서 헤더(속성)와 장 include 목록
│   ├── chapters/           # 장(chapter)별 파일. 2단계 제목(==)으로 시작
│   ├── images/             # 그림. 문서에서는 파일 이름만 적음 (:imagesdir: images)
│   ├── examples/           # 코드 블록에 include 할 설정/소스 예제
│   └── docinfo.html        # HTML 출력에만 덧붙는 스타일(한국어 글꼴)
└── theme/
    ├── DataDynamics-theme.yml   # PDF 테마. 기본 테마를 extends 하고 바꾸는 항목만 정의
    └── *.ttf                    # PDF 에 내장할 글꼴
```

## 문서 생성

```
./gradlew build
```

생성 결과는 다음 위치에 만들어집니다.

* HTML: `build/docs/asciidoc/index.html`
* PDF: `build/docs/asciidocPdf/index.pdf`
* 배포 ZIP: `build/distributions/` (HTML, PDF 가 `<프로젝트명>-reference.*` 이름으로 담김)

HTML 또는 PDF만 따로 생성하려면 `./gradlew asciidoctor` 또는 `./gradlew asciidoctorPdf`를 사용하십시오.

만약에 Gradle 캐쉬로 문서 생성이 잘 되지 않는다면 다음과 같이 빌드 결과를 삭제한 후 다시 생성하십시오.

```
./gradlew clean build
```

## 버전과 날짜

문서 표지의 버전은 `gradle.properties` 의 `version` 값이고, 날짜는 빌드 시점의 날짜입니다.
`build.gradle` 이 `revnumber`, `revdate` 속성으로 전달하므로 `index.adoc` 의 값을 직접 고칠 필요가 없습니다.

## 새 장 추가하기

1. `src/docs/asciidoc/chapters/` 에 파일을 만듭니다. 파일 이름은 소문자와 하이픈만 사용합니다.
2. 첫 줄에 `[[anchor]]` 앵커와 `== 제목` 을 씁니다.
3. `index.adoc` 끝에 `include::chapters/<파일>.adoc[]` 을 추가합니다.

## 빌드 실패 기준

깨진 상호 참조, 정의되지 않은 속성, 없는 include 파일, 없는 그림은 모두 **빌드 실패**로 처리됩니다
(`failureLevel = 'WARN'`, `attribute-missing = warn`).
빌드 로그의 `asciidoctor: WARNING` 줄에서 원인을 확인하십시오.

코드 블록 안에 `include::` 나 `<<xref>>` 를 예시로 적을 때는 `\include::`, `` `+<<xref>>+` `` 처럼 이스케이프해야 합니다.

## PDF 테마 바꾸기

`src/docs/theme/DataDynamics-theme.yml` 은 Asciidoctor PDF 기본 테마를 상속(`extends: default`)합니다.
색상은 `brand` 섹션의 `primary`, `secondary`, `accent` 값만 바꾸면 제목, 링크, 표 머리글, 표지에 함께 반영됩니다.
글꼴을 바꾸려면 `font.catalog` 에 글꼴 파일을 등록하고 `base.font_family` 를 수정하십시오.
굵은 글꼴 파일이 따로 없는 글꼴은 제목이 굵게 표시되지 않으니 Regular/Bold 쌍이 있는 글꼴을 사용하십시오.
