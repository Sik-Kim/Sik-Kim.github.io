---
title: "Django ⎜ select_related 사용하여 쿼리수 줄이기"
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

# select_related 사용하여 쿼리수 줄이기

장바구니의 order list를 GET Method를 통해 불러오는 코드이다.
아래와 같이 3단계(코드1->코드2->코드3)로 수정하며 쿼리수를 줄였다.

## 코드1

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

## 코드2

```
    @login_decorator
    @query_debugger
    def get(self, request):
        orderlists = Order.objects.filter(user=request.user, orderstatus_id=1).last().orderlist_set.all()
```

## 코드3
```
    @login_decorator
    @query_debugger
    def get(self, request):
        orderlists = Order.objects.filter(user=request.user, orderstatus_id=1).last().orderlist_set.select_related('product__seller', 'product__delivery','size','color').all()
```