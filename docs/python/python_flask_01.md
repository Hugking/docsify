

*应用说明：基于 python flask 开发网上商城详细步骤*

##  准备环境

1. 使用 python3 创建虚拟环境

```bash
python3 -m venv py3venv #创建一个py3venv的虚拟环境

```

2. 激活虚拟环境

```bash
source ./py3venv/bin/activate
```

3. 创建项目目录

```bash
mkdir shop
```

4. 为项目 shop 安装依赖

```bash
cd shop
```

5. 安装和卸载包

```python
pip install flask # 安装包
pip uinstall flask # 卸载包
```

6. 退出虚拟环境

```python
deactivate: 退出虚拟环境
```

7. 初始化环境安装依赖

```python
pip freeze >requirements.txt #生成全部依赖 requirements.txt用来记录项目所有的依赖包和版本号，只需要一个简单的pip命令就能完成。
pip install -r requirements.txt #安装全部依赖　
```

`requirements.txt`

```python
flask
flask-sqlalchemy
flask-debugtoolbar
mysqlclient
flask_script
requests
uwsgi #Linux部署用，window会无法安装
```

`环境（application.py）`

```bash
export ops_config=base #基本环境
```

```bash
export ops_config=local #本地环境
```

```bash
export ops_config=production #生产环境
```

# mysql 踩坑

### **用 vscode 连接 mysql 时出现报错**

```Error: ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; consider upgrading MySQL client```

### **_node 使用 mysql 报错_**

原因：登录数据库的客户端跟 mysql8.0 不兼容了，mysql8.0 密码认证采用了新的密码格式

解决办法：在系统 mysql 终端输入下面命令

```mysql
//password 是你的数据库账户密码，root和host也是
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

# 搭建项目目录

1. 创建项目根目录 shop

```bash
mkdir shop
```

2. 在 shop 目录下创建包 config 用来管理配置文件

通用配置文件`config/base_setting.py`

```python
# -- coding: utf-8 --
SERVER_PORT = 5000
DEBUG = False
SQLALCHEMY_ECHO = False
```

3. 本地配置文件

`config/local_setting.py`

```python
# -- coding: utf-8 --
DEBUG = True
SQLALCHEMY_ECHO = True
SQLALCHEMY_DATABASE_URI = 'mysql://root:mysql@127.0.0.1/mysql?charset=utf8mb4'
SQLALCHEMY_TRACK_MODIFICATIONS = False
SQLALCHEMY_ENCODING = "utf8mb4"
```

4. 生产配置文件

`config/production_setting.py`

```python
# -- coding: utf-8 --
```

5. 在 shop 目录下创建 application.py 封装 flask 的全局变量

`application.py`

```python
# -- coding: utf-8 --
from flask import Flask
from flask_script import Manager
from flask_sqlalchemy import SQLAlchemy
import os


class Application( Flask ):
    def __init__(self,import_name):
        super( Application,self ).__init__( import_name )
        self.config.from_pyfile( 'config/base_setting.py' )
        if "ops_config" in os.environ:
            self.config.from_pyfile( 'config/%s_setting.py'%os.environ['ops_config'] )

        db.init_app( self )

db = SQLAlchemy()
app = Application( __name__ )
manager = Manager( app )
```

6. 在 shop 目录下创建 www.py 封装 HTTP 相关初始化

`www.py`

```python
# -- coding: utf-8 --
from application import app
from web.controllers.index import route_index

app.register_blueprint( route_index,url_prefix = "/" )
```

7. 在 shop 目录下创建 manager.py 启动入口

`manager.py`

```python
# -- coding: utf-8 --
from application import app,manager
from flask_script import Server
import www

##web server
manager.add_command( "runserver", Server( host='0.0.0.0',port=app.config['SERVER_PORT'],use_debugger = True ,use_reloader = True) )

def main():
    manager.run()

if __name__ == '__main__':
    try:
        import sys
        sys.exit( main() )
    except Exception as e:
        import traceback
        traceback.print_exc()
```

8. 在 shop 目录下创建 readme.md

`readme.md`

```python
Python Flask 商城系统
=====================
##启动
* export ops_config=local|production && python manage.py runserver

##flask-sqlacodegen

    flask-sqlacodegen 'mysql://root:password@127.0.0.1/shop_db' --outfile "common/models/model.py"  --flask
    flask-sqlacodegen 'mysql://root:password@127.0.0.1/shop_db' --tables user --outfile "common/models/user.py"  --flask

## 所见即所得编辑器ueditor

    <script src="{{ buildStaticUrl('/plugins/ueditor/ueditor.config.js') }}"></script>
    <script src="{{ buildStaticUrl('/plugins/ueditor/ueditor.all.min.js') }}"></script>
    <script src="{{ buildStaticUrl('/plugins/ueditor/lang/zh-cn/zh-cn.js') }}"></script>


    UE.getEditor('editor',{
       toolbars: [
            [ 'undo', 'redo', '|',
                'bold', 'italic', 'underline', 'strikethrough', 'removeformat', 'formatmatch', 'autotypeset', 'blockquote', 'pasteplain', '|', 'forecolor', 'backcolor', 'insertshopedlist', 'insertunshopedlist', 'selectall',  '|','rowspacingtop', 'rowspacingbottom', 'lineheight'],
            [ 'customstyle', 'paragraph', 'fontfamily', 'fontsize', '|',
                'directionalityltr', 'directionalityrtl', 'indent', '|',
                'justifyleft', 'justifycenter', 'justifyright', 'justifyjustify', '|', 'touppercase', 'tolowercase', '|',
                'link', 'unlink'],
            [ 'imagenone', 'imageleft', 'imageright', 'imagecenter', '|',
                'insertimage', 'insertvideo', '|',
                'horizontal', 'spechars','|','inserttable', 'deletetable', 'insertparagraphbeforetable', 'insertrow', 'deleterow', 'insertcol', 'deletecol', 'mergecells', 'mergeright', 'mergedown', 'splittocells', 'splittorows', 'splittocols' ]

        ],
        enableAutoSave:true,
        saveInterval:60000,
        elementPathEnabled:false,
        zIndex:4,
        serverUrl:common_ops.buildUrl(  '/upload/ueditor' )
    });

##可参考资料
* [python-Flask（jinja2）语法：过滤器](https://www.jianshu.com/p/3127ac233518)
* [SQLAlchemy 各种查询语句写法](https://wxnacy.com/2017/08/14/python-2017-08-14-sqlalchemy-filter/)
```

9. 在 shop 目录下创建 requirements.txt

`requirements.txt`

```python
flask
flask-sqlalchemy
flask-debugtoolbar
mysqlclient
flask_script
requests
```

10. 在 shop 目录下创建 common 包 存放公用部分

`common/__init__.py`

```python
# -- coding: utf-8 --
```

`common/libs/__init__.py`

```python
# -- coding: utf-8 --
```

`common/models/__init__.py`

```python
# -- coding: utf-8 --
```

11. 在 shop 目录下创建 doc 目录 存放存放文档

`doc/mysql.md`

```markdown
数据库变更记录
==================
```

12. 在 shop 目录下创建 jobs 包 用来存放一些定时的任务

`jobs/__init__.py_`

```python
# -- coding: utf-8 --
```

`jobs/task/__init__.py_`

```py
# -- coding: utf-8 --
```

13. 在 shop 目录下创建 web 包用来存放蓝图

`web/__init__.py`

```py
# -- coding: utf-8 --
```

`web/controllers/index.py`

```py
from flask import Blueprint

route_index = Blueprint( 'index_page',__name__ )

@route_index.route("/")
def index():
    return 'hello'
```

`web/controllers/__init__.py`

```py
# -- coding: utf-8 --
```

# 简单测试

1. 在终端添加环境变量

```bash
export ops_config=local　　
```

2. 启动项目

```bash
python manage.py runserver
```

3. 输入测试 url

> ```
> http://127.0.0.1:5000/
> ```
>