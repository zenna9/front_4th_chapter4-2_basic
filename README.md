# 바닐라 JS 프로젝트 성능 개선
- url: https://d3evlhnos2z3s3.cloudfront.net

## 성능 개선 보고서
### 1. 준비
 - PageSpeed Insights를 사용해 측정
 - desktop 환경으로 가정
 - 파일 변경 후 콘솔에서 캐시 무효화 실행 확인

### 2. 개선 과정
  1. 프로그레시브 이미지 로딩 기법 적용 
    - 이유 : 
      - 성능 저하 원인 중 이미지에 관련된 내용이 제일 많아, 이 부분을 우선 수정하기로 함. 
      - 특히 배너 이미지가 2,470ms로 가장 큰 저하 요소이기 때문에 우선적으로 적용
    - 개선 방법 : 
      - 프로그레시브 기법을 적용할 이미지에 progressive-image 클래스명을 추가하고, 해당 클래스명을 가진 요소는 기법을 적용하도록 수정
    - 개선 후 향상 지표 
      - 성능 : 62 → 82 (30% 향상)
