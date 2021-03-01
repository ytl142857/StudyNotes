
# 换用Markdown编辑器

参考:
+ [django-mdeditor的github](https://github.com/pylixm/django-mdeditor/blob/master/README_CN.md)

## 安装所需包

`pip install django-mdeditor`

> 报错 UnicodeDecodeError: 'gbk' codec can't decode byte 0xad in position 167: illegal multibyte sequence
> 最新版本中有冲突  
> 换用 `pip install django-mdeditor==0.1.16`  

`pip install markdown` 

## Django配置  

### 配置`settings.py`  
+ `INSTALLED_APPS` 里添加 `mdeditor`
```python
INSTALLED_APPS = [
        ...
        'mdeditor',
    ]
```
+ 添加媒体文件路径配置  
```python
MEDIA_ROOT = os.path.join(BASE_DIR, 'uploads')
MEDIA_URL = '/media/'
```

### 配置`urls.py` 
```python
urlpatterns = [
    ...
    path('mdeditor'), include('mdeditor.urls'), 
]
```

## 修改`models`
`from mdeditor import MDTextField`  
`...`
`content = MDTextField()`



## 使用Latex公式

$$
\begin{matrix}
	1 & x & x^2\\
	1 & y & y^2\\
	1 & z & z^2\\
	\end{matrix}
$$

## 修改`views`
```python
blog.content = markdown.markdown(blog.content)
```

## 执行数据库迁移
`python manage.py makemigrations`  
`python manage.py migrate`