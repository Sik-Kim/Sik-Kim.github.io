---
title: "Django ⎜ AttributeError: 'NoneType' object has no attribute ~ Error"
date: 2020-12-22 20:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - python
    - django
category : 
    - django
---

### AttributeError: 'NoneType' object has no attribute 'attribute명' (에러 내용)

데이터베이스에 저장되지 않은 데이터를 code로 호출했을 때 발생하는 에러이다.  
Serializer 코드 어디서 에러가 발생된건지 찾는데 한참이냐 걸렸다.  

처음에는 주석(# 에러발생 부분)의 코드가 잘못된지 알고 계속해서 코드를 살펴봤다.  
하지만 코드는 문제가 없었고 알고보니 없는 데이터를 요청해서 발생하는 에러였다.  

<br>

**Serializer 코드**
```python
class MapBranchSerializer(serializers.ModelSerializer):

    brand = BrandSerializer() # FK
    neighborhood = NeighborhoodSerializer() #FK

    branch_quantity = serializers.SerializerMethodField()
    
    class Meta:
        model = Branch
        fields = ('brand', 'neighborhood', 'id', 'name', 'latitude', 'longitude', 'branch_quantity')

    def get_branch_quantity(self, obj):
        print("-----obj-----",obj)
        print("-----obj.brand-----",obj.brand)
        print("-----obj.brand.brandinformation_set-----",obj.brand.brandinformation_set)
        print("-----obj.brand.brandinformation_set.first()-----",obj.brand.brandinformation_set.first())
        branch_quantity = obj.brand.brandinformation_set.first().branch_quantity  # 에러발생 부분
        print("-----branch_quantity-----",branch_quantity)
        return branch_quantity
```

<br>

**Error 내용**  
AttributeError: 'NoneType' object has no attribute 'branch_quantity'  
해석 : NoneType인 object가 branch_quantity라는 attribute를 찾을 수 없댜.

이 에러의 주목할 부분은 앞 부분이다.  
branch_quantity라는 attribute가 없다는 내용보다 **이미 object가 None이라는 것**이다.  
print문을 보면 obj.brand.brandinformation_set 이부분에서 object가 없어서 None을 출력한다. (사실 여기서 None이 나온것부터가 문제인데 에러가 나지는 않았다.)  
그러다 None인 object가 branch_quantity라는 attribute에 접근하려고 하니 저런 에러가 발생되는 것이다.

![error 내용](https://i.ibb.co/8xrx6BB/image.png)

<br>

**print문 결과**  



![error 찾기](https://i.ibb.co/C2LHfCT/image.png)