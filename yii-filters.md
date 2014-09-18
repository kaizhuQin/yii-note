#### Yii使用filter进行访问控制    

过滤器可以定义为一个控制器类的方法，方法名必须以`filter`开头。例如，`filterAccessControl`方法定义了一个名为 accessControl 的过滤器:   

```php
public function filterAccessControl($filterChain)
{
    //code: do sth
    $filterChain->run();  
    // 调用 $filterChain->run() 以继续后续过滤器与动作的执行。
}
```   

其中的`$filterChain`(过滤器链)是一个`CFilterChain`的实例，代表与所请求动作相关的过滤器列表。在过滤器方法中，我们可以调用`$filterChain->run()`以继续执行后续过滤器和动作。   

过滤器也可以是一个`CFilter`或其子类的实例：   
```php
class PerformanceFilter extends CFilter
{
    protected function preFilter($filterChain)
    {
          // 动作被执行之前应用的逻辑
          return true; // 如果动作不应被执行，此处返回 false
    }
    
    protected function postFilter($filterChain)
    {
          // 动作执行之后应用的逻辑
    }
}
```   

要对动作应用过滤器，我们需要覆盖 CController::filters() 方法。此方法应返回一个过滤器配置数组。   
```php
class PostController extends CController
{
       public function filters()
       {
              return array(
                     'postOnly + edit, create',
                     array(
                            'application.filters.PerformanceFilter - edit, create',
                            'unit'=>'second',
                     )
              );
       }
}
```   

上述代码指定了两个过滤器：`postOnly`和`PerformanceFilter`,`postOnly`过滤器是基于方法的（相应的过滤器方法已在`CController`中定义）,而`performanceFilter`过滤器是基于对象的。
路径别名`application.filters.PerformanceFilter`指定过滤器类文件是`protected/filters/PerformanceFilter`.
我们使用一个数组配置`PerformanceFilter`,这样它就可被用于初始化过滤器对象的属性值.此处`PerformanceFilter`的`unit`属性值将被初始为`second`.

使用加减号，我们可指定哪些动作应该或不应该应用过滤器。上述代码中， postOnly 应只被应用于 edit 和 create 动作，而 PerformanceFilter 应被应用于 除了 edit 和 create 之外的动作。如果过滤器配置中没有使用加减号，则此过滤器将被应用于所有动作。
