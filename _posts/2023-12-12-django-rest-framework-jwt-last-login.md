---
layout : post
title : "django-rest-framework-jwt last_login does not get updated"
categories : [django]
published : true
---


```python
# last_login does not get updated 
# https://github.com/jpadilla/django-rest-framework-jwt/issues/235
from django.contrib.auth.models import update_last_login

from rest_framework_jwt import views as jwt_views
from rest_framework_jwt import serializers
from rest_framework_jwt import settings
from datetime import datetime

jwt_response_payload_handler = settings.api_settings.JWT_RESPONSE_PAYLOAD_HANDLER

class ObtainAuthJSONWebToken(jwt_views.JSONWebTokenAPIView):
    """Implementation of ObtainAuthJSONWebToken with last_login update"""

    def post(self, request):
        serializer = self.get_serializer(data=request.data)

        if serializer.is_valid():
            user = serializer.object.get('user') or request.user
            update_last_login(None, user)
            token = serializer.object.get('token')
            response_data = jwt_response_payload_handler(token, user, request)
            response = Response(response_data)
            if settings.api_settings.JWT_AUTH_COOKIE:
                expiration = (datetime.utcnow() +
                              settings.api_settings.JWT_EXPIRATION_DELTA)
                response.set_cookie(settings.api_settings.JWT_AUTH_COOKIE,
                                    token,
                                    expires=expiration,
                                    httponly=True)
            return response

        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    serializer_class = serializers.JSONWebTokenSerializer
```


**urls.py:**

```python
re_path(r'^user/login/$', views.ObtainAuthJSONWebToken.as_view(), name='user-login'),
```
### Reference 
* [last_login does not get updated](https://github.com/jpadilla/django-rest-framework-jwt/issues/235)