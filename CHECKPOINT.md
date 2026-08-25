# CHECKPOINT — 사회적협동조합 신안올리브협회 웹사이트

최종 갱신: 2026-08-25 (QA-1 배치)

---

## 1. 이 사이트는 무엇인가

- 사회적협동조합 신안올리브협회 공식 홈페이지 (https://sinan-olive.org)
- **정관 제6조에 따른 조합의 공식 공고 채널.** 단순 홍보 사이트가 아니라
  법적 공고 효력이 걸린 채널이므로, `notice.html`의 공고 전문
  (일시·장소·조합원 자격요건·의결사항·공고일·발기인 대표)은 임의 수정 금지.
- 현재 핵심 기능: **2026. 8. 29.(토) 창립총회 개최 공고** 게시.

## 2. 구조

빌더·프레임워크·빌드 단계 없음. 순수 정적 HTML 10개 파일.

```
/
├── index.html      홈 (히어로 + 창립총회 팝업 + 최근 소식)
├── about.html      협회소개 (목적·설립배경·주요사업)
├── people.html     함께하는 사람들 (조직도·연혁)
├── resource.html   자료실 (정관 링크 + 외부 링크 2건)
├── notice.html     공지사항  ← 공고 채널 (앵커 #post1 공고, #post2 개설 안내)
├── jeongwan.html   정관(안) 전문 (약 29,000px, 341개 조문 단락)
├── story.html      올리브이야기 (목록)
├── story1.html     올리브나무, 평화의 나무
├── story2.html     천 년을 사는 나무 아래에서
├── contact.html    오시는 길·문의 (구글맵 iframe + 카카오맵 링크)
├── images/         hero.jpg / greenhouse.jpg / school.jpg / farm_aerial.jpg (4장)
├── sitemap.xml     10개 URL
├── robots.txt      전체 허용
├── CNAME           sinan-olive.org
└── .nojekyll       Jekyll 처리 비활성화
```

### CSS·JS 구조 (주의)

- **공통 CSS가 각 HTML의 `<style>` 블록에 통째로 복제되어 있음.** 외부 .css 파일 없음.
  → 공통 스타일 1곳을 고치려면 10개 파일을 모두 고쳐야 함. 반대로 한 페이지만
  고치면 나머지와 어긋남. 수정 시 반드시 의도한 범위를 명확히 할 것.
- JS는 `index.html`의 팝업 스크립트 1개뿐. 나머지 페이지는 햄버거 메뉴의
  inline `onclick` 외에 스크립트 없음.
- 폰트는 외부 CDN 2곳(Google Fonts — Gowun Batang, jsDelivr — Pretendard)에 의존.
  로컬 사본 없음. CDN 장애 시 시스템 폰트로 폴백됨(레이아웃은 유지).

## 3. 배포 방식

- GitHub Pages. 저장소 `paulsong7777/sinan-olive-web`, **`main` 브랜치 루트가 그대로 실배포.**
- `git push` 즉시 공개 반영. 스테이징 환경 없음. 리뷰 게이트 없음.
- 커스텀 도메인은 `CNAME` 파일(`sinan-olive.org`)로 지정.
- HTTPS/DNS는 GitHub Pages 설정 영역이며 코드로 제어 불가.

### 로컬 확인 방법

```bash
python3 -m http.server 8765
```
→ http://localhost:8765 . 절대경로가 없어 서브디렉터리 없이 그대로 동작함.

## 4. QA-1(2026-08-25) 실측 결과

측정 환경: 실제 브라우저 렌더, 모바일 390×844 / 데스크톱 1440×1000·1300.
코드 추측이 아닌 DOM·computed style 실측.

| 항목 | 결과 |
|---|---|
| 내부 링크 전수 | 전 페이지 200. 깨진 링크 0건 |
| 앵커 #post1 / #post2 | 타깃 존재, 스티키 내비에 가리던 문제 **수정함** |
| plan.html 잔여 참조 | 인바운드 0건 확인 후 **파일 삭제** |
| 모바일 햄버거 | 10개 페이지 전부 열림/닫힘 정상, 불투명 흰 패널(rgb(255,255,255), opacity 1, z-index 60 > nav 50) |
| 모바일 가로 스크롤 | 10개 페이지 전부 scrollWidth == clientWidth == 390 (**발생 0건**) |
| 스티키 푸터 | 데스크톱·모바일 모두 바닥 밀착 확인 (gap 0px) |
| 팝업 | 열림/닫힘 정상. "오늘 하루 보지 않기" + 만료 로직 **신규 구현** |
| 콘솔 에러 | 10개 페이지 전부 0건 |
| 이미지 | 4장 전부 200 + naturalWidth > 0 |
| `<title>` / meta description | 10개 페이지 전부 존재 (누락 0건) |
| 날짜·주소·이메일 대조 | index의 게시일 1건 불일치 **수정함**, 나머지 전부 일치 |

## 5. 남은 이슈 / 알려진 제약

### 확인 못 한 것 (환경 제약, 코드 문제 아님)
- **외부 링크 실제 응답 미확인**: coop.go.kr, shinan.go.kr, map.kakao.com,
  구글맵 embed. QA 환경에서 외부 호스트가 차단되어 로컬에서 검증 불가.
  → 실배포 후 브라우저에서 직접 클릭 확인 필요.
- **웹폰트 실제 로딩 미확인**: 같은 이유로 CDN 요청이 나가지 않음.
  실배포 URL에서 Gowun Batang / Pretendard 적용 여부 육안 확인 필요.
- **스크린샷 미첨부**: QA 환경에서 브라우저 페인 캡처가 불가하여
  픽셀 스크린샷 대신 DOM·computed style 실측값으로 검증함.

### 구조적 이슈 (동작에는 문제 없음)
- 공통 CSS 10중 복제 (위 2절 참고).
- meta description이 10개 페이지 전부 동일 문구.
- 팝업에 `role="dialog"`는 있으나 `aria-modal`·포커스 트랩·ESC 닫기 없음.
- 햄버거 버튼에 `aria-expanded` 없음.
- 이미지 4장 합계 약 2.5MB, 원본 해상도 그대로. 모바일 데이터 부담.

### 정책·명의
- 도메인과 저장소가 아직 개인 명의(paulsong7777). 조합 명의 이전 필요 → TASKS.md
- 임원 명단·확정 정관·총회 결과는 총회 이후 갱신 대상 → TASKS.md

## 6. 손대면 안 되는 것

1. `notice.html` 공고 전문 — 정관 제6조 공고. 한 글자도 임의 수정 금지.
2. 디자인·레이아웃·컬러·문구 톤 — 임의 변경으로 사고 이력 있음.
   개선점은 코드에 반영하지 말고 TASKS.md에 기록만 할 것.
3. HTTPS/DNS 설정 — GitHub Pages 설정 영역.
