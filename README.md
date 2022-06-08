##中间件组件

middleware组件用于PHP框架中间件管理

###安装组件

使用 composer 命令进行安装或下载源代码使用(依赖willphp/config组件)。

    composer require willphp/middleware

>WillPHP框架已经内置此组件，无需再安装。

###调用示例

    \willphp\middleware\Middleware::globals(); //执行全局中间件

###配置中间件

`config/middleware.php`配置文件设置如下：
	
	//全局中间件	
	'global' => [			
		\middleware\Boot::class,
	],
	//应用中间件	
	'web' => [
		'database_query' => [
			\middleware\web\Dbquery::class,
		],
		'database_execute' => [
			\middleware\web\Dbexecute::class,
		],		
	],
	//控制器中间件	
	'controller' => [
		'check' => [
			\middleware\controller\Check::class,					
		],
		'auth' => [
			\middleware\controller\Auth::class,
		],	
	],
	//获取当前控制器方法的方法
	'get_action' => '\willphp\route\Route::getAction', 
	

###创建中间件

如在对应命名空间的文件目录中创建Auth.php中间件：

	namespace middleware\controller;
	class Auth {
		//执行中间件
		public function run($next, $params = []){
			echo "Auth中间件执行了";
	        $next();
		}
	}

###应用中间件

执行应用中间件

    Middleware::web('database_query', $sql);  //第二个参数为转入中间件参数

添加应用中间件

    Middleware::add('test', [\middleware\web\Test::class,]);  //第二个参数为数组

###控制器中间件

在控制器的 __construct 方法或之前执行以下代码都可触发控制器中间件运行：

    Middleware::set('check');  

只允许指定方法运行中间件：

    Middleware::set('auth',  ['only' => ['test']]);

除了某个方法外其他方法执行中间件

    Middleware::set('auth', ['except' => ['test']]);

示例：

	namespace app\home\controller;
	use willphp\middleware\Middleware;	
	class IndexController{
	    public function __construct(){
	        //中间件只在test方法中执行
	        Middleware::set('auth',  ['only' => ['test']]);
	    }
	    public function index(){
	        echo '不执行auth';
	    }
	    public function test(){
	        echo '执行auth';
	    }
	}
