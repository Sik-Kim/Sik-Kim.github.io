---
title: "Python ⎜ haversine module 사용하여 좌표 사이 거리 구하기"
date: 2020-12-13 20:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - python
    - haversine
category : 
    - python
---

### python haversine module 사용


Python으로 지도 관련 Code를 작성한다면 haversine module은 필수적으로 사용하게 될 것이다.  
haversine의 뜻은 호로 좌표 사이의 호의 길이를 구해주는 모듈이다.

<br>

### haversine 모듈 설치
> pip install haversine

<br>

python 공식 페이지의 예시는 아래와 같다.  
lyon, paris의 좌표값을 주고 거리를 계산한 것이다. 이게 끝이다.
```python
from haversine import haversine, Unit

lyon = (45.7597, 4.8422) # (lat, lon)
paris = (48.8567, 2.3508)

haversine(lyon, paris)
>> 392.2172595594006  # in kilometers

haversine(lyon, paris, unit=Unit.MILES)
>> 243.71201856934454  # in miles

# you can also use the string abbreviation for units:
haversine(lyon, paris, unit='mi')
>> 243.71201856934454  # in miles

haversine(lyon, paris, unit=Unit.NAUTICAL_MILES)
>> 211.78037755311516  # in nautical miles
```

공식을 다시 쓰면 이렇다. 되게 간단하다.
> 호길이 = haversine(좌표1, 좌표2, unit=단위)  (unit를 안쓰면 km로 인식)


<br><br>

### 실제 응용
아래는 직방 프로젝트에서 부동산 주변 편의시설을 찾는 API의 일부분이다.  
다 볼 필요없이 아래 haversine 부분만 참고하면 된다.  
haversine 값이 5km 이내인 school을 모두 찾아 near_schools이란 변수에 저장했다.
이렇게 좌표값에 따른 거리가 필요할 때 haversine을 활용할 수 있다.

```python
    location = ApartmentComplex.objects.get(id=id)

    latitude = float(location.latitude)
    longitude = float(location.longitude)
    position      = (latitude,longitude)
    LATITUDE_1KM  = 0.00904
    LONGITUDE_1KM = 0.00898
    if not (-90 < latitude < 90 and -180 < longitude < 180):
        return JsonResponse({"message":"INVALID_COORDINATE"}, status=400)
    limit = (
        Q(latitude__gt=latitude-5*LATITUDE_1KM, latitude__lt=latitude+5*LATITUDE_1KM) &
        Q(longitude__gt=longitude-5*LONGITUDE_1KM, longitude__lt=longitude+5*LONGITUDE_1KM)
    )

    schools = School.objects.filter(limit)
    near_schools = [school for school in schools if haversine(position, (school.latitude, school.longitude)) < 5 ]
```

<br>

### 위도, 경도 용어
용어가 매번 헷갈려서 정리했다.
1. latitude = 위도 = Y방향, 북/남 방향 = 서울(약 37.5)
2. longitude = 경도 = X방향, 동/서 방향 = 서울(약 127.0)

<br>

### 위도, 경도를 찾는 가장 간단한 방법
구글 지도에서 오른쪽 버튼을 누르면 위도, 경도를 바로 확인 할 수 있다.  
그 외 방법으론 네이버지도나 카카오지도의 개발자도구탭에서 network 페이지를 찾아보는 것과 공공데이터 등이 있다.