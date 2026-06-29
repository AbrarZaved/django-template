# Working with DRF Generic Views

Django REST Framework (DRF) provides built-in "Generic Views" that handle common database operations (CRUD). In this template, we utilize these generic views but override their main methods to enforce our **standard JSON response format**.

Here is a comprehensive guide on how to build APIs using Generic Views in this project.

---

## 1. `ListAPIView` (GET - Fetch Multiple Records)

Use this when you want to return a list of items. Override the `list` method to format the response.

```python
from rest_framework import generics
from helpers.response import response
from .models import Product
from .serializers import ProductSerializer

class ProductListAPIView(generics.ListAPIView):
    queryset = Product.objects.filter(status=1) # Example filter
    serializer_class = ProductSerializer

    def list(self, request, *args, **kwargs):
        queryset = self.get_queryset()
        serializer = self.get_serializer(queryset, many=True)
        
        return response(
            details="Products retrieved successfully.",
            data=serializer.data
        )
```

---

## 2. `CreateAPIView` (POST - Create a Record)

Use this to create a single resource. Override the `create` method.

```python
from rest_framework import generics
from helpers.response import response, error_response
from .serializers import ProductSerializer

class ProductCreateAPIView(generics.CreateAPIView):
    serializer_class = ProductSerializer

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

## 3. `ListCreateAPIView` (GET & POST)

This is a combination of the above two. It allows fetching a list and creating an item at the same endpoint (e.g., `/api/v1/products/`).

```python
class ProductListCreateAPIView(generics.ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def list(self, request, *args, **kwargs):
        # Implement same logic as ListAPIView
        pass

    def create(self, request, *args, **kwargs):
        # Implement same logic as CreateAPIView
        pass
```

---

## 4. `RetrieveAPIView` (GET - Fetch a Single Record)

Use this when you only need to retrieve a single instance by its ID (e.g., `/api/v1/products/<id>/`).

```python
class ProductRetrieveAPIView(generics.RetrieveAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def retrieve(self, request, *args, **kwargs):
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        return response(
            details="Product retrieved successfully.",
            data=serializer.data
        )
```

---

## 5. `UpdateAPIView` (PUT/PATCH - Update a Record)

Use this when you want an endpoint strictly for updating a single resource.

```python
class ProductUpdateAPIView(generics.UpdateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def update(self, request, *args, **kwargs):
        partial = kwargs.pop('partial', True) # Default to PATCH behavior
        instance = self.get_object()
        serializer = self.get_serializer(instance, data=request.data, partial=partial)
        
        if serializer.is_valid():
            serializer.save()
            return response(
                details="Product updated successfully.",
                data=serializer.data
            )
            
        return error_response(
            details=serializer.errors
        )
```

---

## 6. `DestroyAPIView` (DELETE - Delete a Record)

Use this when you want an endpoint strictly for deleting a resource.

```python
class ProductDestroyAPIView(generics.DestroyAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def destroy(self, request, *args, **kwargs):
        instance = self.get_object()
        # Soft delete is highly recommended over instance.delete()
        instance.status = 0 # Example STATUS.INACTIVE
        instance.save()

        return response(
            details="Product deleted successfully.",
            status_code=204
        )
```

---

## 7. `RetrieveUpdateDestroyAPIView` (GET, PATCH, DELETE - Single Item)

Combines `RetrieveAPIView`, `UpdateAPIView`, and `DestroyAPIView` for full management of a single item via `GET`, `PATCH`, and `DELETE`.

```python
class ProductDetailAPIView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    def retrieve(self, request, *args, **kwargs):
        # Same as RetrieveAPIView
        pass

    def update(self, request, *args, **kwargs):
        # Same as UpdateAPIView
        pass

    def destroy(self, request, *args, **kwargs):
        # Same as DestroyAPIView
        pass
```

---

## 8. Advanced: Overriding Core Methods

Sometimes simply assigning `queryset` or `serializer_class` isn't enough. DRF allows you to override core methods to introduce dynamic behavior.

### A. Dynamic Querysets (`get_queryset`)
Use this when the queryset depends on the logged-in user or other dynamic factors.
```python
def get_queryset(self):
    # Only return products belonging to the logged-in user
    return Product.objects.filter(user=self.request.user)
```

### B. Dynamic Serializers (`get_serializer_class`)
Use this when you want a different serializer for `GET` vs `POST` requests.
```python
def get_serializer_class(self):
    if self.request.method == 'POST':
        return ProductCreateSerializer
    return ProductListSerializer
```

### C. Dynamic Object Retrieval (`get_object`)
Use this if you need to fetch an object based on something other than the default URL `pk` lookup.
```python
def get_object(self):
    slug = self.kwargs.get("slug")
    return get_object_or_404(Product, slug=slug)
```

### D. Dynamic Permissions (`get_permissions`)
Use this when `GET` requests are public, but `POST` requests require authentication.
```python
from rest_framework.permissions import IsAuthenticated, AllowAny

def get_permissions(self):
    if self.request.method == 'GET':
        return [AllowAny()]
    return [IsAuthenticated()]
```

---

## 9. Using `QueryParamsMixin` for Filtering

If you want to elegantly validate incoming GET query parameters (e.g., `?category=electronics`), you can use the `QueryParamsMixin` from `helpers.api_view`.

```python
from helpers.api_view import QueryParamsMixin
from rest_framework import serializers

class ProductFilterSerializer(serializers.Serializer):
    category = serializers.CharField(required=False)
    min_price = serializers.DecimalField(max_digits=10, decimal_places=2, required=False)

class FilteredProductListAPIView(QueryParamsMixin, generics.ListAPIView):
    serializer_class = ProductSerializer
    params_serializer = ProductFilterSerializer # Attach your query validator here

    def get_queryset(self):
        query = self.get_query() # Returns a clean dict of validated params
        return Product.objects.filter(**query)
        
    def list(self, request, *args, **kwargs):
        # ... standard list implementation using self.get_queryset()
```
