# auth系统

### 1.总的知识点

- #### create_user：创建用户

- authenticate：验证登录

- login：记住用户的登录状态

- logout：退出登录

- is_authenticated：判断用户是否登录

- login_required：判断用户是否登陆的装饰器

#### 1.1login_required使用

```python
from django.shortcuts import render,reverse,redirect
from django.views import View
from django.http import HttpResponse
from form_session.forms import RegisterForm

from django.contrib.auth.models import User,Group,Permission
from django.contrib.auth import login,logout,authenticate

from django.contrib.auth.decorators import login_required
from django.contrib.auth.decorators import login_required

@login_required()   #登录权限控制,成功就会跳转到home函数
def home(request):
    # username = request.session.get('username','用户未登录')
    # if username=='用户未登录':
    #     return redirect(reverse('login'))
    return render(request,'form_session/home.html',context={'username':request.user})

#settings界面，没有登陆时的页面
LOGIN_URL='/form_session/login1/'
```



