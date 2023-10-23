--

layout: single

title:  "데이터를 Java의 stream()과 람다표현식으로 그룹화하는 법"
---

typora-root-url: ../

--

<script src="https://gist.github.com/XOHW91/b4bcc3a39ea49b160a18e70b8cf0fcc3.js"></script>

 쿼리 결과로 총 20줄을 반환 > rating_result_id로 그룹화하여 데이터 처리를 해야하는 상황

  - stream()과 람다를 사용하면 원하는 요소를 기준으로 데이터를 그룹화할 수 있음. 

  - groupedDetails은 `Map<Integer, List<RatingDetail>>` 형태로 표현되며, 각 `rating_result_id`에 해당하는 `RatingDetail` 객체들의 리스트를 나타냄. 예를 들어  `rating_result_id`가 1과 2 두 개의 값이 있을 경우, `groupedDetails`는 다음과 같은 형태가 될 수 있음

    {    

    1: [RatingDetail1, RatingDetail2, RatingDetail3],    2: [RatingDetail4, RatingDetail5] 

    }