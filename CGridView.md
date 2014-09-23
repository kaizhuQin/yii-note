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
    		'nextPageLabel'=>'下一页',
    		'header'=>'',
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
         array(
         	'name'=>'is_vip',
         	'value'=>'$data->is_vip',
         	'filter'=>array('0'=>'否','1'=>'是'),
         ),
         array(  
            'class'=>'CButtonColumn',  
            'template'=>'{view} {update}',  
            'viewButtonOptions'=>array('title'=>'查看'),  
            'updateButtonOptions'=>array('title'=>'修改'),  
        ),
        
        array(  
            'class'=>'CButtonColumn',  
            'header'=>'首页展示',  
            'template'=>'{add} {del}',  
            'buttons'=>array(  
                    'add' => array(  
                            'label'=>'展示',     // text label of the button  
                            'url'=>'Yii::app()->controller->createUrl("focus/create",array("id"=>$data->primaryKey,"type"=>1))',       // a PHP expression for generating the URL of the button  
                            'imageUrl'=>'http://s.maylou.com/common/images/ysh.jpg',  // image URL of the button. If not set or false, a text link is used  
                            'options'=>array('style'=>'cursor:pointer;'), // HTML options for the button tag  
                            'click'=>$click,     // a JS function to be invoked when the button is clicked  
                            'visible'=>'SiteRecommend::isItemInTypeAndId(1, $data->id)?false:true',  
                    ),  
                    'del' => array(  
                            'label'=>'取消展示',     // text label of the button  
                            'url'=>'Yii::app()->controller->createUrl("focus/delete",array("id"=>$data->primaryKey,"type"=>1))',       // a PHP expression for generating the URL of the button  
                            'imageUrl'=>'http://s.maylou.com/common/images/yzhu.jpg',  // image URL of the button. If not set or false, a text link is used  
                            'options'=>array('style'=>'cursor:pointer;'), // HTML options for the button tag  
                            'click'=>$click,     // a JS function to be invoked when the button is clicked  
                            'visible'=>'SiteRecommend::isItemInTypeAndId(1, $data->id)?true:false',  
                    )  
            ),  
        ), 
      
    ),
));
?>
```
