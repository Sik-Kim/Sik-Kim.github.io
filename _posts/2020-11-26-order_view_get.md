---
title: "Django ⎜ select_related 사용하여 쿼리수 줄이기(장바구니 get method)"
date: 2020-11-26 13:25:00 -0400
summary: 
toc : ture
toc_sticky: true
comments: true
tag : 
    - django
    - select_related
category : 
    - django
---

## select_related 사용하여 쿼리수 줄이기(장바구니 get method)

장바구니의 order list를 GET Method를 통해 불러오는 코드이다.
아래와 같이 3단계(코드1->코드2->코드3)로 수정하며 쿼리수를 줄였다.

<br>

### 코드1
order, orderlists 2번이나 클래스에 접근하며 쿼리수를 마구 만들어냈다.
처음 코드를 짤때는 원하는 정보를 불러오기 바빠 코드의 성능에 대해서는 크게 고려하지 못했다.

```
    @login_decorator
    @query_debugger
    def get(self, request):
        user = request.user
        order = Order.objects.get(user=user, status_id=1)
        orderlists = OrderList.objects.filter(order=order)
        
        orderlist_data = [{
            "orderlist_id"    : orderlist.id,
            "title"           : orderlist.product.title,
            "quantity"        : orderlist.quantity,
            "price"           : orderlist.product.price,
            "delivery"        : orderlist.product.delivery.delivery_type,
            "seller"          : orderlist.product.seller.name,
            "thumb_image_url" : orderlist.product.thumb_image_url,
            "created_at"      : orderlist.created_at,
            "updated_at"      : orderlist.updated_at,
            "size"            : orderlist.size.name if orderlist.size_id is not None else None,
            "color"           : orderlist.color.name if orderlist.color_id is not None else None,
            "delivery_fee"    : orderlist.order.delivery_fee,
            "delivery_fee1"    : orderlist.order.delivery_fee,
            "delivery_fee2"    : orderlist.order.delivery_fee,
            "delivery_fee3"    : orderlist.order.delivery_fee
        } for orderlist in orderlists]
        return JsonResponse({"data": orderlist_data}, status=200)
```

<br>

### 코드2
Order Class에 1번만 접근하면서 코드1과 비교해서 코드 3줄을 1줄로 줄일 수 있었다. 불필요한 코드를 줄이며 코드를 깔끔하게 만든 것에 보람이 있었으나 사실 쿼리수를 크게 줄이지는 못했다. (이때는 select_realated 개념을 몰랐었다.)
```
    @login_decorator
    @query_debugger
    def get(self, request):
        orderlists = Order.objects.filter(user=request.user, orderstatus_id=1).last().orderlist_set.all()
```

<br>

### 코드3
select_related를 활용해서 쿼리수를 엄청나게 줄일 수 있었다.
@query_debugger로 확인했을 때 쿼리수가 100번 이상 되는걸 딱 2번으로 줄일 수 있었다.
그리고 filter 대신 get을 사용하며 추가적으로 불필요한 코드를 줄일 수 있었다.
```
    @login_decorator
    @query_debugger
    def get(self, request):
        orderlists = Order.objects.get(user=request.user, orderstatus_id=1).orderlist_set.select_related('product__seller', 'product__delivery','size','color').all()
```