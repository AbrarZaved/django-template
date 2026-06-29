# API Conventions & Best Practices

To ensure consistency across the project, developers must adhere to the following conventions when building new features.

## 1. Using the `BaseModel`
All new models must inherit from `common.models.BaseModel`. This ensures every table has standard metadata.

```python
from common.models import BaseModel
from django.db import models

class Product(BaseModel):
    title = models.CharField(max_length=200)
```
You automatically get `status`, `created_at`, and `updated_at` fields.

## 2. Standardized JSON Responses
Do not return native DRF `Response` objects directly. Instead, wrap your responses using the helpers in `helpers/response.py`.

### Success
```python
from helpers.response import response

def list(self, request, *args, **kwargs):
    queryset = self.get_queryset()
    serializer = self.get_serializer(queryset, many=True)
    return response(
        details="Data fetched successfully.",
        data=serializer.data
    )
```

### Errors
```python
from helpers.response import error_response

def my_action(self, request):
    if not valid:
        return error_response(
            details="Invalid action performed.",
            status_code=400
        )
```
This guarantees the frontend always receives the structure: `{ "success": ..., "details": ..., "code": ..., "data": ... }`.

## 3. Query Parameter Handling
Use the `QueryParamsMixin` from `helpers/api_view.py` for strictly validating GET request parameters.

```python
from helpers.api_view import QueryParamsMixin
from rest_framework import generics

class MyView(QueryParamsMixin, generics.ListAPIView):
    params_serializer = MyQuerySerializer
    
    def get_queryset(self):
        query = self.get_query() # Validated dict of query parameters
        return MyModel.objects.filter(**query)
```

## 4. Swagger Documentation
This project uses `drf-yasg` to auto-generate Swagger docs. Ensure all views and viewsets have clear serializers attached so the docs generate correctly.

For custom create views, you can utilize the `@create_view` decorator from `helpers/api_view.py` to seamlessly bind request and response serializers for the Swagger schema.
