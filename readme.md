## 了解Yii过程的一些记录
---

### Part 1:main.php 配置文件
---

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
---

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

`array('属性名1,属性名2', '验证器别名', 'on'=>'场景', '验证器属性'=>'值', '...'=>'...')`

+ array() 中前两个值是必须的，后面则是可选的，当然你要看具体验证器了;
+ 当有多个属性要使用同一个验证器时，可以写在同一条规则中，属性名使用英文逗号分隔;
+ 验证器别名是必须的;
+ 'on'=>'场景' 是可选的, 场景是在初始化某个具体的 CActiveRecord 类时通过构造函数设定的;   
		选择场景
		如：`class Post extends CActiveRecord`
		`$model=new Post('search');`
		其中`search`就是场景，会去执行对应场景下的验证规则。

#### Yii内置的验证器   
---
+ `boolean`：它是CBooleanValidator类的别名，验证属性的值是布尔值(true或false)。   
	**`array('isAdmin','boolean')`**
+ `captcha`：它是CCaptchaValidator类的别名，验证属性的值等于一个显示的CAPTCHA(验证码)的值。   
	**`array('verifyCode','captcha')`**  [详情](http://blog.csdn.net/yuhui_fish/article/details/7580770)
+ `compare`：它是CCompareValidator类的别名，验证属性的值与另一个属性的值相等。   
	**`array('repeatPassword', 'compare', 'compareAttribute'=>'newPassword', 'message'=>'两次输入密码不同')`**
+ `email`：它是CEmailValidator类的别名，验证属性的值为有一个有效的Email地址。   
	**`array('emailAdr','email','message'=>'邮箱格式不对')`**
+ `default`：它是CDefaultValidator类的别名，验证属性的值为分配的默认值。
+ `exist`：它是CExistValidator类的别名，验证属性的值在表中的对应列中存在。   
	**`array('ip','exist','message'=>'{attribute}字段不存在')`**
+ `file`：它是CFileValidator类的别名，验证属性的值包含上传的文件。
+ `filter`：它是CFilterValidator类的别名，用过滤器转换属性的值。
+ `in`：它是CRangeValidator类的别名，验证属性值在一个预定义列表中。   
	**`array('gender', 'in', 'range'=>array(0,1,2),'message'=>'不在系统限定的范围')`**
+ `length`：它是CStringValidator类的别名，验证属性值的长度在一个范围内。    
	**`array('uname','length','min'=>6,'max'=>20,'message'=>'长度超出了限定值' );`**
+ `match`：它是CRegularExpressionValidator类的别名，验证属性值匹配一个正则表达式。
+ `numerical`：它是CNumberValidator类的别名，验证属性值是数字。   
	**`array('icon','numerical','message'=>'不是整型')`**
+ `required`：它是CRequiredValidator类的别名，验证属性值必需有值，不能为空。    
	**`array('uid','required','message'=>'不能为空');`**
+ `type`：它是CTypedValidator类的别名，验证属性值是一个指定的数据类型。
+ `unique`：它是CUniquedValidator类的别名，验证属性值在表中的对应列中是唯一的。   
	`array('qq','unique','message'=>'{attribute}已经存在相同的了')`
+ `url`：它是CUrlValidator类的别名，验证属性值是一个有效的URL。   
	`array('webSite','url','message'=>'{attribute}格式不对')`
+ `date`： 它是CDateValidator类的别名，验证属性值是否是一个时间格式类型；
+ `safe`：它是CSafeValidator类的别名，将对应的属性值标记为安全，以使它们可以被大规模赋值；
+ `unsafe`：它是CUnsafeValidator类的别名，将对应的属性值标记为不安全，不能赋值；   

#### 调用自己的验证规则   
----
```php
class LoginForm extends CFormModel
{
    public $username;
    public $password;
    public $rememberMe=false;
 
    private $_identity;
 
    public function rules()
    {
        return array(
            array('username, password', 'required'),
            array('rememberMe', 'boolean'),
            array('password', 'authenticate'),
        );
    }
 
    public function authenticate($attribute,$params)
    {
        $this->_identity=new UserIdentity($this->username,$this->password);
        if(!$this->_identity->authenticate())
            $this->addError('password','错误的用户名或密码。');
    }
}
```  

#### 利用正则   
---
```php
array('phone','match','pattern'=>'/^(\+?86-?)?(18|15|13|17)[0-9]{9}$/','message'=>'手机号码格式不正确')
```

### 数据库操作相关   
---
+ **`find()`**
```php
// find()查找满足指定条件的集合，并返回第一条
// findAll() 查找满足指定条件的集合，并返回结果集
$post=Post::model()->find($condition,$params);
// find the row with postID=10
$post=Post::model()->find('postID=:postID', array(':postID'=>10));
$dataPa1 = Post::model()->find("qq_group_num=:qq_group_num and status=:status",array(":qq_group_num"=>'330428605',":status"=>3));

```
配合CDbCriteria   
```php
$criteria=new CDbCriteria;
$criteria->select='title';  // only select the 'title' column
$criteria->condition='postID=:postID';
$criteria->params=array(':postID'=>10);
$post=Post::model()->find($criteria); // $params is not needed
```

+ **`findByAttributes()`**  
```php
$post=Post::model()->findByAttributes($attributes,$condition,$params);
```   

```php
$checkuser = Post::model()->findByAttributes(
    array('user_id' => Yii::app()->user->user_id, 'field_id' => $fieldid));
$user_data = Post::model()->findAllByAttributes(
    $attributes = array('user_id' => ':user_id'),
    $condition = "field_id in (:fields)",
    $params = array(':user_id' => Yii::app()->user->user_id, ':fields' => "$rule->dep_fields"));

// find the first row using the specified SQL statement
$post=Post::model()->findBySql($sql,$params);
//例子
Post::model()->findBySql("select id from user_field_data where user_id = :user_id and field_id = :field_id", array(':user_id' => $userid,':field_id'=>$fieldid));
//此时回传的是一个对象
```

+ **`findByPk()`**  
```php
//根据主键来查找数据
$post=Post::model()->findByPk($uid);
```   

+ **`update()`**  
```php
// 根据条件更新数据
$resCod = $model->updateAll($data,'id=:id',array(':id'=>2));
```

+ **`count()`**
```php
//public string count(mixed $condition='', array $params=array ( ))
//满足指定的查询条件的行数
$all = $model->count(); //返回全部的记录行数
$count = $model->count('status=:status',array(':status'=>0)); //返回符合查询条件的记录行数
```

+ **`deleteAll()`**
```php
//public string deleteAll(mixed $condition='', array $params=array ( ))
//删除指定的查询条件的记录
$count = $model->deleteAll('status=:status',array(':status'=>0));
```

+ **`deleteByPk()`**
```php
//根据主键来删除指定的记录
$count = $model->deleteByPk('21');
```

+ **`save()`**
```php
//保存记录
$model->attributes = $data;
$model->save();
```

#### CDbCriteria  构造数据库查询条件
```php
   /**
	* Yii的Active Recorder包装了很多
	* 特别是把SQL中 把where,order,limit,IN/not IN,like等常用短句都包含进CDbCriteria这个类中
	*
	*/
   $criteria =new CDbCriteria; 

   $criteria->addCondition("id=1"); //查询条件，即where id =1  
   $criteria->addInCondition('id', array(1,2,3,4,5));//代表where id IN (1,23,,4,5,);  
   $criteria->addNotInCondition('id',array(1,2,3,4,5));//与上面正好相法，是NOT IN  
   $criteria->addCondition('id=1','OR');//这是OR条件，多个条件的时候，该条件是OR而非AND 
   $criteria->addSearchCondition('name','分类');//搜索条件，其实代表了。。where name like '%分类%'  
   $criteria->addBetweenCondition('id', 1, 4);//between1 and 4   
     
   $criteria->compare('id',1);   //这个方法比较特殊，他会根据你的参数自动处理成addCondition或者addInCondition， 
                                   //即如果第二个参数是数组就会调用addInCondition  
    
   $criteria->addCondition("id = :id"); 
   $criteria->params[':id']=1;  
    
   $criteria->select = 'id,parentid,name';//代表了要查询的字段，默认select='*';  
   $criteria->join = 'xxx'; //连接表 
   $criteria->with = 'xxx';//调用relations   
   $criteria->limit =10;   //取1条数据，如果小于0，则不作处理  
   $criteria->offset =1;   //两条合并起来，则表示 limit 10 offset1,或者代表了。limit 1,10  
   $criteria->order = 'xxx DESC,XXX ASC';//排序条件 
   $criteria->group = 'group 条件'; 
   $criteria->having = 'having 条件'; 
   $criteria->distinct = FALSE;//是否唯一查询
```