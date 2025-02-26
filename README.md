# 바닐라 JS 프로젝트 성능 개선

- url: https://d3evlhnos2z3s3.cloudfront.net

## 성능 개선 보고서

### 1. 준비

- PageSpeed Insights를 사용해 측정
- desktop 환경으로 가정
- 파일 변경 후 콘솔에서 캐시 무효화 실행 확인
   - ![image](https://github.com/user-attachments/assets/9c639e7a-434a-4a4f-8d85-a73cd148bf11)

### 2. 개선 과정
![image](https://github.com/user-attachments/assets/2f3ce7e8-0de7-4160-b173-2be86343db41)

1. 프로그레시브 이미지 로딩 기법 적용

   - https://pagespeed.web.dev/analysis/https-d3evlhnos2z3s3-cloudfront-net/t5sykptbyp?form_factor=desktop
   - 이유 :
     - 성능 저하 원인 중 이미지에 관련된 내용이 제일 많아, 이 부분을 우선 수정하기로 함.
     - 특히 배너 이미지가 2,470ms로 가장 큰 저하 요소이기 때문에 우선적으로 적용
   - 개선 방법 :
     - 프로그레시브 기법을 적용할 이미지에 progressive-image 클래스명을 추가하고, 해당 클래스명을 가진 요소는 기법을 적용하도록 수정
   - 개선 후 향상 지표
     - 성능 : 62 → 82 (30% 향상)

2. jpg to webp (성능 82→93)

   - https://pagespeed.web.dev/analysis/https-d3evlhnos2z3s3-cloudfront-net/p5asdgd0rl?form_factor=desktop
   - 이유 :
     - WebP 및 AVIF와 같은 이미지 형식은 PNG나 JPEG보다 압축률이 높기 때문에 다운로드가 빠르고 데이터 소비량도 적음
     - 2,156KB의 절감을 예상
   - 개선 방법 :
     - 이미지의 webp 변환 방법들 중, 한번에 변경 가능한 CLI방식을 선택
     - cwebp으로 images폴더 내의 jpg를 일괄 변경 `for %i in (*.jpg) do cwebp -q 80 "%i" -o "%~ni.webp"`
     - index.html에서 ".jpg" → ".webp"로 일괄 변경
   - 개선 후 지표
     - 성능 : 82 → 93
     - TBT : 100 → 150 (성능 저하됨)
     - LCP : 2.6 → 1.3
   - 기타
     - 전체적인 성능은 많이 향상되었지만 TBT가 오렌지 레벨 직전 수치를 보여 우려됨

3. lazy loading (성능 93→96)

   - https://pagespeed.web.dev/analysis/https-d3evlhnos2z3s3-cloudfront-net/3ffp85f7pg?form_factor=desktop
   - 이유 :
     - First Paint & LCP 최적화
   - 개선 방법 :
     - 외부 API로 불러오는 이미지에 lazy 클래스명을 추가하고, js로 적용했다.
   - 개선 후 지표
     - 성능 : 93 → 96
     - TBT : 150 → 90
     - LCP : 1.3 → 1.1
   - 기타
     - 모바일 환경에서 데이터 사용량 절약 효과가 높은 개선안인데 데스크탑 기준으로 측정해서 유의미한 차이가 느껴지지 않는 걸 수도 있다는 생각을 했다.

4. 불필요한 dom제거 (성능 +-0 )

   - https://pagespeed.web.dev/analysis/https-d3evlhnos2z3s3-cloudfront-net/71gbutstew?form_factor=desktop
   - 이유 :
     - TBT 점수 향상
   - 개선 방법 :
     - 불필요하게 사용된 span을 제거하라는 권고를 따름
   - 개선 후 향상 지표
     - TBT : 90 → 110
   - 기타
   - TBT점수를 올리기 위해 진행한 내용인데 오히려 성능이 저하되어서 당황했다..
   - 캐시 무효화를 깜박하고 쟀을 땐 유의미한 성능 개선이 있었는데 체크아웃하고 다시 측정했을땐 아니었다.

5. alt추가, 색상대비 강조 (성능 +-0 )
   - https://pagespeed.web.dev/analysis/https-d3evlhnos2z3s3-cloudfront-net/if8qj14qqo?form_factor=desktop
   - 이유 :
     - 권장사항 점수를 올리기 위함
   - 개선 방법 :
     - 배너 이미지에 alt 태그 추가
     - 대비가 낮은 버튼의 색상을 조절 (https://dequeuniversity.com/rules/axe/4.10/color-contrast)
   - 개선 후 향상 지표
     - 성능 : 96 → 98
     - 접근성 : 82 → 91
     - SEO(검색엔진 최적화) : 82 → 91
   - 기타
     - 청록색(#33c6dd)이 많이 사용되는데, 배경과의 대비가 크지 않아 권장사항 항목에서 마이너스 요소가 되었다.
       #00a0b9로 변경하면 점수를 올릴 수 있지만, 실무라면 디자이너가 화낼 것 같다.
       ![image](https://github.com/user-attachments/assets/53b6d60d-0ecd-4d30-a61c-12f08e6e6953)


# 질문

1. 매 수정 이후마다 캐시 무효화를 실행했는데 의미가 있는 것인지 궁금합니다. 이론상으로는 실행하고 측정해야 될 것 같았고, 누락하고 측정했을 때 결과가 제대로 반영되지 않는 느낌이 있었습니다.
2. 이미지 비율 기재는 이해가 되는데, width, height등을 어떻게 적어야 하는지 조금 난해합니다. 대부분 반응형이기 때문에 명확하게 적을 수 없을 것같은데, %로 기재하는 것도 의미가 있을까요?
