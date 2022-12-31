---
layout: single
title: Excel function
published : false
---
# 함수
<!-- | 함수 | 내용 |
| [AVERAGEIF](https://support.microsoft.com/ko-kr/office/averageif-%ED%95%A8%EC%88%98-faec8e2e-0dec-4308-af69-f5576d8ac642)  | 조건 열, 조건, 평균 열을 주면 조건에 맞는 셀만 평균 내는 함수 </br> range : 조건 비교 대상들</br> criteria  : 조건 </br> average_range : 조건 맞으면 평균 구할 대상들 |
| [INDEX](https://support.microsoft.com/ko-kr/office/index-%ED%95%A8%EC%88%98-a5dcf0dd-996d-40a4-a822-b56b061328bd)| 행, 열 인덱스로 선택한 테이블의 값 반환 </br> array : 기준 영역 </br> row_num : 행 번호 </br> column_num : 열 번호 | -->
<!-- | OFFSET | 기준과 폭, 너비로 지정된 범위의 참조를 반환 </br> reference : 기준 셀 </br> rows : 기준 셀 오프셋 (행) </br> cols : 기준 셀 </br> height : 범위 영역 높이 </br> width : 범위 영역 넓이|

| VLOOKUP | 조건에 맞는 행을 찾아 지정된 열 값 반환. </br> 조회할 값 </br> 찾을 범위 </br> 반환할 열 인덱스 지정 </br> 유사한 일치 (TRUE/FALSE)| -->
<!-- | COUNTIFS | 조건 맞는 행 수 반환 -->
| INDIRECT | 텍스트 참조에서 값을 반환|

## VLOOKUP

조건에 맞는 행을 찾아 원하는 열 값 반환.

>=VLOOKUP(조회하려는 항목, 찾고자 하는 위치, 반환할 값이 포함된 범위의 열 번호, 대략적 또는 정확히 일치 반환 - 1/TRUE 또는 0/FALSE로 표시).

</br> 조회할 값 </br> 찾을 범위 </br> 반환할 열 인덱스 지정 </br> 유사한 일치 (TRUE/FALSE)

## OFFSET

테이블 영역의 참조를 반환하는 [함수](https://support.microsoft.com/ko-kr/office/index-%ED%95%A8%EC%88%98-a5dcf0dd-996d-40a4-a822-b56b061328bd)입니다.

> OFFSET(reference, rows, cols, [height], [width])

</br> reference : 기준 셀 </br> rows : 기준 셀 오프셋 (행) </br> cols : 기준 셀 </br> height : 범위 영역 높이 </br> width : 범위 영역 넓이ㅋ


## INDEX

테이블 안에서 지정된 위치의 값을 반환하는 [함수](https://support.microsoft.com/ko-kr/office/index-%ED%95%A8%EC%88%98-a5dcf0dd-996d-40a4-a822-b56b061328bd)입니다.

|     | A      | B    | C   | D                   |
| --- | ------ | ---- | --- | ------------------- |
| 1   | 사과   | 레몬 |     | '=INDEX(A1:B2,2, 2) |
| 2   | 바나나 | 배   |     | 배                  |
| 3   |        |      |     |                     |
| 4   |        |      |     |                     |
| 5   |        |      |     |                     |


> INDEX(array, row_num, [column_num])

</br> array : 기준 영역 </br> row_num : 행 번호 </br> column_num : 열 번호

## AVERAGEIF

테이블에서 조건으로 필터링된 값들만 추려 평균을 내는 [함수](https://support.microsoft.com/ko-kr/office/averageif-%ED%95%A8%EC%88%98-faec8e2e-0dec-4308-af69-f5576d8ac642)입니다.

|     | A      | B     | C   | D                                  |
| --- | ------ | ----- | --- | ---------------------------------- |
| 1   | 100000 | 7000  |     | '=AVERAGEIF(B1:B4,"<23000")        |
| 2   | 200000 | 14000 |     | 14000                              |
| 3   | 300000 | 21000 |     | '=AVERAGEIF(A1:A4,">250000",B1:B4) |
| 4   | 400000 | 28000 |     | 24500                              |
| 5   |        |       |     |

> AVERAGEIF(range, criteria, [average_range])

</br> range : 조건 비교 대상들</br> criteria  : 조건 </br> average_range : 조건 맞으면 평균 구할 대상들


## MATCH

[항목을 영역에서 검색하고 상대위치를 반환하는 함수](https://support.microsoft.com/ko-kr/office/match-%ED%95%A8%EC%88%98-e8dffd45-c762-47d6-bf89-533f4a37673a)입니다.

값의 영역 내 상대 위치를 알고 싶을 때 사용합니다.

|     | A   | B   | C                 |
| --- | --- | --- | ----------------- |
| 1   | 5   |     | '=INDEX(25,A1:A3) |
| 2   | 25  |     | 2                 |
| 3   | 38  |     |                   |
| 4   |     |     |                   |
| 5   |     |     |                   |


> MATCH(lookup_value, lookup_array, [match_type])

## INDIRECT

값을 참조 위치로 인식하여 가리키는 값을 반환하는 [함수](https://support.microsoft.com/ko-kr/office/indirect-%ED%95%A8%EC%88%98-474b3a3a-8a26-4f44-b491-92b6306fa261)입니다.

|     | A    | B     | C   | D                  |
| --- | ---- | ----- | --- | ------------------ |
| 1   | B2   | 1.333 |     | '=INDIRECT(A1)     |
| 2   | B3   | 45    |     | 1.333              |
| 3   | 가격 | 10    |     | '=INDIRECT("B"&A4) |
| 4   | 4    | 62    |     | 62                 |
| 5   |      |       |     |                    |

> INDIRECT(ref_text, [a1])