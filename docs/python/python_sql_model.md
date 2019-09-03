## 1、安装工具
```bash
PS E:\Flask> pip install flask-sqlacodegen
Collecting flask-sqlacodegen
  Downloading https://files.pythonhosted.org/packages/3b/2a/e47611e4fec19e33af5fc90dd57ec2b064056f6c433804742d66e80b2f57/flask_sqlacodegen-1.1.6.1-py2.py3-none-any.whl
Collecting inflect>=0.2.0 (from flask-sqlacodegen)
  Downloading https://files.pythonhosted.org/packages/86/02/e6b11020a9c37d25b4767a1d0af5835629f6e75d6f51553ad07a4c73dc31/inflect-2.1.0-py2.py3-none-any.whl (40kB)
     |████████████████████████████████| 51kB 21kB/s
Requirement already satisfied: SQLAlchemy>=0.6.0 in e:\-avpfbmcx\lib\site-packages (from flask-sqlacodegen) (1.2.11)
Installing collected packages: inflect, flask-sqlacodegen
Successfully installed flask-sqlacodegen-1.1.6.1 inflect-2.1.0
```
## 2、生成model.py文件(这里我只生成两个表的模型)
```bash
flask-sqlacodegen 'mysql+cymysql://root:password@127.0.0.1/test' --tables goods,goods_attribute --outfile "test.py"  --flask
```
## 3、查看文件
```bash
$ cat test.py
```
```bash
# coding: utf-8
from sqlalchemy import Column, DateTime, Integer, Numeric, String, Text
from flask_sqlalchemy import SQLAlchemy


db = SQLAlchemy()


class Good(db.Model):
    __tablename__ = 'goods'

    create_time = db.Column(db.DateTime)
    update_time = db.Column(db.DateTime)
    delete_time = db.Column(db.DateTime)
    id = db.Column(db.Integer, primary_key=True)
    category_id = db.Column(db.Integer, nullable=False, index=True)
    goods_sn = db.Column(db.String(255), nullable=False, index=True)
    name = db.Column(db.String(255), nullable=False)
    brand_id = db.Column(db.Integer, nullable=False, index=True)
    goods_num = db.Column(db.Integer, nullable=False, index=True)
    keywords = db.Column(db.String(255), nullable=False)
    goods_brief = db.Column(db.String(255), nullable=False)
    goods_desc = db.Column(db.Text)
    is_on_sale = db.Column(db.Integer, nullable=False)
    sort_order = db.Column(db.Integer, nullable=False, index=True)
    is_delete = db.Column(db.Integer, nullable=False)
    attribute_category = db.Column(db.Integer, nullable=False, index=True)
    counter_price = db.Column(db.Numeric(10, 2), nullable=False)
    extra_price = db.Column(db.Numeric(10, 2), nullable=False)
    is_new = db.Column(db.Integer, nullable=False)
    goods_unit = db.Column(db.String(255), nullable=False)
    primary_pic_url = db.Column(db.String(255), nullable=False)
    list_pic_url = db.Column(db.String(255), nullable=False)
    retail_price = db.Column(db.Numeric(10, 2), nullable=False)
    sell_volume = db.Column(db.Integer, nullable=False)
    primary_product_id = db.Column(db.Integer, nullable=False)
    unit_price = db.Column(db.Numeric(10, 2), nullable=False)
    promotion_desc = db.Column(db.String(255), nullable=False)
    promotion_tag = db.Column(db.String(255), nullable=False)
    app_exclusive_price = db.Column(db.Numeric(10, 2), nullable=False)
    is_app_exclusive = db.Column(db.Integer, nullable=False)
    is_limited = db.Column(db.Integer, nullable=False)
    is_hot = db.Column(db.Integer, nullable=False)


class GoodsAttribute(db.Model):
    __tablename__ = 'goods_attribute'

    create_time = db.Column(db.DateTime)
    update_time = db.Column(db.DateTime)
    delete_time = db.Column(db.DateTime)
    id = db.Column(db.Integer, primary_key=True)
    goods_id = db.Column(db.Integer, nullable=False, index=True)
    attribute_id = db.Column(db.Integer, nullable=False, index=True)
    values = db.Column(db.Text)
```
### 大功告成！