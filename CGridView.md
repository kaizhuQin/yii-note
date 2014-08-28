### yii 分页

#### zii.widgets.grid.CGridView   
```php
//action UserController.php
public function actionListUser()
{
	$dataProvider = new CActiveDataProvider(
		'UcenterMembers',
		array(
				'pagination'=>array('pageSize'=>10),
			)
		);

	$this->render('ListUser',array('userData'=>$dataProvider));
}

//view ListUser.php
<?php 
$this->widget('zii.widgets.grid.CGridView', array(
    'id'=>'ui-userData',
    'dataProvider' => $userData,
    'htmlOptions'=>array(
                // 下面的两种css作用等同
                // 'width'=>'200',
                // 'style'=>'text-align:center',
    			'style'=>'width:100;text-align:center;',
            ),
    // {items} 代表列表内容  {pager} 代表分页导航
    'template'=>'{items}{pager}',
    'pager' => array(
    		'prevPageLabel'=>'上一页',
    	),
   	// 每一页显示的内容，和model的数据表的属性(数据表的字段)对应
    'columns' => array(
        'uid', 
        'username', 
        'email',
         array(
         	//name | header 的效果相似，都是在表头显示的字符
         	// 'name' => 'regdate',
         	'header' => '注册时间',
         	// value 代表要显示的内容，value的值是php表达式
         	'value'=>'date("Y-m-d",$data["regdate"])',
         	// 判断是否显示，默认为true，
         	// 'visible'=>false|true,
         	// 控制这一列的样式
         	'htmlOptions'=>array(
         			"style"=>"color:red;",
         			)
         	),
      
    ),
));
?>
```