## 了解Yii过程的一些记录

### Part 1:main.php 配置文件

```php
<?php
return array(
	// 当前应用根目录的绝对物理路径 
	'basePath'=>dirname(__FILE__).DIRECTORY_SEPARATOR.'..',
	// 当前应用的名称
	'name'=>'Yii Blog Demo test',

	// 预载入log（记录）应用组件，这表示该应用组件无论它们是否被访问都要被创建。
	// 该应用的参数配置在下面以“components”为关键字的数组中设置。 
	'preload'=>array('log'),

	// 自动载入的模型和组件类 
	'import'=>array(
		'application.models.*',
		'application.components.*',
	),

	// 默认控制器
	'defaultController'=>'TestYii',

	// 当前应用的组件配置
	'components'=>array(
		'user'=>array(
			// 允许自动登录
			'allowAutoLogin'=>true,
		),
		// 'db'=>array(
		// 	'connectionString' => 'sqlite:protected/data/blog.db',
		// 	'tablePrefix' => 'tbl_',
		// ),
		// Mysql 数据库
		'db'=>array(
			'connectionString' => 'mysql:host=localhost;dbname=kaizhu',
			'emulatePrepare' => true,
			'username' => 'root',
			'password' => '666666',
			'charset' => 'utf8',
			'tablePrefix' => 't_',
		),
		
		'errorHandler'=>array(
			// 使用SiteController控制器类中的actionError方法显示错误
			'errorAction'=>'site/error',
		),
		// URL路由管理器
		'urlManager'=>array(
			'urlFormat'=>'path',// URL格式
			'rules'=>array(
				'post/<id:\d+>/<title:.*?>'=>'post/view',
				'posts/<tag:.*?>'=>'post/index',
				'<controller:\w+>/<action:\w+>'=>'<controller>/<action>',
			),
		),
		// 日志
		'log'=>array(
			'class'=>'CLogRouter',// 处理记录信息的类 
			'routes'=>array(
				array(
					'class'=>'CFileLogRoute',// 处理错误信息的类 
					'levels'=>'error, warning', // 错误等级 
				),
				// 如要将错误记录消息在网页上显示，取消下面的注释 
				/*
				array(
					'class'=>'CWebLogRoute',
				),
				*/
			),
		),
	),
	
	// 模块
	'modules'=>array(
		// uncomment the following to enable the Gii tool
		'gii'=>array(
			'class'=>'system.gii.GiiModule',
			'password'=>'666666',
			// If removed, Gii defaults to localhost only. Edit carefully to taste.
			'ipFilters'=>array('127.0.0.1','::1'),
		),
	),

	// application-level parameters that can be accessed
	// using Yii::app()->params['paramName']
	'params'=>require(dirname(__FILE__).'/params.php'),
);
?>
```

### Part 2:数据库操作和验证

利用gii工具可以快速地生成对应的model来进行数据库的字段的验证，同时，我们也可以根据需求加入自己的验证。   

例如：数据库的结构如下
```mysql
CREATE TABLE `t_qq_group` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '自增主键',
  `qq_group_num` int(11) DEFAULT NULL COMMENT 'QQ群号',
  `create_time` int(11) DEFAULT NULL COMMENT '创建时间',
  `update_time` int(11) DEFAULT NULL COMMENT '更新时间',
  `power_level` int(11) DEFAULT NULL COMMENT '权重',
  `status` int(11) DEFAULT NULL COMMENT '状态',
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8
```

利用gii生成的继承CActiveRecord的QqGroup这个model如下：
```php
<?php

/**
 * This is the model class for table "{{qq_group}}".
 *
 * The followings are the available columns in table '{{qq_group}}':
 * @property integer $id
 * @property integer $qq_group_num
 * @property integer $create_time
 * @property integer $update_time
 * @property integer $power_level
 * @property integer $status
 */
class QqGroup extends CActiveRecord
{
	/**
	 * @return string the associated database table name
	 */
	public function tableName()
	{
		return '{{qq_group}}';
	}

	/**
	 * @return array validation rules for model attributes.
	 */
	public function rules()
	{
		// NOTE: you should only define rules for those attributes that
		// will receive user inputs.
		return array(
			array('qq_group_num, create_time, update_time, power_level, status', 'numerical', 'integerOnly'=>true, 'message'=>'必须是整数'),
			// The following rule is used by search().
			// @todo Please remove those attributes that should not be searched.
			array('qq_group_num','required','message'=>'不能为空'),
			array('id, qq_group_num, create_time, update_time, power_level, status', 'safe', 'on'=>'search'),
		);
	}

	/**
	 * @return array relational rules.
	 */
	public function relations()
	{
		// NOTE: you may need to adjust the relation name and the related
		// class name for the relations automatically generated below.
		return array(
		);
	}

	/**
	 * @return array customized attribute labels (name=>label)
	 */
	public function attributeLabels()
	{
		return array(
			'id' => 'ID',
			'qq_group_num' => 'Qq Group Num',
			'create_time' => 'Create Time',
			'update_time' => 'Update Time',
			'power_level' => 'Power Level',
			'status' => 'Status',
		);
	}

	/**
	 * Retrieves a list of models based on the current search/filter conditions.
	 *
	 * Typical usecase:
	 * - Initialize the model fields with values from filter form.
	 * - Execute this method to get CActiveDataProvider instance which will filter
	 * models according to data in model fields.
	 * - Pass data provider to CGridView, CListView or any similar widget.
	 *
	 * @return CActiveDataProvider the data provider that can return the models
	 * based on the search/filter conditions.
	 */
	public function search()
	{
		// @todo Please modify the following code to remove attributes that should not be searched.

		$criteria=new CDbCriteria;

		$criteria->compare('id',$this->id);
		$criteria->compare('qq_group_num',$this->qq_group_num);
		$criteria->compare('create_time',$this->create_time);
		$criteria->compare('update_time',$this->update_time);
		$criteria->compare('power_level',$this->power_level);
		$criteria->compare('status',$this->status);

		return new CActiveDataProvider($this, array(
			'criteria'=>$criteria,
		));
	}

	/**
	 * Returns the static model of the specified AR class.
	 * Please note that you should have this exact method in all your CActiveRecord descendants!
	 * @param string $className active record class name.
	 * @return QqGroup the static model class
	 */
	public static function model($className=__CLASS__)
	{
		return parent::model($className);
	}
}

```
`public function rules()` 主要描述对数据库字段的验证规则