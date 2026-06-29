# How to Create a New API

This guide will walk you through the step-by-step process of creating a new API endpoint using this template's conventions. 

As an example, we will create a simple `Product` API.

---

## Step 1: Create a New App

First, generate a new Django app.
```bash
python manage.py startapp product
```

Next, register your app in the project settings. Open `projectile/settings.py` (or `projectile/settings/...` if split) and add it to `INSTALLED_APPS`:
```python
INSTALLED_APPS = [
    # ... other apps
    'product',
]
```

---

## Step 2: Create the Model

All models should inherit from `common.models.BaseModel`. This provides standard fields like `status`, `created_at`, and `updated_at`.

Open `product/models.py`:
```python
from django.db import models
from common.models import BaseModel

class Product(BaseModel):
    name = models.CharField(max_length=255)
    description = models.TextField(blank=True, null=True)
    price = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return self.name
```

Create and run the migrations:
```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Step 3: Create the Serializer

Create a file `product/serializers.py` to handle data serialization.

```python
from rest_framework import serializers
from .models import Product

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'description', 'price', 'status', 'created_at']
        read_only_fields = ['id', 'status', 'created_at']
```

---

## Step 4: Create the Views

Use DRF generic views but override the methods to use our standard `response` helper from `helpers.response.py`. You can also use the `@create_view` decorator for POST endpoints to generate Swagger docs automatically.

Open `product/views.py`:
```python
from rest_framework import generics
from helpers.response import response, error_response
from .models import Product
from .serializers import ProductSerializer

class ProductListCreateAPIView(generics.ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        serializer = self.get_serializer(queryset, many=True)
        return response(
            details="Products fetched successfully.",
            data=serializer.data
        )

    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return response(
                details="Product created successfully.",
                data=serializer.data,
                status_code=201
            )
        return error_response(
            details=serializer.errors,
            status_code=400
        )
```

---

## Step 5: Wire up the URLs

Create a file `product/urls.py` and map your view to a route:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('products/', views.ProductListCreateAPIView.as_view(), name='product-list-create'),
]
```

Finally, include these URLs in the main `projectile/urls.py` file:

```python
from django.urls import path, include

urlpatterns = [
    # ... existing routes
    path('api/v1/', include('product.urls')),
]
```

---

## Step 6: Test the API

Run the server:
```bash
python manage.py runserver
```

You can now test your new API:
- **GET** `http://127.0.0.1:8000/api/v1/products/`
- **POST** `http://127.0.0.1:8000/api/v1/products/`
- **Swagger Docs:** `http://127.0.0.1:8000/swagger/` (Your new endpoints will appear here automatically).
