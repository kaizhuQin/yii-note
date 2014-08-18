### Yii中使用Memcache
---
+ 将Memcached安装在主机上 
+ 配置yii的main.php   

```php
//main.php
array(
    ......
    'components'=>array(
        ......
        'cache'=>array(
            //'class'=>'system.caching.CMemCache',
            'class'=>'CMemCache',
            'servers'=>array(
                array('host'=>'server1', 'port'=>11211, 'weight'=>60),
                array('host'=>'server2', 'port'=>11211, 'weight'=>40),
            ),
        ),
    ),
);
```

在Controller下的action是使用：  

```php
//SiteController.php

public function actionTest()
{
	//加入缓存，key-value

	//第一种方式
	Yii::app()->cache->set('key','value',30);  //30s过期
	Yii::app()->cache->get('key');
	exit();

	//第二种方式
	$cache=Yii::app()->cache;
	$cache['var1']=$value1;  // 等价于: $cache->set('var1',$value1);
	$value2=$cache['var2'];  // 等价于: $value2=$cache->get('var2');

	//删除缓存
	Yii:app()->cache->delete('name');
}
```