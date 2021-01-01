---
title: "Django ⎜ MethodSerializerField()로 역참조 Model을 Serializer 필드에 추가하기"
date: 2021-01-01 01:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - python
    - django
    - DRF
    - Serializer
category : 
    - DRF
---

## Intro
Serialzier를 안써봤을 땐 몰랐는데 이제는 Serializer가 없으면 Model을 사용할 때 상당히 불편할 것 같다. Serializer는 본인 필요에 따라 잘 구축하면 아주 편하게 model들을 사용할 수 있다.  
그렇기에 처음부터 프로젝트에 필요한 Model의 조합을 전체적으로 구상하고 Serializer 설계를 하면 최적화에 좋지 않을까..

<br>

## MethodSerializerField() 활용
**ModelSerializer의 model이 역참조하고 있는 model을 하나의 Serializer화 시키는 방법에 대한 내용이다.**  

우선 models를 보면 BrandInformation이 Brand를 참조하고 있다.   

```python
# in models (Brand, BrandInformation)

class Brand(models.Model):
    name = models.CharField(max_length=100)

class BrandInformation(models.Model):
    branch_quantity           = models.IntegerField(blank=True, null=True)
    branch_total_sales        = models.IntegerField(blank=True, null=True)
    branch_total_sales_pyeong = models.IntegerField(blank=True, null=True)
    brand = models.ForeignKey('brand.Brand', on_delete=models.CASCADE)
```

<br>

Brand의 ModelSerializer를 만들어 BrandInformation model의 필드 몇 개를 합쳐야 한다. Serializer 1, Serializer 2 부분의 return 부분을 주목하고 보면 된다.

처음엔 Serializer 1과 같이 작성 했다. 이렇게 작성해도 아무 문제는 없다.   
다만, 필드값을 하나하나 적어주는게 뭔가 Serializer 스럽지가 않았고 필드가 늘어 날 수록 적어줘야 하는 코드는 더 많아지는 것도 부담이다.  

<br>

그래서 Serializer 2와 같이 BrandInformation을 model로 하는 Serializer를 1개 더 만들고 2개를 합치는 방식으로 했다.  
코드가 더 직관적으로 바꼈으며 Serializer를 구축만 잘해두면 실수할 요소도 줄어든다.  

코드가 더 길어졌는데요? 라고 생각할 수 도 있는데 그렇지 않다.  
**난 각각의 특성에 따라 Serializer를 쪼개고 필요에 따라 조합하는게 거대한 Serializer 하나를 만드는 것보다 가독성이나 안정성도 높아지고 유지보수면에서도 좋은 것 같다.** 그리고 DetailBrandInformationSerializer는 이 부분 말고도 어짜피 다른 부분에서 필요해서 공통으로 활용할 수 있기도 했다.  


```python
# Serializer 1

class DetailBrandSerializer(serializers.ModelSerializer):
    brand_info = serializers.SerializerMethodField()

    class Meta:
        model  = Brand
        fields = ('name', 'brand_info')

    def get_brand_info(self, obj):
        info = obj.brandinformation_set.order_by('-year').first()
        return {
            "branch_quantity":info.branch_quantity,
            "branch_total_sales":info.branch_total_sales,
            "branch_total_sales_pyeong":info.branch_total_sales_pyeong
            }
```

```python
# Serializer 2

class DetailBrandInformationSerializer(serializers.ModelSerializer):
    class Meta:
        model  = BrandInformation
        fields = ('branch_quantity', 'branch_total_sales', 'branch_total_sales_pyeong')
        
class DetailBrandSerializer(serializers.ModelSerializer):
    brand_info = serializers.SerializerMethodField()

    class Meta:
        model  = Brand
        fields = ('name', 'brand_info')

    def get_brand_info(self, obj):
        info = obj.brandinformation_set.order_by('-year').first()
        return DetailBrandInformationSerializer(info).data
```
    
    

