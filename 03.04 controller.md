#03.04 控制器

控制器其实就是一个PHP类。

控制器的作用在于，接受来自路由的调度，进行相应的工作（获取请求的参数，进行数据库的查询或操作，对返回的数据加以进一步的处理，显示一个模板并对模板中的变量加以赋值）。

出于管理的需要，我们通常将对某个实体进行操作的工作集中归并到一个类中，这个类的名称、类中方法的命名都遵循一定的规范。

##类的名称和类成员的名称

从之前我们讨论的[路由](03.03 route.md)中，我们看到这样一个路由：

```
homepage:
  pattern:  /
  defaults: { _controller: AppBundle:Default:index }
```

我们知道这个路由表示的路径是`/`，也就是一般意义上的站点首页的位置，而它对应的控制器是`AppBundle:Default:index`，再回想一下我们在[包](03.02 bundle.md)这一章中展示的包结构图，SF的规定是这样的：

1. 所有的控制器都位于`AppBundle\Controller`目录下。
2. 定义控制器类的文件命名规范是：*Name*+**Controller**.php。而这里的`name`和该路由定义的`AppBundle:Default:index`的第二部分（也就是`Default`）一样。所以，针对`homepage`这个路由，我们必然要有一个`DefaultController.php`的文件与之对应。
3. 这个文件必须定义一个*Name*Controller的类，且这个类必须派生于`Controller`类。
4. 鉴于路由定义的第三部分`index`，它规定了具体采用什么动作。与之对应的是这个类中的成员函数。这个成员函数必须是`public`，而且命名为`indexAction`。也就是说，它的名称是路由定义中的第三部分`action`加上`Action`这个后缀。
5. 函数参数的定义必须和路由中的要求一致。参数出现的顺序并不是特别重要，但是名字必须和路由中指定的参数名称有对应。我们会在以后再更详细地讨论路由参数和控制器参数的问题。
6. 控制器类必须有自己的`namespace`声明。通常它就是该文件所在目录，因此它应该总是**`namespace AppBundle\Controller;`**。

一个典型的控制器类的代码可能是这样的：

```
//File: src/AppBundle/Controller/DefaultController.php

namespace AppBundle\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\Controller;
use Symfony\Component\HttpFoundation\Request;
use Symfony\Component\Security\Core\Security;

class DefaultController extends Controller
{

    public function indexAction()
    {
		// Code for index action.        
    }
    // More codes and more actions
}
``` 

上述代码中出现的一些`use`语句，我们在此不多做解释。只说明一点：它们是根据控制器中代码的需要所引入的命名空间。

关于控制器，本节就描述到这里。控制器是SF中最关键的一个概念，也是我们用SF编写应用时写代码最多、业务逻辑最集中的一个地方。

