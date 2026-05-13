# 1편. 웹 스크래핑 입문: 필요한 정보 빠르게 모으기

## 들어가며

데이터 기반으로 글을 쓰거나 간단한 분석을 할 때, 가장 먼저 필요한 것은 **원천 데이터 수집**입니다.  
이번 글에서는 Python 표준 라이브러리만 사용해서 웹 페이지에서 핵심 정보(제목, 헤더, 링크)를 수집하는 방법을 소개합니다.

> 대상 독자: Python 기초 문법을 알고 있고, 웹 스크래핑을 처음 시작하는 분

---

## 왜 표준 라이브러리로 시작할까?

처음부터 무거운 도구를 쓰기보다, 표준 라이브러리로 시작하면 구조를 이해하기 좋습니다.

- `urllib.request`: 웹 페이지 HTML 가져오기
- `html.parser.HTMLParser`: 태그를 해석해 원하는 데이터 추출

이 방식은 학습용으로 매우 좋고, 간단한 자동화에는 충분히 실용적입니다.

---

## 이번 글에서 만들 도구

CLI로 실행되는 간단한 스크래퍼를 만듭니다.

```bash
python scraper_app.py https://example.com --max-links 5
```

출력 예시(요약):

- TITLE: Example Domain
- H1: Example Domain
- LINK: https://iana.org/domains/example

---

## 구현 핵심 아이디어

### 1) HTML 가져오기

`User-Agent` 헤더를 포함해 요청을 보내고, 응답 인코딩을 고려해 문자열로 변환합니다.

```python
req = Request(url, headers={"User-Agent": "Mozilla/5.0 (compatible; SimpleScraper/1.0)"})
with urlopen(req, timeout=10) as response:
    charset = response.headers.get_content_charset() or "utf-8"
    html = response.read().decode(charset, errors="replace")
```

### 2) 필요한 태그만 파싱

`HTMLParser`를 상속받아 아래만 추출합니다.

- `<title>`
- `<h1>`, `<h2>`, `<h3>`
- `<a href="...">`

상대 경로 링크는 `urljoin`으로 절대 URL로 변환하면 재사용성이 높습니다.

### 3) 사용자 친화적 출력

- 섹션별(HEADINGS, LINKS) 구분
- 링크 중복 제거
- `--max-links` 옵션으로 출력 길이 제어

---

## 자주 만나는 오류와 해결

1. **인코딩 문제**
   - 증상: 한글이 깨져 보임
   - 대응: 응답 헤더의 charset을 우선 사용하고, 없으면 `utf-8` 기본값 적용

2. **네트워크/접속 오류**
   - 증상: 타임아웃, DNS 오류
   - 대응: `try/except`로 잡아 사용자에게 명확한 메시지 출력

3. **수집 데이터 없음**
   - 증상: 헤더/링크가 비어 있음
   - 대응: 빈 결과도 정상 케이스로 처리하고 안내 문구 출력

---

## 마무리

이번 1편에서는 스크래핑의 가장 기본적인 뼈대를 만들었습니다.  
다음 2편에서는 수집한 데이터를 **CSV 형태로 정리하고 품질 점검하는 방법**을 다룰 예정입니다.

---

## 다음 글 예고

**2편. CSV 데이터 가공: 분석 가능한 형태로 정리하기**

- 컬럼 설계 원칙
- 결측치/중복치 처리
- 재사용 가능한 CSV 로더 함수 작성
