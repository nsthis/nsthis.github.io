---
title: MVC
date: 2017-09-30 03:35:43
tags: 设计模式
categories: 设计模式
---
我将MVC归属在设计模式里面,个人对MVC的理解它应该为一种设计模式。

<!-- more -->

##	什么是MVC
由模型(model),视图(view),控制器(controller)完成的应用程序由模型发出要实现的功能到控制器,控制器接收组织功能传递给视图;

Model(模型)，程序应用功能的实现，程序的逻辑的实现。在PHP中负责数据管理，数据生成;
View(视图)，图形界面逻辑。在PHP中负责输出，处理如何调用模板、需要的资源文件;
Controller(控制器)，负责转发请求，对请求处理。在PHP中根据请求决定调用的视图及使用的数据;

##	为什么使用MVC
MVC的主要作用是为了将代码分层、分类;
MVC的主要目的是为了解决Web开发中分离开发与设计工作，使其工作相对独立;
在这样的过程中还发现了其他的一些优点，网站的目录结构更加清晰，网站更易维护与扩展，可以实现模块的复用;

## 我所认知的MVC
MVC 是一个设计模式，它强制性的使应用程序的输入、处理和输出分开。使用 MVC 应 用程序被分成三个核心部件:模型、视图、控制器。它们各自处理自己的任务。视图是 用户看到并与之交互的界面。模型表示企业数据和业务规则。控制器接受用户的输入并 调用模型和视图去完成用户的需求。

##	优点
低耦合性、高重用性和可适用性、较低的生命周期成本、快速的部署、 可维护性、可扩展性，有利于软件工程化管理

##	缺点
没有明确的定义，完全理解 MVC 并不容易。不适合小型规模的应用程序;

##	MVC底层架构
1、Image类
```
	<?php

	namespace Framework;

	class Image
	{
		//路径
		private $path = './upload/';
		//随机文件名
		private $isRandName;
		
		//初使化成员属性
		public function  __construct($path = null , $r = true)
		{
			if (!is_null($path)) {
				$this->path = rtrim($path,'/').'/';
			} 
			
			$this->isRandName = $r;
		}
		
		//water水印的方法  目标和源，自适应能力更强一些，直接调成员方法即可
		//源、目标、位置 、透明度
		public function  water($dst,$src,$pos = 9, $prefix = 'wa_', $tmd = 100)
		{
			//判断文件路径是否存在
			$src = $this->path . $src;
			
			if (!file_exists($dst) || !file_exists($src))
			{
				exit('目标者水印图片不存在');
			}
			//获取图像的相关信息
			$dstInfo = self::getImageInfo($dst);
			$srcInfo = self::getImageInfo($src);
			
			//判断宽高是否超过了目标图片的宽和高
			if (!$this->_checkSize($dstInfo,$srcInfo)) {
				exit('水印图片宽、高不合法');
			}
			//摆放位置 
			$position = self::getPosition($dstInfo,$srcInfo,$pos);
			
			//打开图片
			$dstRes = self::openImg($dst, $dstInfo);
			$srcRes = self::openImg($src, $srcInfo);
			
			//将两个图片合并在一起
			$newRes = $this->_mergeImage($dstRes,$srcRes,$position,$dstInfo,$srcInfo,$tmd);
			
			
			if ($this->isRandName) {
				
				$path = $this->path.$prefix.uniqid().'.'.$dstInfo['subfix'];
			} else {
				$path = $this->path.$prefix.$dstInfo['basename'];
			}
			//保存图片
			self::saveImage($newRes,$path,$dstInfo);
			
			//销毁资源
			imagedestroy($newRes);
			imagedestroy($srcRes);
			//返回路径
			return $path;
		}
		
		//thumb 源 和 宽高
		public function thumb($dst,$width,$height,$prefix = 'thumb_')
		{
			//判断文件是否在
			if (!file_exists($dst)) {
				exit('文件路径不存在');
			}
			//获取图像的信息，没有信息，你什么都 做不鸟
			$info = self::getImageInfo($dst);
			//得到新的尺寸
			$newSize = self::getNewSize($width,$height,$info);
			//打开资源
			$res = self::openImg($dst,$info);
			//等比缩放这个资源，处理gif背景变黑的问题 
			$newRes = self::kidOfImage($res,$newSize,$info);
			//保存
			$path = $this->path.$prefix.$info['basename'];
			self::saveImage($newRes,$path,$info);
			//销毁
			imagedestroy($newRes);
			//返回路径
			return $path;
			
		}
		
		private static function kidOfImage($srcImg, $size, $imgInfo)
		{
			$newImg = imagecreatetruecolor($size["width"], $size["height"]);		
			$otsc = imagecolortransparent($srcImg);
			if ( $otsc >= 0 && $otsc < imagecolorstotal($srcImg)) {
				 $transparentcolor = imagecolorsforindex( $srcImg, $otsc );
					 $newtransparentcolor = imagecolorallocate(
					 $newImg,
					 $transparentcolor['red'],
						 $transparentcolor['green'],
					 $transparentcolor['blue']
				 );

				 imagefill( $newImg, 0, 0, $newtransparentcolor );
				 imagecolortransparent( $newImg, $newtransparentcolor );
			}

		
			imagecopyresized( $newImg, $srcImg, 0, 0, 0, 0, $size["width"], $size["height"], $imgInfo["width"], $imgInfo["height"] );
			imagedestroy($srcImg);
			return $newImg;
		}
		
		private static function getNewSize($width, $height, $imgInfo)
		{	
			$size["width"] = $imgInfo["width"];   //将原图片的宽度给数组中的$size["width"]
			$size["height"] = $imgInfo["height"];  //将原图片的高度给数组中的$size["height"]
			
			if($width < $imgInfo["width"]) {
				$size["width"] = $width;             //缩放的宽度如果比原图小才重新设置宽度
			}

			if ($width < $imgInfo["height"]) {
				$size["height"] = $height;            //缩放的高度如果比原图小才重新设置高度
			}

			if($imgInfo["width"]*$size["width"] > $imgInfo["height"] * $size["height"]) {
				$size["height"] = round($imgInfo["height"] * $size["width"] / $imgInfo["width"]);
			} else {
				$size["width"] = round($imgInfo["width"] * $size["height"] / $imgInfo["height"]);
			}

			return $size;
		}		
		
		
		public static function saveImage($res,$path,$info)
		{
			switch ($info['mime']) {
				case 'image/png':
				case 'image/x-png':
					imagepng($res,$path);
					break;
				case 'image/jpeg':
				case 'image/jpg':
				case 'image/pjpeg':
					imagejpeg($res,$path);
					break;
				case 'image/gif':
					imagegif($res,$path);
					break;
				case 'image/wbmp':
				case 'image/bmp':
					imagewbmp($res,$path);
					break;
			}
		}
		
		private function _mergeImage($dstRes,$srcRes,$position,$dstInfo,$srcInfo,$tmd)
		{
			imagecopymerge($dstRes,$srcRes,$position['x'],$position['y'],0,0,$srcInfo['width'],$srcInfo['height'],$tmd);
			return $dstRes;
		}
		
		
		public static function openImg($path, $info)
		{
			switch ($info['mime']) {
				case 'image/png':
				case 'image/x-png':
					$res = imagecreatefrompng($path);
					break;
				case 'image/jpeg':
				case 'image/jpg':
				case 'image/pjpeg':
					$res = imagecreatefromjpeg($path);
					break;
				case 'image/gif':
					$res = imagecreatefromgif($path);
					break;
				case 'image/wbmp':
				case 'image/bmp':
					$res = imagecreatefromwbmp($path);
					break;
			}
			return $res;
		}
		
		public static function getPosition($dstInfo,$srcInfo,$pos)
		{
			switch ($pos) {
				case 1:
					$x = 0;
					$y = 0;
					break;
				case 2:
					$x = ceil(($dstInfo['width'] - $srcInfo['width']) / 2);
					$y = 0;
					break;
				case 3:
					$x = $dstInfo['width'] - $srcInfo['width'];
					$y = 0;
					break;
					
				case 4:
					$x = 0;
					$y = ceil(($dstInfo['height'] - $srcInfo['height']) /2 );
					break;
				case 5:
					$x = ceil(($dstInfo['width'] - $srcInfo['width']) / 2);
					$y = ceil(($dstInfo['height'] - $srcInfo['height']) /2 );
					break;
				case 6:
					$x = $dstInfo['width'] - $srcInfo['width'];
					$y = ceil(($dstInfo['height'] - $srcInfo['height']) /2 );
					break;
					
				case 7:
					$x = 0;
					$y = $dstInfo['height'] - $srcInfo['height'];
					break;
				case 8:
					$x = ceil(($dstInfo['width'] - $srcInfo['width']) / 2);
					$y = $dstInfo['height'] - $srcInfo['height'];
					break;
				case 9:
					$x = $dstInfo['width'] - $srcInfo['width'];
					$y = $dstInfo['height'] - $srcInfo['height'];
					break;
				default:
					$x = mt_rand(0,$dstInfo['width'] - $srcInfo['width']);
					$y = mt_rand(0,$dstInfo['height'] - $srcInfo['height']);
				
			}

			return ['x' => $x, 'y' => $y];
		}
		
		private function _checkSize($dstInfo,$srcInfo)
		{
			if ($dstInfo['width'] < $srcInfo['width'] || $dstInfo['height'] < $srcInfo['height']) {
				return false;	
			}
			return true;
		}
		
		public static  function getImageInfo($path)
		{
			$data = [];
			$info = getimagesize($path);
			$data['width'] = $info[0];
			$data['height'] = $info[1];
			$data['mime'] = $info['mime'];
			$path = pathinfo($path);
			$data['basename'] = $path['basename'];
			$data['subfix'] = $path['extension'];
			return $data;
		}
	}
```
2、Model类
```
	<?php
	namespace Framework;


	class Model
	{
		//连接
		protected $link;
		//用户名
		protected $user;
		//密码
		protected $pwd;
		//字符集
		protected $charset;
		//表前缀
		protected $prefix;
		//库名
		protected $dbName;
		//主机
		protected $host;
		//表名
		protected $table;
		//字段
		protected $fields;
		//保存的查询、更新、添加的参数
		protected $options;
		
		//初使化数据库连接
		//把一批成员属性都 初使化
		public function __construct(Array $config)
		{
			$this->host = $config['DB_HOST'];
			$this->user = $config['DB_USER'];
			$this->pwd = $config['DB_PWD'];
			$this->dbName = $config['DB_NAME'];
			$this->charset = $config['DB_CHARSET'];
			$this->prefix = $config['DB_PREFIX'];
			//连接
			$this->link = $this->connect();
			//判断是否连接失败
			if (!$this->link) {
				exit('数据库连接失败');
			}
			//表名
			$this->table = $this->getTable();

			//字段
			$this->fields = $this->getFields();
			
			
		}
		
		
		//连接数库
		protected function connect()
		{
			$conn = mysqli_connect($this->host,$this->user,$this->pwd);
			//连接失败
			if (!$conn) {
				return false;
			}
			//选择库失败处理
			if (!mysqli_select_db($conn,$this->dbName)) {
				return false;
			}
		
			mysqli_set_charset($conn,$this->charset);
			
			return $conn;
			
		}
		
		//初始化表
		//（报错，我们再根据命名空间去解决，涉及到命名空间的类名）
		protected function getTable()
		{
			if (isset($this->table)) {
				//用户设置 过以用户设置的为准
				return $this->prefix . ltrim($this->table,$this->prefix);
				
			} else {
				
				if ($pos = strpos(get_class($this),'\\')) {
					
					return $this->prefix .  strtolower(str_replace(['Model','model'],'',substr(get_class($this),$pos + 1)));
					
					
				} else {
				
					//用户没有设置过，以类名拼接前缀，给成成全新的表名
					return $this->prefix .strtolower(substr(get_class($this),0,-5));
				}
				
			}
		}
		
		
		//始初化字段
		//要把字段缓存到一个文件中去
		
		protected function getFields()
		{
			//如果有字段的文件则说明曾经缓存过这个文件，直接包含进来即可
			//知道文件路径命名的规则 
			$filePath = './Cache/'.$this->table.'.php';
			if (file_exists($filePath)) {
				return include $filePath;
			} else {
				$fields = $this->queryFields();
				
				$str = "<?php \nreturn ".var_export($fields,true).';?>';
				
				file_put_contents($filePath,$str);
			}
			
			return $fields;
		}
		
		//查询出字段
		protected function queryFields()
		{
			$sql = 'desc '.$this->table;
			
			$data = $this->query($sql);
		
			
			$fields = [];
			foreach ($data as $key => $val) {
				$fields[] = $val['Field'];
				if ($val['Key'] == 'PRI') {
					$fields['_pk'] = $val['Field'];
				}
			}
			return $fields;
			
		}
		
		//系统级的查询方法，设置 为public ，如果在外部想要调用的时候，想自定sql句的候，可以直接query
		//查询应的结果，这个仅供读取使用
		public function query($sql)
		{
			
			$result = mysqli_query($this->link,$sql);
			
			if ($result) {
				$data = [];
				while($row = mysqli_fetch_assoc($result)) {
					$data[] = $row;
				}
				return $data;
			} else {
				return false;
			}
			
		}
		
		//查询方法
		//准备好无需换的SQL语句
		//用户调用询的时候，将call里面保存进去的参数，一一替换sql语句
		//发送SQL语句
		//返回查询果
		public function select()
		{
			
			$sql = "select %FIELDS% from %TABLE% %WHERE% %GROUP% %HAVING% %ORDER% %LIMIT%";
			
			$newSql = str_replace(
							array(
								'%FIELDS%',
								'%TABLE%',
								'%WHERE%',
								'%GROUP%',
								'%HAVING%',
								'%ORDER%',
								'%LIMIT%',
								
							),
							array(
								$this->parseFields(),
								$this->parseTable(),
								$this->parseWhere(),
								$this->parseGroup(),
								$this->parseHaving(),
								$this->parseOrder(),
								$this->parseLimit(),
							),
							$sql
							);
							
			//为了在外面想知道自动生成的SQL语旬是神马的时候
			$this->sql = $newSql;
			return $this->query($newSql);
			
		}
		
		protected function  parseFields()
		{
			//因为我们对比字段的时候不需要对比主键是谁
			//因此，我干掉主键 
			$fields = $this->fields;
			unset($fields['_pk']);
			
			//用户向fileds('id,username,password') 先将其切割，用,号切割explode
			//如果用户传的是一个数组fileds(['id','username','password'])，则对比应的字段，算交集
			//array_intersect
			//array_intersect_key
			//array_intersect_assoc
			//array_intersect_ukey
			if (isset($this->options['fields'][0])) {
				//是数组的时怎么处理法
				if (is_array($this->options['fields'][0])) {
					foreach($this->options['fields'][0] as $key => $val) {
						if (!in_array($val,$fields)) {
							unset($this->options['fields'][0][$key]);
						}
					}
					return join(',',$this->options['fields'][0]);
					
				} elseif (is_string($this->options['fields'][0])) {
					//是字符串的时候先变为组再处理
					$this->options['fields'][0] = explode(',',$this->options['fields'][0]);
					
					foreach($this->options['fields'][0] as $key => $val) {
						if (!in_array($val,$fields)) {
							unset($this->options['fields'][0][$key]);
						}
					}
					return join(',',$this->options['fields'][0]);
				}
				
			} else {
				return join(',',$fields);
			}
			
		}
		
		//判断用户有没有手动指定过查询哪个用
		//如果指定过，则以用户设定的options里面的表为准
		//如果没有设定过，则以默认的为准
		protected function  parseTable()
		{
			if (isset($this->options['table'][0])) {
				return $this->options['table'][0];
			} else {
				return $this->table;
			}
		}
		
		//看看用户是否设置过where，如果设置过以用户设置 的为准
		//如果没有设置 则为空
		protected function  parseWhere()
		{
			if (isset($this->options['where'][0])) {
				return 'WHERE '.$this->options['where'][0];
			} else {
				return '';
			}
		}
		
		
		protected function  parseGroup()
		{
			if (isset($this->options['group'][0])) {
				return 'GROUP BY '.$this->options['group'][0];
			} else {
				return '';
			}
		}

		protected function  parseHaving()
		{
			if (isset($this->options['having'][0])) {
				return 'HAVING '.$this->options['having'][0];
			} else {
				return '';
			}
		}
		
		protected function  parseOrder()
		{
			if (isset($this->options['order'][0])) {
				return 'ORDER BY '.$this->options['order'][0];
			} else {
				return '';
			}
		}
		
		//limit可以有以下几种传法
		//用户传进来的是一个整 数，就是查询指定的条数
		//用户传进来的是一个数组，则数组中的第一个元素为$offset,第二个元素为$num
		//如果户传进来的是一个字符串，则以用户传的为准
		protected function  parseLimit()
		{
			if (isset($this->options['limit'][0])) {
				
				if (is_int($this->options['limit'][0])) {
					return 'LIMIT '.$this->options['limit'][0];
				} elseif (is_array($this->options['limit'][0])) {
					return 'LIMIT '. join(',',$this->options['limit'][0]);
				} else {
					return 'LIMIT ' . $this->options['limit'][0];
				}
			} else {
				return '';
			}
		}
		
		
		//增加方法
		public function add($data)
		{
			
		
			$sql = "insert into %TABLE%(%FIELDS%) values(%VALUES%)";
			//insert into bbs_user(username,sex) values('liwenkai','null');
			$newSql = str_replace(
						array('%TABLE%','%FIELDS%','%VALUES%'),
						array(
							$this->parseTable(),
							$this->parseInsertFields($data),
							join(',',$this->parseValue($data)),
						),
						$sql
					);
			
			$this->sql = $newSql;
			
			//echo $newSql;
			return $this->exec($newSql,true);
		}
		
		protected function parseValue($data)
		{
			if (is_string($data)) {
				$data = '\''.$data.'\'';
			} elseif (is_array($data)) {
				$data = array_map(array($this,'parseValue'),$data);
			} elseif (is_null($data)) {
				$data = 'null';
			}
	 		
			return $data;
		}
		
		protected function parseInsertFields(&$data)
		{
			foreach ($data as $key => $value) {
				if (!in_array($key,$this->fields)) {
					unset($data[$key]);
				}
			}
			
			return join(',',array_keys($data));
		}
		
		//更新方法
		public function update($data)
		{
			$sql = "update %TABLE% set %SETS% %WHERE% %ORDER% %LIMIT%";
			$newSql = str_replace(
							array('%TABLE%','%SETS%','%WHERE%','%ORDER%','%LIMIT%'),
							array(
								$this->parseTable(),
								$this->parseSets($data),
								$this->parseWhere(),
								$this->parseOrder(),
								$this->parseLimit(),
							),
							$sql
						);
				
			
			$this->sql = $newSql;
			return $this->exec($newSql);
			
		}
		
		public function parseSets($data)
		{
			$sets = [];
			
			foreach ($data as $key => $val) {
				if (in_array($key,$this->fields)) {
					
					$sets[] = $key.'='.$this->parseValue($val);
				}
			}
			
			return join(',',$sets) ;
			
		}
		
		//删除方法
		public function delete()
		{
			$sql = "delete from %TABLE% %WHERE% %ORDER% %LIMIT%";
			
			$newSql = str_replace(
						array('%TABLE%','%WHERE%','%ORDER%','%LIMIT%'),
						array(
							$this->parseTable(),
							$this->parseWhere(),
							$this->parseOrder(),
							$this->parseLimit(),
						),
						$sql
					);
			
			$this->sql = $newSql;
			
			return $this->exec($newSql);
		
		}
		
		public function exec($sql, $isInsertId = false)
		{
			$result = mysqli_query($this->link,$sql);
			
			if ($result) {
				
				if ( $isInsertId ) {
					//insert 返回自动增长的ID
					return mysqli_insert_id($this->link);
				} else {
					
					//update delete只直返回 受影响行数
					return mysqli_affected_rows($this->link);
				}
				
			} else {
				return false;
			}		
		}
		
		//查最大值 
		public function count($field = null)
		{
			if (is_null($field)) {
				$field = $this->fields['_pk'];
			}
			
			$sql = "select count($field) as num  from %TABLE% %WHERE%";
			
			$sql = str_replace(
					array(
						'%TABLE%',
						'%WHERE%',
					),
					array(
						$this->parseTable(),
						$this->parseWhere(),
					),
					$sql
					);
			
			$this->sql = $sql;
			$data =  $this->query($sql);
			
			return $data[0]['num'];
			
		}
		
		//最小值 
		public function min($field = null)
		{
			if (is_null($field)) {
				$field = $this->fields['_pk'];
			}
			
			$sql = "select min($field) as num  from %TABLE% %WHERE%";
			
			$sql = str_replace(
					array(
						'%TABLE%',
						'%WHERE%',
					),
					array(
						$this->parseTable(),
						$this->parseWhere(),
					),
					$sql
					);
			
			$this->sql = $sql;
			$data =  $this->query($sql);
			
			return $data[0]['num'];
			
		}
		
		public function sum($field = null)
		{
			if (is_null($field)) {
				$field = $this->fields['_pk'];
			}
			
			$sql = "select SUM($field) as num  from %TABLE% %WHERE%";
			
			$sql = str_replace(
					array(
						'%TABLE%',
						'%WHERE%',
					),
					array(
						$this->parseTable(),
						$this->parseWhere(),
					),
					$sql
					);
			
			$this->sql = $sql;
			$data =  $this->query($sql);
			
			return $data[0]['num'];
			
		}
		
		//求最大值 
		public function max($field = null)
		{
			if (is_null($field)) {
				$field = $this->fields['_pk'];
			}
			
			$sql = "select max($field) as num  from %TABLE% %WHERE%";
			
			$sql = str_replace(
					array(
						'%TABLE%',
						'%WHERE%',
					),
					array(
						$this->parseTable(),
						$this->parseWhere(),
					),
					$sql
					);
			
			$this->sql = $sql;
			$data =  $this->query($sql);
			
			return $data[0]['num'];
			
		}
		
		//求平均数
		public function avg($field = null)
		{
			if (is_null($field)) {
				$field = $this->fields['_pk'];
			}
			
			$sql = "select avg($field) as num  from %TABLE% %WHERE%";
			
			$sql = str_replace(
					array(
						'%TABLE%',
						'%WHERE%',
					),
					array(
						$this->parseTable(),
						$this->parseWhere(),
					),
					$sql
					);
			
			$this->sql = $sql;
			$data =  $this->query($sql);
			
			return $data[0]['num'];
			
		}
		
		protected function getBy($field,$value)
		{
			
			$sql = "select %FIELDS% from %TABLE% %WHERE%";
			
			$sql = str_replace(
					array('%FIELDS%','%TABLE%','%WHERE%'),
					array(
						$this->parseFields(),
						$this->parseTable(),
						'WHERE '.$field . "='$value'",
					),
					$sql
					);
			
			$this->sql = $sql;
			
			return $this->query($sql);
			
		}
		
		//自动的一个按照字段来查询的智能化查询方法
		
		//__call方法，针对用户请求的limit()  order()  group having等将其保存到options中去
		//并且判断方法的法性
		//并且return $this，让其能连贯操作。ORM （对象映射模型）$user->limit()->order()
		public function __call($func,$args)
		{
			$allow = ['where','table','fields','order','limit','group','having'];
			$func = strtolower($func);
			
			if (in_array($func,$allow)) {
				$this->options[$func] = $args;
				return $this;
			} elseif(substr($func,0,5) == 'getby'){
				$field = substr($func,5);
			
				if (in_array($field,$this->fields)) {
					return $this->getby($field,$args[0]);
				}
			}else {
				exit('方法不合法');
			}
		}
		
		//对象销毁者关闭页面的时候，自动执行
		public function __destruct()
		{
			mysqli_close($this->link);
		}
	}
```
3、Page类
```
	<?php
	namespace Framework;


	//声明page类
	class Page
	{
		//url
		protected $url;
		//总页数
		protected $pageCount;
		//总条数
		protected $total;
		//每页显示数
		protected $num;
		//当前页
		protected $page;
		
		
		//初使化成员属性
		public function __construct($total, $num = 5)
		{
			//总条数
			$this->total = ($total > 0) ? (int) $total : 1;
			//每页显示数
			$this->num = $num;
			//总页数
			$this->pageCount = $this->getPageCount();
			//当前页
			$this->page = $this->getCurrentPage();
			//url
			$this->url = $this->getUrl();
		}
		
		//一次性返回所有的分页信息
		public function rendor()
		{
			return [
				'first' => $this->first(),
				'next' => $this->next(),
				'prev' => $this->prev(),
				'end' => $this->end(),
			];
		}
		
		//limit方法，在未来分页数据查询时候，直接返回对应的limit 0,5 这样的字符串
		public function limit()
		{
			$offset = ($this->page - 1) * $this->num;
			
			$str = $offset.','.$this->num;
			
			return $str;
		}
		
		
		protected function end()
		{
			return $this->setQueryString('page='.$this->pageCount);
		}
		
		
		//上一页
		protected function  prev()
		{
			
			$page = ($this->page <=1) ? 1 : ($this->page -1);
			
			return $this->setQueryString('page='.$page);
			
		}
		
		//下一页
		protected function next()
		{
			
			$page = ($this->page >= $this->pageCount) ?  $this->pageCount : ($this->page + 1);
			
			return $this->setQueryString('page='.$page);
		}
		
		
		//首页
		protected function first()
		{
			//设置查询的page页码为第一页，即为首页
			return $this->setQueryString('page=1');
			
		}
		
		
		//一种况有?，一种情况是没有?的情况
		protected function setQueryString($page)
		{
			//如果url当中有问号
			if (strpos($this->url, '?')) {
				return $this->url.'&'.$page;
			} else {
			//如果url当中没有问号
				return $this->url.'?'.$page;
			}
		}
		
		
		protected function getUrl()
		{
			$path = $_SERVER['REQUEST_URI'];
			//先要获取户传进来的URI
			//用parse_url 解释URI
			$par = parse_url($path);
			
			
			//判断是否设置过query
			if (isset($par['query'])) {
				
				parse_str($par['query'],$query);
				
				//检查query里面是否有page,如果有就干掉page
				unset($query['page']);
				
				$path = $par['path'].'?'.http_build_query($query);
				
			}
			
			$path = rtrim($path,'?');
			
			
			//协议：主机：端口：文件和请求
			//判断是否定义过端口，并且端口是否为443，如果为443则是https协议，否则就是http协议
			$protocal = (isset($_SERVER['SERVER_PORT']) && $_SERVER['SERVER_PORT'] == 443) ?'https://' : 'http://';
			
			if (80 == $_SERVER['SERVER_PORT'] || 443 == $_SERVER['SERVER_PORT']) {
				$url = $protocal.$_SERVER['SERVER_NAME'].$path;
			} else {
				//http://www.baidu.com:8012/index.php?username=liwenkai
				
				$url = $protocal.$_SERVER['SERVER_NAME'].':'.$_SERVER['SERVER_PORT'].$path;
			}
			
			//拼接 path. ? . 全新组合成的URI
			return $url;
		}
		
		
		protected function getCurrentPage()
		{
			//如果用户设置过page信息，就返回page相关信息，强制转为整型
			if (isset($_GET['page'])) {
				//得到页码
				$page = (int) $_GET['page'];
				
				//你的页码不能够大于总页数
				if ($page > $this->pageCount) {
					$page = $this->pageCount;
				}
				//你的页码不能小于1
				if ($page < 1) {
					$page = 1;
				}
				
			} else {
				$page = 1;
			}
			
			return $page;
		}
		
		protected function getPageCount()
		{
			//计算总页数，使用到了进一法取整
			return ceil($this->total / $this->num);
		}
		
	}
```
4、Tpl类
```
	<?php
	namespace Framework;


	class Tpl
	{
		
		//缓存目录
		protected $cacheDir = './Cache/';
		//模版目录
		protected $tplDir = './Tpl/';
		//保存变量的成员方法
		protected $vars = [];
		//缓存有效期
		protected $cacheLifeTime = 3600;
		
		
		//初使化成员属性
		//判断缓存目录是否存在
		//判断模版路径是否存在，如果不存在要创建，如果不可写，要改权限
		//初化缓存时间
		public function __construct($tplDir = null, $cacheDir = null ,$cacheLifeTime = null)
		{
			if (isset($tplDir)) {
				if ($this->_checkDir($tplDir)) {
					$this->tplDir = rtrim($tplDir,'/').'/';
				}
			}
			
			if (isset($cacheDir)) {
				if ($this->_checkDir($cacheDir)) {
					$this->cacheDir = rtrim($cacheDir,'/').'/';
				}
			}
			
			if (isset($cacheLifeTime)) {
				$this->cacheLifeTime = $cacheLifeTime;
			}
			
		}
		
		private function _checkDir($path)
		{
			if (!file_exists($path) || !is_dir($path)) {
				return mkdir($path,0755,true);
			}
			
			if (!is_writable($path) || !is_readable($path)) {
				return chmod($path,0755);
			}
			return true;
		}
		
		//$tpl->assign()->assign()->display();
		
		//分配变量
		public function assign($key,$val)
		{
			$this->vars[$key] = $val;
			//return $this;
		}
		
		//display显示模版内容
		public function display($file,$isExecute = true, $uri = null)
		{
			//获得模版路径（模版目录路径 + 模版文件）
			$tplFilePath = $this->tplDir.$file;
			
			//判断文件是否存在
			if (!file_exists($tplFilePath)) {
				echo $tplFilePath;
				exit('模板文件不存在');
			}
			//获得编译文件路径（组合一个新的） page.php?page=3
			$cacheFile = md5($file.$uri).'.php';
			
			$cacheFilePath = $this->cacheDir . $cacheFile;
			
			//检测是否编译过、是否修改过模版文件，如果编译，并且在有效期内，则不编译
			if (!file_exists($cacheFilePath)) {
				$html = $this->compile($tplFilePath);
			} else {
				//如果修改过模版文件，则删除原有的编译文件重新编译
				if (filemtime($tplFilePath) > filemtime($cacheFilePath)) {
					unlink($cacheFilePath);
					$html = $this->compile($tplFilePath);
				}
			
				// 文件创建时间 + 缓存有效期 > time(当前时间) 没有过期，否则过期了
				//如果大于time 没过期
			
				$isTimeout = (filemtime($tplFilePath) + $this->cacheLifeTime > time()) ? false : true;
				
				if ($isTimeout) {
					$html = $this->compile($tplFilePath);
				}
				
			}
			
			//编译
			if (isset($html)) {
				if (!file_put_contents($cacheFilePath,$html)) {
					exit('编译文件写入失败');
				}
			}
			
			//判断是否需要执行
			//如果不需要执行则，则不包含 ，不extract变量，反之则不然
			if ($isExecute) {
				extract($this->vars);
				include $cacheFilePath;
			}
		
		
		}
		
		protected function compile($tplFilePath)
		{
			$html = file_get_contents($tplFilePath);
			
			$keys = [
				'{if %%}' => '<?php if(\1): ?>',
	            '{else}' => '<?php else : ?>',
	            '{else if %%}' => '<?php elseif(\1) : ?>',
	            '{elseif %%}' => '<?php elseif(\1) : ?>',
	            '{/if}' => '<?php endif;?>',
	            '{$%%}' => '<?=$\1;?>',
	            '{foreach %%}' => '<?php foreach(\1) :?>',
	            '{/foreach}' => '<?php endforeach;?>',
	            '{for %%}' => '<?php for(\1):?>',
	            '{/for}' => '<?php endfor;?>',
	            '{while %%}' => '<?php while(\1):?>',
	            '{/while}' => '<?php endwhile;?>',
	            '{continue}' => '<?php continue;?>',
	            '{break}' => '<?php break;?>',
	            '{$%% = $%%}' => '<?php $\1 = $\2;?>',
	            '{$%%++}' => '<?php $\1++;?>',
	            '{$%%--}' => '<?php $\1--;?>',
	            '{comment}' => '<?php /* ',
	            '{/comment}' => ' */ ?>',
	            '{/*}' => '<?php /* ',
	            '{*/}' => '* ?>',
	            '{section}' => '<?php ',
	            '{/section}' => '?>',
				'{{%%(%%)}}' => '<?=\1(\2);?>',
				'{date(%%,$%%)}'=> '<?=date(\1,$\2);?>',
				'{include %%}' => '<?php include "\1";?>',
				
			];
			
			foreach ($keys as $key => $val) {
				
				$pattern = '#'. str_replace('%%','(.+)',preg_quote($key,'#')) .'#imsU';
				$replace = $val;
				
				if (stripos($pattern,'include')) {
					//处理一次回调
				
					$html = preg_replace_callback($pattern,array($this,'parseInclude'),$html);
				} else {
					
					$html = preg_replace($pattern,$replace,$html);
				}
			}
			
			return $html;
		}
		
		protected function parseInclude($data)
		{
			
			$file = str_replace(['\'','"'],'',$data[1]);
			$path = $this->parsePath($file);
			$this->display($file,false);
			
			return '<?php include "'.$path.'";?>';
			
		}
		
		protected function parsePath($file)
		{
			$path = $this->cacheDir . md5($file).'.php';
			return $path;
			
		}
		
		//删除缓存文件（递归）
		public function clearCache()
		{
			self::delDir($this->cacheDir);
		}
		
		public static function delDir($path)
		{
			$res = opendir($path);
			
			while($file = readdir($res)) {
				if ($file == '.' || $file == '..') {
					continue;
				}
				$newPath = $path.'/'.$file;
				
				if (is_dir($newPath)) {
					self::delDir($newPath);
				} else{
					unlink($newPath);
				}
				
				
			}
	 	}
		
	}
```
5、Upload类
```
	<?php
	namespace Framework;


	class Upload
	{
		//上传到哪个目录
		protected $path = 'Upload/';
		//准许的MIME
		protected $allowmime = ['image/png','image/jpg','image/jpeg','image/pjpeg','image/bmp','image/wbmp','image/gif','image/x-png'];
		//准许的后缀
		protected $allowsubfix = ['jpg','png','jpeg','gif','bmp'];
		//准许的大小
		protected $allowsize = 2097152;
		//是否准许随机文件名
		protected $israndname = true;
		//是否准许日期目录
		protected $isdatepath = true;
		//文件的大小
		protected $size;
		//文件的原名
		protected $orgname;
		//文件的新名
		protected $newname;
		//文件的后缀
		protected $subfix;
		//文件的MIME类型
		protected $mime;
		//文件的新路径
		protected $newpath;
		//错误号
		protected $errorno;
		//错误信息
		protected $errorinfo;
		//临时文件
		protected $tmpfile;
		//前缀
		protected $prefix;
		
		
		//初使化成员属性，传进来的东西特别多，我们要求以数组的形式来传递
		//foreach循环，取出键名、值
		//将键名设置为成员属性名，将键值设为成员属性值
		//注意键名的合法性
		// errorInfo   errorinfo
		public function __construct(Array $config = [])
		{
		
			foreach ($config as $key => $value) {
	 
				$this->setOption($key,$value);
			}
		
		}
		
		protected function setOption($key,$value)
		{
			$key = strtolower($key);
			
			$allPro = get_class_vars(get_class($this));
			//var_dump($allpro);
			if (array_key_exists($key,$allPro)) {
				$this->$key = $value;
			}
		}
		
		
		
		//设置一个get方法专门获得新路径
		
		//设置 一个get方法专门来获得错误信息
		
		//成员方法上传方法
		public function uploadFile($field)
		{
			//var_dump($field);die;
			//检测路径用户是否定义过，如果没有定义失败
			if (!file_exists($this->path)) {
				echo '11111111111';
				$this->setOption('errorNo',-1);
				return false;
			}
			
			//目录权限检测
			if (!$this->checkPath()) {
				$this->setOption('errorNo',-2);
				return false;
				
			}
			
			
			//获得$_FILES当中五个基本信息
			$name = $_FILES[$field]['name'];
			$size = $_FILES[$field]['size'];
			$type = $_FILES[$field]['type'];
			 
			$tmpName = $_FILES[$field]['tmp_name'];
			$error = $_FILES[$field]['error'];
			
			//var_dump($_FILES);die;
			//传入到一个成员方法里面进行设置
			if (!$this->setFiles($name,$size,$type,$tmpName,$error)) {
				return false;
			}
			
			//检测MIME是否合法，检测后缀是否合法，检测文件大小是否超过了自定义的大小
			if (!$this->checkMime() || !$this->checkSubfix() || !$this->checkSize()) {
				return false;
			}
			
			//新名
			$this->newname = $this->getNewName();
			
			//新路径处理
			$this->newpath = $this->getNewPath();
		
			//是否是上传文件
			//如果是上传文件移动上传文件至指定的目录
			//如果成员return true
			if( $this->move()){
				
				return $this->newpath . $this->newname;
			}
				
		
		}
		
		protected function move()
		{
			if (!is_uploaded_file($this->tmpfile)) {
				$this->setOption('errorNo',-6);
				return false;
			}
		
			//var_dump($this-tmpfile);
			if (move_uploaded_file($this->tmpfile,$this->newpath.$this->newname)) {
				return true;
			} else {
				$this->setOption('errorNo',-7);
				return false;
			}
			
		}
		public function getNewPath()
		{
			$this->path = rtrim($this->path,'/').'/';
			
			if ($this->isdatepath) {
				$newpath = $this->path.date('Y/m/d/');
				if (!file_exists($newpath)) {
					mkdir($newpath,0755,true);
				}
				return $newpath;
			} else {
				return $this->path;
			}
			
		}
		
		protected function getNewName()
		{
			if ($this->israndname) {
				
				return $this->prefix . uniqid() . '.' . $this->subfix;
			} else {
				return $this->prefix . $this->orgname;
			}
		}
		
		protected function checkSize()
		{
			if ($this->size > $this->allowsize) {
			
				$this->setOption('errorNo',-5);
				return false;
			} else {
				
				return true;
			}
			
		}
		
		protected function checkSubFix()
		{
		
			if (in_array($this->subfix,$this->allowsubfix)) {
			
				return true;
			} else {
				
				$this->setOption('errorNo',-4);
				return false;
			}
		}
		
		protected function checkMime()
		{
			if (in_array($this->mime,$this->allowmime)) {
				
				return true;
			} else {
				
				$this->setOption('errorNo',-3);
				return false;
			}
			
		}
		
		
		protected function setFiles($name,$size,$type,$tmpName,$error) {
			//1 2 3 4 6 7  0(正常)
			if ($error) {
				$this->setOption('errorNo',$error);
				return false;
			}
			
			$this->orgname = $name;
			$this->size = $size;
			$this->tmpfile = $tmpName;
			$this->mime = $type;
			
			//后缀没处理
			$info = pathinfo($name);
			$this->subfix = $info['extension'];
			
			return true;
			/*
			$arr = explode('.',$name);
			$this->subfix = array_pop($arr);
			
			
			$arr = explode('.',$name);
			$this->subfix = $arr[count($arr)-1];
			
			$pos = strrpos($name,'.');
			echo substr($name,$pos + 1);
			*/
		}
		
		protected function checkPath()
		{
			//检测路径是否是目录，如果不存在创建
			if (!is_dir($this->path)) {
				return mkdir($this->path,0755,true);
			}
			//检测路径是否可写，如果不写写更改权限
			if (!is_writeable($this->path) ||  !is_readable($this->path)) {
				return chmod($this->path,0755);
			}
			return true;
		}
		
		protected function getErrorInfo()
		{
			switch ($this->errorno) {
				case 1:
					$str = '上传的文件超过了 php.ini 中 upload_max_filesize 选项限制的值';
					break;
				case 2:
					$str = '上传文件的大小超过了 HTML 表单中 MAX_FILE_SIZE 选项指定的值';
					break;
				case 3:
					$str = '部份件被上传';
					break;
				case 4:
					$str = '没有文件被上传';
					break;
				case 6:
					$str = '找不到临时文件夹';
					break;
				case 7:
					$str = '临时文件写入失败';
					break;
				case -1:
					$str = '自定义的上传路径不存在';
					break;
				case -2:
					$str = '没有权限';
					break;
				case -3:
				case -4:
					$str = '类型或后缀不准许';
					break;
				case -5:
					$str = '超过了自定义的大小';
					break;
				case -6:
					$str = '不是上传文件';
					break;
				case -7:
					$str = '移动上传文件失败';
					break;
				
			}
			return $str;
		}
		
		public function __get($key)
		{
			if (in_array($key, ['newpath','newname','errorno','size'])) {
				var_dump($key);
				return $this->$key;
			} else if ($key == 'errorinfo') {
				return $this->getErrorInfo();
			}
		}
	}
```
6、Verify类
```
	<?php
	namespace Framework;


	class Verify
	{
		//宽
		protected $width;
		//高
		protected $height;
		//图片类型
		protected $imgType;
		//文字类型
		protected $codeType;
		//文字的个数
		protected $num;
		//保存的验证码字符串
		protected $verifyCode;
		//保存验证码资源的一个成员属性
		protected $res;
		
		
		//初始化成员
		//上面这些参数
		public function __construct($width = 100, $height = 50, $imgType = 'png', $codeType = 3, $num = 4)
		{
			$this->width = $width;
			$this->height = $height;
			$this->imgType = $imgType;
			$this->codeType = $codeType;
			$this->num = $num;
			$this->verifyCode = $this->createVerifyCode();
			
		}
		
		protected function  createVerifyCode()
		{
			$string = '';
			
			switch ($this->codeType) {
				case 1:
					$string = implode('',array_rand(range(0,9),$this->num));
					break;
				case 2:
					$string = join('',array_rand(array_flip(range('a','z')),4));
					break;
				case 3:
					/*
					for ($i = 0; $i < $this->num; $i++) {
						$r= mt_rand(0,2);
						switch ($r) {
							case 0:
								$ascii = mt_rand(48,57);
								break;
							case 1:
								$ascii = mt_rand(65,90);
								break;
							case 2:
								$ascii = mt_rand(97,122);
								break;
						}
						$string .= chr($ascii);
						
					}
					*/
					$str = 'abcdefghijkmnpqrstuvwxzABCDEFGHJKLMNPQRSTUVWXYZ23456789';
					$string = substr(str_shuffle($str),0,$this->num);
					break;
			}
			
			return $string;
		}
		
		//调验证码显示的一个方法 output
		//1.画图
		//2.分配颜色(写两个成员方法，调的时候直接调对应的成员方法即可)
		//3.背景填充
		//4.画干扰点
		//5.画干扰线
		//6.写字 
		//7. 输出类型
		//8. 输出图片
		public function outImg()
		{
			$this->createImg();
			$this->fillBgColor();
			$this->fillPix();
			$this->fillArc();
			$this->writeFont();
			$this->output();
		}
		
		protected function output()
		{
			//imagepng
			$func = 'image'.$this->imgType;
			$mime = 'Content-type:image/'.$this->imgType;
			header($mime);
			$func($this->res);
		}
		
		protected function writeFont()
		{
			for ($i = 0; $i < $this->num; $i++) {
				
				$width = ceil($this->width / $this->num);
				$x = $width * $i;
				$y= mt_rand(5,$this->height - 10);
				$c = $this->verifyCode[$i];
				imagechar($this->res,5,$x,$y,$c,$this->darkColor());
			}
			
		}
		
		protected function fillArc()
		{
			for($i = 0; $i < 10; $i++) {
				imagearc($this->res,
						mt_rand(10,$this->width - 10),
						mt_rand(10,$this->height - 10),
						mt_rand(0,$this->width),
						mt_rand(0,$this->height),
						mt_rand(0,180),
						mt_rand(181,360),
						$this->lightColor()
						);
			}
		}
		
		protected function fillPix()
		{
			$num = $this->pixNum();
			for ($i = 0; $i < $num; $i++) {
				
				imagesetpixel($this->res,mt_rand(0,$this->width),mt_rand(0,$this->height),$this->darkColor());
			}
		}
		
		protected function pixNum()
		{
			$area = ceil(($this->width * $this->height) / 20);
			return $area;
		}
		
		
		protected function fillBgColor()
		{
			imagefill($this->res,0,0,$this->lightColor());
		}
		
		
		protected function lightColor()
		{
			return imagecolorallocate($this->res,
							   mt_rand(130,255),
							   mt_rand(130,255),
							   mt_rand(130,255)
								);
		}
		
		protected function darkColor()
		{
			return imagecolorallocate($this->res,
							   mt_rand(0,120),
							   mt_rand(0,120),
							   mt_rand(0,120)
								);
		}
		
		protected function createImg()
		{
			$this->res = imagecreatetruecolor($this->width,$this->height);
		}
		
		//9 .销毁图片资源
		public function __destruct()
		{
			//imagedestroy($this->res);
		}
		
		//可以做一个魔术方法__get专门用于得到验证码字符串
		public function __get($key)
		{
			if ($key == 'verifyCode') {
				return $this->$key;
			}
			
			return false;
		}
	}
```
7、auto_load(类的自动加载)
```
<?php
	class Init
	{
		
		public function loader()
		{
			$psr = new Psr4();
			$psr->register();
			
			foreach ($GLOBALS['nameSpace'] as $name => $baseDir) {
				$psr->addNamespace($name,$baseDir);
			}
		}
		
		public function router()
		{
			$this->loader();
			
			$_GET['m'] = isset($_GET['m']) ? ucfirst(strtolower($_GET['m'])) : 'Index';
			$_GET['a'] = isset($_GET['a']) ? $_GET['a'] :'index';
			
			$controllerName = 'Controller\\'.$_GET['m'].'Controller';
			
			
			$mod = new $controllerName();
			
			if (method_exists($mod,$_GET['a'])) {
				
				call_user_func(array($mod,$_GET['a']));
			} else{
				exit('方法不合法');
				
			}
		}
	}
```
8、PSR_4
```
	<?php
	class Psr4
	{
		
		private $prefixes;
		
		
		public function register()
		{
			spl_autoload_register(array($this,'loadClass'));
		}
		
		public function addNamespace($nameSpace, $path)
		{
			$nameSpace = trim($nameSpace,'\\');
			$path = rtrim($path,DIRECTORY_SEPARATOR).'/';
			
			if (isset($this->prefixes[$nameSpace]) == false) {
				$this->prefixes[$nameSpace] = array();
			}
			
			array_push($this->prefixes[$nameSpace],$path);
			
		}
		
		private function loadClass($className)
		{
			$pos = strrpos($className,'\\');
			//µÃµ½ÃüÃû¿Õ¼ä
			$nameSpace = substr($className,0, $pos);
			$realClass = substr($className,$pos + 1);
			
			
			//´¦ÀíÓ³Éä°üº¬
			$this->mappingFile($nameSpace, $realClass);
		}
		
		private function mappingFile($nameSpace, $realClass)
		{
			if (isset($this->prefixes[$nameSpace]) == false) {
				return false;
			}
			
			foreach ($this->prefixes[$nameSpace] as $baseDir) {
				$file = $baseDir . str_replace('\\','/',$realClass).'.php';
				
				$this->requireFile($file);
			}
			
		}
		
		private function requireFile($file)
		{
			if (file_exists($file)) {
				include $file;
			}
			
		}
		
	}
```
9、NameSpace
```
	<?php
	return [
		'Model' => 'App/Model/',
		'Controller' => 'App/Controller/',
		'Framework' => 'Vendor/Phone/Framework/Src/',
	];
```