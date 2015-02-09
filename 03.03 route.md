#03.03 路由

在理解SF的路由之前，我们先了解一下什么是pretty URI。

现在的Web页面，基本都会用形如“`http://www.rsywx.net/books/01805.html`”的URI来表示一个资源。这个名为`01805.html`的页面并不物理存在于服务器上。在以前的编写实践中，它很可能是要这样来表达的：“`http://www.rsywx.net/listbook.php?bookid=01805`”。

现代PHP框架（其实更恰当地说是所有现代框架）都抛弃了第二种很丑陋也极不灵活极不安全、也极不SEO友好的做法。而采用类似第一种这样的pretty URI方式。

和几乎所有现代Web框架一样，SF也是单入口的。所谓单入口，是说整个Web应用都以一个文件作为入口，作为调用其它控制器的总调度。在SF中，这个文件是`app.php`（生产环境）或者`app_dev.php`（调试环境）。

那么问题来了，如果我们只有一个PHP文件，我们怎么来定义一个URI的路径呢？比如说：`http://www.rsywx.net/books/01805.html`需要将我们“带到”一个控制器，这个控制器能识别出参数（`01805`），然后进行相关的后续工作。

换句话说，在V->C的过程中（我们访问一个URI是V层次的动作，而对这个动作进行相应是C层次的操作），我们如何建立起这个映射？

我们需要的是所谓的**路由**。

我们先看一个典型的路由：

```
homepage:
  pattern:  /
  defaults: { _controller: AppBundle:Default:index }
```

这个最简单的路由由三个部分组成：

* 名称：`homepage`。这是一个描述性的名称，可以随意起名，但是最好和其内容有点关联并有指示作用。
* 模式：`pattern: /`。这个模式定义了访问应用的入口。这个入口可以是对外的，也可以是内部的。即以本例来说，它定义的入口就是“`http://somedomain.com/`”，也就是常规意义上的首页。
* 选项：`defaults: { ... }`。这部分进一步定义了该入口的参数。其中，最重要的就是`_controller`参数。
	* `_controller`：指明处理该应用入口请求的控制器是哪一个。除了极个别情况，这是必须有的参数。控制器的指定形式是：**Bundle_Name**:**Controller_Name** (or **class name**):**Action_Name**。在本例中个，处理`/`这个URI请求的控制器动作是`index`，它在`Default`控制器中，并位于`AppBundle`这个包里。

从理论上说，一个应用可以开放的入口并没有上限。不过在实际应用中，有那么几十个也就差不多了。

##参数

路由可以带参数。而参数有两种：一种是没有缺省值而必须提供的，一种是有缺省值而可以省略的。

带有参数的路由举例如下：

```
book_list:
  pattern: /books/list/{type}/{page}/{key}
  defaults: 
    page: 1
    key: all
    type: title
    _controller: trrsywxBundle:Book:list
```

路由中的参数用形如`{param_name}`的形式定义。一般建议将各个参数用`/`分割以避免参数之间的混淆和最终URI的清晰。

在上面这个路由中，我们定义了三个参数：`type`,`page`,`key`。这些参数具体派什么用途我们会稍后讨论。

在`defaults`中，对这三个参数设置了缺省值。因此，如果我们只是简单地访问：`http://mydomain.com/books/list`，那么由于三个参数都没有提供值，就等同于访问：`http://mydomain.com/books/list/title/all/1`。

如果一个路由中的参数没有缺省值，那么必须在访问时提供。否则SF会报错。

###路由参数的限定

对类似“`http://www.rsywx.net/books/01805.html`”这样的一个URL，我们可以这样来设置其路由：

```
book_detail:
  pattern:  /books/{id}.html
  defaults: { _controller: AppBundle:Book:detail }
```

如果一个用户不小心使用了类似：`/books/abc.html`（一本书的ID不可能是字符）或者`/books/123.html`（一本书的ID必须是5位数字组成）这样的URI，会发生什么？

这个路由中指定的控制器还是会被执行，根据传递进来的参数（abc或者123）进行书籍的选择——当然就找不到了。如果我们能在请求进入控制器之前就对参数加以限定以避免这样的低级错误，不是更好吗？

此时，我们可以对“合法”的参数该是怎样做出限定。

```
book_detail:
  pattern:  /books/{id}.html
  defaults: { _controller: AppBundle:Book:detail }
  requirements: 
    id: \d{5}
```

这里的`\d{5}`是一个正则表达式，匹配5个数字。通过这样的限制，我们可以保证通过该路由传递过来的参数必然是5位数字。当然，这个数字是不是有对应的书籍是另外一个问题。

###访问URI的方法

通常，我们输入一个URI或者通过点击一个链接访问一个URI时，都是进行的GET请求。一般来说，GET方法是最常见的，也是足够用的。但是，在处理表单的提交时，我们一般更会偏向于使用POST方法。也就是说，一个URI显示表单，然后提交的数据进入另一个URI进行处理。

因此，通常情形下，我们要设置两个路由：一个用来显示表单(比如`register`)，一个用来处理表单(比如`do_register`)。我们当然不希望用户在浏览器中直接访问`do_register`所对应的URI，因此有必要对访问路由的方法进行限制：

```
do_register:
  pattern:  /create_user
  defaults: { _controller: AppBundle:User:create }
  methods:  [POST]
```
通过指定`do_register`只能通过POST方法访问，就阻止了用户简单地在浏览器中输入“`/create_user`”来访问这个URI。

##创建路由时的常见陷阱

* SF对路由的解析是由上到下的。也就是说，如果有一个路由的模式得到匹配，SF将不再匹配后续的路由。因此，我们必须注意一点，就是路径模式应该遵循**“越精确、越特殊的模式越在前定义”**的原则。
* 在定义路径模式时，一定要注意会不会出现可能的重复。

比如这两个路由：

```
books_with_tag:
  pattern: /tag/{tag}

add_tag:
  pattern: /tag/add
  methods: [POST]
```

第一个路由可以用来显示那些有着标记为`tag`的书籍，第二个路由用来为一本书籍增加一个tag。这两个路由以这样的顺序出现的问题在于，如果我们通过一个表单提交了一些新的tag准备加到一本书籍上，那么我们期望的动作是`/tag/add`这个路由定义的动作，但是由于`books_with_tag`这个路由定义的路径模式在前，也匹配形如`/tag/add`这样的调用（此时这个路由的`tag`参数变成`add`），且该路由没有说明不可接受POST方法，于是这个路由将被第一个匹配。于是我们的表单递交动作将不会被执行。

解决方法之一，是调换这两个路由的定义次序；其二，可以限制`books_with_tag`的方法为只接受GET；其三，当然也可以修改其中一个路由的路径模式，使其不会产生误解。在实际操作中，我们可以根据需要选择一种方法来避免出现问题。

路由定义往往是应用开发的第一步——因为至少你必须创建一个主页吧？

