# AsciiDoc Project

AsciiDoc으로 작성한 문서를 HTML과 PDF로 생성하는 템플릿 프로젝트입니다.
문서 작성 규칙과 요소별 예제는 생성된 문서의 "작성 가이드" 장에 있습니다.

* Asciidoctor Gradle Plugin 사용자 가이드: https://asciidoctor.github.io/asciidoctor-gradle-plugin/master/user-guide/
* Asciidoctor PDF 테마 가이드: https://docs.asciidoctor.org/pdf-converter/latest/theme/
* AsciiDoc 문법 빠른 참조: https://docs.asciidoctor.org/asciidoc/latest/syntax-quick-reference/

## 요구 사항

* JDK 17 이상 (JDK 21 권장)
* Gradle은 별도로 설치할 필요 없음 (Gradle Wrapper 포함)

## 구성 요소와 버전 관리

| 구성 요소 | 버전을 바꾸는 곳 |
|---|---|
| Gradle (Wrapper) | `./gradlew wrapper --gradle-version <버전> --gradle-distribution-sha256-sum <SHA-256>` |
| Asciidoctor Gradle Plugin | `build.gradle` 의 `plugins` 블록 |
| AsciidoctorJ, AsciidoctorJ PDF | `build.gradle` 의 `asciidoctorjVersion`, `asciidoctorjPdfVersion` |

현재 버전은 생성된 문서의 "전제 조건" 절에 표시됩니다.
`build.gradle` 이 버전 값을 문서 속성(`gradle-version`, `asciidoctorj-pdf-version` 등)으로 넘기므로 문서 본문은 따로 고칠 필요가 없습니다.

Gradle Wrapper 는 `gradle-wrapper.properties` 의 `distributionSha256Sum` 으로 내려받은 배포판을 검증합니다.
SHA-256 값은 https://gradle.org/release-checksums/ 에서 확인하십시오.

Dependabot(`.github/dependabot.yml`)이 Gradle 플러그인과 GitHub Actions 의 새 버전을 매주 확인해 PR 을 만듭니다.

## 디렉터리 구조

```
.
├── .github/
│   ├── workflows/docs.yml      # push, PR 마다 문서를 빌드하고 ZIP 을 결과물로 올리는 CI
│   └── dependabot.yml          # 의존성 버전 자동 업데이트
├── .gitattributes              # 줄바꿈(gradlew 는 LF), 바이너리 파일 지정
├── build.gradle                # Asciidoctor 설정, 공통 문서 속성, 배포 ZIP 태스크
├── gradle.properties           # 문서 버전(version)과 Gradle 실행 옵션
├── gradlew, gradlew.bat        # Gradle Wrapper 실행 스크립트
├── gradle/wrapper/             # Gradle Wrapper 설정 (버전, 체크섬)
└── src/docs/
    ├── asciidoc/
    │   ├── index.adoc          # 문서 헤더(속성)와 장 include 목록
    │   ├── chapters/           # 장(chapter)별 파일. 2단계 제목(==)으로 시작
    │   │   ├── introduction.adoc
    │   │   ├── getting-started.adoc
    │   │   ├── writing-guide.adoc
    │   │   └── appendix.adoc
    │   ├── images/             # 그림. 문서에서는 파일 이름만 적음 (:imagesdir: images)
    │   ├── examples/           # 코드 블록에 include 할 설정/소스 예제
    │   └── docinfo.html        # HTML 출력에만 덧붙는 스타일(한국어 글꼴)
    └── theme/
        ├── DataDynamics-theme.yml    # 기본 PDF 테마. 기본 테마를 extends 하고 바꾸는 항목만 정의
        ├── KaiGenGothicKR-theme.yml  # 대체 PDF 테마 (KaiGen Gothic KR + Roboto Mono)
        └── *.ttf                     # PDF 에 내장할 글꼴 (아래 "글꼴" 참고)
```

## 문서 생성

```
./gradlew build
```

생성 결과는 다음 위치에 만들어집니다.

* HTML: `build/docs/asciidoc/index.html`
* PDF: `build/docs/asciidocPdf/index.pdf`
* 배포 ZIP: `build/distributions/<프로젝트명>-<버전>.zip`

배포 ZIP 의 구성은 다음과 같습니다.

```
reference/
├── pdf/<프로젝트명>-reference.pdf
└── htmlsingle/
    ├── <프로젝트명>-reference.html
    └── images/
```

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

다른 테마를 쓰려면 `build.gradle` 의 `asciidoctorPdf` 블록에서 `pdf-theme` 값을 바꿉니다.
예를 들어 대체 테마를 쓰려면 `DataDynamics-theme.yml` 을 `KaiGenGothicKR-theme.yml` 로 바꾸십시오.

## 글꼴

`src/docs/theme/` 에는 두 PDF 테마의 `font.catalog` 가 참조하는 글꼴만 들어 있습니다.

| 글꼴 | 파일 | 사용처 |
|---|---|---|
| KaiGen Gothic KR | `KaiGenGothicKR-Regular`, `-Bold`, `-Regular-Italic`, `-Bold-Italic` | 두 테마의 본문, 제목 |
| D2Coding | `D2Coding`, `D2CodingBold` | DataDynamics 테마의 코드 |
| Roboto Mono | `RobotoMono-Regular`, `-Bold`, `-Italic`, `-BoldItalic` | KaiGenGothicKR 테마의 코드 |

Noto Serif 와 M+ 1mn 은 Asciidoctor PDF 에 포함된 글꼴을 사용합니다 (`pdf-fontsdir` 의 `GEM_FONTS_DIR`).
DataDynamics 테마는 Noto Serif 를 대체 글꼴로 지정해, Asciidoctor PDF 가 URL 줄바꿈을 위해 넣는 제로폭 공백(U+200B)을 처리합니다.

새 글꼴을 추가할 때는 파일을 `src/docs/theme/` 에 넣고 테마의 `font.catalog` 에 등록하십시오.
catalog 에 등록하지 않은 글꼴 파일은 사용되지 않으니 저장소에 넣지 마십시오.
